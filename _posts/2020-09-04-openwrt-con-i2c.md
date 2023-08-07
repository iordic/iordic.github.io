---
title:  "Usando el GPIO de un router con OpenWrt (+ i2c)"
description: "Post sobre sintaxis del lenguaje Smali"
date:   2020-09-04 23:30
categories: openwrt gpio i2c
---

Si estás familizariado con el uso de las Raspberry Pi más allá de instalar programas o
servicios, conocerás los pines GPIO que se pueden usar para interactuar con componentes
electrónicos, sino ya queda dicho ;D. GPIO son las siglas de General Purpose Input/Output,
es decir, son pines que se pueden usar para enviar o recibir señales eléctricas
(generalmente digitales) sin  un propósito específico, para que juegues tú con ellos ;).

Pues bien, si lo estabas pensando, los GPIO no son algo exclusivo de Raspberry, la mayoría
de [SoC](https://es.wikipedia.org/wiki/System_on_a_chip) los llevan para que los 
desarrolladores puedan adaptar ese sistema a sus necesidades específicas, incluso los
routers. Por ejemplo, se necesita encender los leds específicos de un router para indicar
que está encendido, el led de actividad de wifi, etc...

La cosa es que la mayoría de las veces algunos de esos GPIO del chip quedan sin uso, por
lo que podemos usarlos para lo que queramos. Por ejemplo, qué se yo, encender un led
cuándo se produzca cierto evento, mostrar valores en una pantalla o darle de comer a tu
gato desde Lisboa.

Voy a explicar cómo usar esos pines libres en OpenWrt, intentando generalizar, pero en mi
caso usando un router Huawei HG556a (versión C).

El funcionamiento es simple, al disponer de nuestro queridísimo sistema GNU/Linux ♥ y su
estándar, activar o desactivar un GPIO será tan fácil como leer o escribir en un fichero y
que se apañe él :). Sólo tenemos que saber dónde.

## Primeros pasos
Cómo los routers no suelen ser hardware de código abierto ni documentado, lo primera que
necesitas es que alguien haya hecho ingeniería inversa a tu modelo concreto, haya
documentado en la página de OpenWrt todo el proceso para grabar la imagen y la ubicación
donde acceder a los GPIO libres, sino estás jodid@ :D (a no ser que sepas hacerlo tú, en
ese caso no creo que estuvieses por aquí jaja).
En mi caso [aquí](https://openwrt.org/toh/huawei/hg556a). Importante que leas TODA la
página, toda información es útil.

Lo primero es seguir los pasos para grabar el firmware en tu router y tener openwrt ya
instalado. Una vez lo tengas y sin acceder aún al hardware, puedes jugar un rato con los
[leds](https://openwrt.org/docs/guide-user/base-system/led_configuration) y ver cómo
puedes encenderlos y apagándolos.

Materiales necesarios:

* Soldador de punta fina
* Estaño
* Cablecillos finos
* Voltímetro
* Pistola de pegamento caliente (recomendado)

Conocimientos necesarios:

* Nociones básicas de electrónica
* Soldadura
* Manejo de SSH y la terminal de Linux
* C y gcc (para la parte extra)

## 1. Localizando el acceso los GPIO y "pinchándolos"
Buscamos por la página una sección o tabla que ponga GPIO. Si todo va bien habrá una
correspondencia entre el número de GPIO del SoC y dónde "pincharlo", es decir, dónde
tenemos un acceso en la placa a ese contacto, suelen ser contactos para resistencias que
el fabricante no ha soldado y cosas así, en otros casos pueden ser cosas complicadas como
tener que soldar directamente a los contactos del chip (toca madera para que no sea así
xD). Presta atención a las explicaciones, algunos gpio aunque vengan en la tabla, te dicen
que están en uso o que hay que recompilar el kernel, y la verdad que no es algo que te
recomiende jajaj.

Al lío, en mi caso concreto, viene una tabla así:

| location        | GPIO (C) |
|-----------------|:--------:|
| R446/R440       |     4    |
| R476/R441       |     5    |
| U400            |     6    |
| R450            |     7    |
| R906/R923/U900  |    25    |
| R415            |    30    |
| Le88266DLC/R433 |    32    |
| R429/R939       |    34    |
| R946/R430       |    37    |

También pone que para usar los pines 4 y 5 hay que parchear el kernel, pasando del tema.
Ahora hay dos opciones, el que lo ha documentado ha sido majete y nos ha dibujado sobre la
foto dónde encontrar estos contactos o no. En caso de que no te toca volverte loco
buscándolo por la placa (mi caso fue así :(, estaban documentados el modelo A y B y el mío
no). En cualquiera de los dos casos toca... ¡desmontar el bicho!

¿Y una vez localizados a soldar y ya? No nos precipitemos, no quieres tener que soldar y
desoldar varias veces, creéme. Primero unos briconsejos:

1. Intenta encontrar siempre las localizaciones a huecos de resistencias vacías (Rxxx).
2. Las resistencias tienen dos contactos, no es lo mismo soldar en uno que en otro, y
desde luego tampoco en ambos a la vez (suele fallar si lo haces). Para asegurarte es hora
de usar el voltímetro, seguramente el contacto que tenga acceso al GPIO al no estar en uso
tendrá un valor alto (3.3v). Lo más recomendable es que pruebes a activarlo y desactivarlo, 
midiendo con el voltímetro para ver si cambia y asegurarte, esa explicación viene luego
(te lo recomiendo, a mi me toco desoldar por no hacerlo, ve al apartado siguiente).
3. Las soldaduras que harás serán bastante frágiles, la pistola de pegamento es para fijar
el cable a la placa y que el cable no de tirones sin querer, así nos aseguramos de que no
se parte la soldadura.
4. Si vas a querer hacer lo de i2c que viene luego: necesitas almenos 2 GPIO y sacar
también un cable a Vcc (3.3v) y otro a GND (tierra, masa, etc...). Estos dos últimos los
saque de los pines que vienen para el puerto UART, así estoy seguro de que son los buenos.
5. En mi caso al querer luego usarlos con i2c, los he soldado a una "placa de topos" de
estas con pin header hembra y la disposición de pines para que encaje con los módulos i2c 
que tenía por aquí. Suelen ser iguales en disposición, menos SDA y SCL, pero como lo vamos
a simular, siempre se puede cambiar por software :).

Con todo esto: punta de soldador fina, cuidado con la temperatura, no te pases con el
estaño y mucha suerte. Foto de mi resultado:

![soldaduras en placa]({{site.url}}/assets/openwrt_i2c/soldaduras.jpg "Soldaduras en la placa")

![Puerto externo]({{site.url}}/assets/openwrt_i2c/puerto.jpg "Puerto improvisado")

## 2. Probando que funcionan los pines
Si has sido list@ habrás venido aquí antes de soldar :). Aquí está la
[documentación](https://openwrt.org/docs/techref/hardware/port.gpio) sobre los gpio, para
probar si funcionan tienes que acceder a los archivos del sistema que los controlan, ve al
apartado de "software" dónde te explica cómo se habilitan los pines y se cambia de valor.

Como nos explica la documentación, tenemos que obtener el número "base" desde dónde
empiezan a contarse los GPIO, vamos a la ruta "/sys/class/gpio" y dentro habrá una o
varias carpetas. En mi caso había dos: "gpiochip472" y "gpiochip480". Si ejecutamos en
lugar de lo que nos indica: "cat /sus/class/gpio/gpiochip*/base" nos saldrán todos los
número base. En mi caso 472 y 480, y a diferencia de lo que pone en la documentación en mi
caso funciona a partir del segundo (480), igual tiene que ver con que lo modifique para
que usara el segundo núcleo de procesador siguiendo las instrucciones.

En mi caso los gpio que saque son los respectivos a la tabla, que estaban ubicados en R906
y R450, es decir: los gpio 25 y 7 respectivamente. Por lo tanto, los que queremos activar
son 505 y 487 (base + gpio en la tabla).

Siguiendo la documentación los activamos cómo salidas:

```
echo "487" > /sys/class/gpio/export
echo "505" > /sys/class/gpio/export

echo "out" > /sys/class/gpio/gpio487/direction
echo "out" > /sys/class/gpio/gpio505/direction
```

Y ya podemos probar a activar y desactivar (1 - activar, 0 - desactivar):

```
echo "1" > /sys/class/gpio/gpio487/value
echo "1" > /sys/class/gpio/gpio505/value


echo "0" > /sys/class/gpio/gpio487/value
echo "0" > /sys/class/gpio/gpio505/value
```

Podemos medir con el voltímetro que cambia entre 0v y 3.3v. Aquí la demostración:

<video width="640" height="360" controls>
    <source src="{{site.url}}/assets//openwrt_i2c/demo_gpio.mp4" type="video/mp4">
    Your browser does not support the video tag.
</video>

Te sirve *echo* igual que te sirve cualquier cosa, al final es escribir o leer en esos
ficheros, puedes por ejemplo en C abrir el fichero, escribir un 1 para activar y comprobar
si ha escrito bien en el fichero. Ten en cuenta que hay que tener los permisos adecuados
para poder acceder al fichero.

¡Hecho! Ya puedes usar los gpio accediendo a los ficheros, eso sí, al reiniciar el router
se perderá la configuración y tendrás que volver a hacer todo el proceso.

## 3. Preparando GPIO para i2c
Si has hecho cosillas de Arduino es posible que alguna vez te haya tocado usar algún
módulo que use el protocolo i2c para comunicar la información a nuestra placa. El SoC del 
router no tiene i2c. es verdad pero, ¿a quién le importa? Tenemos linux y aquí hemos
venido a hacer chapuzas :D. La verdad es que openwrt dispone de unos módulos para el
kernel de linux que permiten, entre otras cosas, usar los gpio para comunicarse de
diferentes formas mediante técnicas de
[bit banging](https://en.wikipedia.org/wiki/Bit_banging).

Viene bien explicado [aquí](https://openwrt.org/docs/techref/hardware/port.i2c) en el
apartado de *I2C over GPIO*. Los pasos concretos en mi caso son:

```
opkg update
opkg install kmod-i2c-gpio-custom kmod-i2c-core

insmod i2c-dev
insmod i2c-gpio-custom bus0=0,487,505
```

> Nota: busX=X,Y,Z -> X: número de puerto, Y: gpio para sda, Z: gpio para sdl.

Al igual que en el caso básico de antes, la configuración hecha con *insmod* se perderá al
reiniciar el dispositivo. Después de esto, si ha funcionado, puedes instalar i2c-tools
también con `opkg install i2c-tools` o la interfaz web. Conectarle un módulo con la 
disposición de pines correcta y y ejecutar `i2cdetect -y 0` (0 es el número del bus) para
buscar la dirección del módulo, si te sale es que funciona correctamente.
Yo usé un módulo con LM75 que tenía por casa, es un sensor de temperatura:

![LM75]({{site.url}}/assets/openwrt_i2c/modulo_conectado.jpg "Módulo LM75")

![i2cdetect]({{site.url}}/assets/openwrt_i2c/i2cdetect.png "Ejecución de i2cdetect")

## Extra: aprovecho el post para explicar como compilar de forma cruzada en C
Si necesitas ejecutar un programa específico en el router, debes saber que al tratarse de
una arquitectura distinta, no puedes simplemente ejecutarlo en el router y ya. Tienes que
compilarlo para esa arquitectura particular, si usas gcc en tu ordenador personal generas
un binario para esa arquitectura. Por lo que tenemos que hacer uso de una técnica llamada
*compilación cruzada* que consiste en compilar un programa desde una arquitectura a otra
distinta. Sin entrar en detalles pero generalmente es un dolor de muelas, porque suele dar
mil problemas, la suerte es que OpenWrt lo tiene todo montado para que tengas lo necesario
y asegurarte de que va a funcionar.

Para poder usar estas herramientas (o como suelen llamarlo, *toolchain*) tienes que
descargarte el código de OpenWrt, seleccionar tu dispositivo en el menú de configuración
y, si no me equivoco, 
[compilar el kernel](https://openwrt.org/docs/guide-developer/quickstart-build-images) 
para que te genere todo lo necesario (tarda bastante, un par de horas o así, según la
cpu). Cuando acabe tendrás las toolchain específicas para tu dispositivo dentro de la
carpeta *staging_dir*, si hay varias una de ellas tendrá la carpeta *bin* con el
compilador y el resto de programas (busca un nombre largo acabado en gcc).

> Recomendable saltar a la rama de git que sea del kernel que estás ejecutando en tu router.

En mi caso, las herramientas están en
"/home/jordi/Descargas/openwrt/staging_dir/toolchain-mips_mips32_gcc-7.3.0_musl/bin". Para
poder usarlas en cualquier sitio las añadimos al path (se pierde al reiniciar):

```
export PATH=$PATH:/home/jordi/Descargas/openwrt/staging_dir/toolchain-mips_mips32_gcc-7.3.0_musl/bin
export STAGING_DIR=/home/jordi/Descargas/openwrt/staging_dir/toolchain-mips_mips32_gcc-7.3.0_musl
```
Lo de STAGING_DIR si no está declarado no funciona :(. Esto viene explicado 
[aquí](https://openwrt.org/docs/guide-developer/crosscompile) pero esun poco lioso. Con 
eso ya podrías compilar un fichero de C con el nombre del programa que venga dentro del
directorio (en mi caso *mips-openwrt-linux-gcc*).

Si quieres algo más complejo y hacer uso de make, tienes que usarlo especificando por
parámetro que el compilador y linker sean esos, y no los del sistema, en mi caso usando:
`make CC=mips-openwrt-linux-gcc LD=mips-openwrt-linux-ld`

Continuando con i2c, creamos un código en C para comunicar con el módulo, lo he hecho
copiando y pegando de varios sitios y funciona medio mal pero bueno, aquí esta:

```c
#include <linux/i2c-dev.h>
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <sys/ioctl.h>
#include <string.h>

#define LM75_TEMP_REGISTER 0

int main(int args, char *argv[]) {
    int file;
    int adapter_nr = 0; // El número de adaptador, se puede hardcodear o pasar por parámetro

    char filename[20];
    snprintf(filename, 19, "/dev/i2c-%d", adapter_nr);

    file = open(filename, O_RDWR);

    if (file < 0) {
      /* ERROR HANDLING; you can check errno to see what went wrong */
      exit(1);
    }

    int addr = 0x4f; /* The I2C address */

    if (ioctl(file, I2C_SLAVE, addr) < 0) {
      /* ERROR HANDLING; you can check errno to see what went wrong */
      exit(1);
    }

    __u8 reg = LM75_TEMP_REGISTER; /* Device register to access */
    char buf[2];    // No hará falta más para la prueba

    memset(buf, 0, sizeof(buf));

    /* Daba error al compilar con las librerías de smbus, así que usamos
     * las funciones estándar y ya...
     */
    if (write(file, LM75_TEMP_REGISTER, 1) != 1) {
      /* ERROR HANDLING: I2C transaction failed */
    }

    /* Using I2C Read, equivalent of i2c_smbus_read_byte(file) */
    if (read(file, buf, 2) != 2) {
      /* ERROR HANDLING: I2C transaction failed */
        printf("ERROR: i2c transaction failed.\n"); // por poner algo...
    } else {
        // Calculate temperature value in Celcius
        int16_t i16 = (buf[0] << 8) | buf[1];
        printf("Raw: %d\n", i16);
        // Read data as twos complement integer so sign is correct
        float temp = i16 / 256.0;
        printf("Temperature: %.2f ºC\r\n",temp);
    }
    close(file);
    return 0;
```

Aquí una captura del proceso de compilado y envío del binario al router:

![Cross compile]({{site.url}}/assets/openwrt_i2c/cross_compile.png "Compilación cruzada")

Y aquí ejecutando en el router de forma remota:

![Ejecución C]({{site.url}}/assets/openwrt_i2c/ejecucion.png "Ejecución C remota")

Como puedes ver, una vez ya puedes comunicarte con otros dispositivos mediante diferentes
protocolos (aunque sea simulado por *bit banging*) se abren muchas posibilidades. Se puede
conectar una pantalla para mostrar datos, un módulo de expansión para tener más GPIO,
etc... Si has llegado hasta aquí tienes mucha paciencia :D. FIN.


### Enlaces:
* [OpenWrt - router Huawei HG556a](https://openwrt.org/toh/huawei/hg556a)
* [OpenWrt - GPIO](https://openwrt.org/docs/techref/hardware/port.gpio)
* [OpenWrt - i2c](https://openwrt.org/docs/techref/hardware/port.i2c)
* [OpenWrt - Compilación cruzada](https://openwrt.org/docs/guide-developer/crosscompile)
* [Datasheet LM75](https://datasheets.maximintegrated.com/en/ds/LM75.pdf)
* [Linux i2c - Documentación del kernel](https://www.kernel.org/doc/html/latest/i2c/dev-interface.html)
* [Artículo sobre bitbang i2c en OpenWrt para otro router](http://taiksonprojects.blogspot.com/2012/06/i2c-bitbanging-con-openwrt-en-fonera.html)
* [Código de ejemplo LM75 (no muy útil realmente)](https://cued-partib-device-programming.readthedocs.io/en/latest/i2c-com-lm75.html)
