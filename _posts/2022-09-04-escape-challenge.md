---
layout: post
title:  "Un reto sobre cómo la memoria en C puede traicionarte"
description: "Pequeño reto en C usado en escape room"
date:   2022-09-04 23:00
categories: reversing
---

Pues ya tocaba escribir algo nuevo, ¿eh o no?. Y como siempre me gusta mezclar conceptos
voy a explicar una de las pruebas que cree para el escape room de la WonderHack.
¿Qué conceptos? :O
* SUID
* Cómo los punteros pueden traicionarte
* Electrónica en GNU/Linux (?)

El escape room consistía en encontrar unas tres cartas que correspondían a la clave 
numérica de una caja con código. Esta servía para abrirla y encontrar el último muñeco:
el conejo blanco que siempre andan persiguiendo los hackers en las pelis (esta vez un 
moñeco DIY de lo más inofensivo jaja).

Básicamente la prueba consistía en conseguir ejecutar un programa que pedía una clave
y si era correcta activaba un servomotor que liberaba uno de los naipes. Todo montado
sobre una Orange Pi Zero (bendita orange pi):

![Arquitectura de la prueba]({{site.url}}/assets/wonderescape/arquitectura_prueba.png "Arquitectura de la prueba")

Es importante la parte del usuario sin privilegios, ya que está todo montado para que
tengamos que pasar por el aro de usar ese programa incluso teniendo el código fuente a
mano (explicación en siguiente apartado :p).


## 1. Configuración
Si te la pela la configuración y temas de funcionamiento de linux puedes saltar al
siguiente apartado. Esto servirá para darte más contexto y comprenderlo todo.

Necesitamos tener un usuario sin privilegios en la máquina, eso no lo voy a eplicar 
porque simplemente es ejecutar un comando. Lo que interesa es que necesitamos tener
activado el módulo de PWM en la máquina porque el servomotor necesita una señal de pulsos
para funcionar (el ancho de pulso determina la posición entre 0 y 180º que es el máximo 
de estos servomotores baratos).

Por suerte ya estuve investigando hace tiempo como mover un servomotor con Armbian y
tengo algo de info en un [repositorio propio](https://github.com/iordic/OPiZ-Camera) que
no es gran cosa pero me sirvió.

Para activar el módulo que permite mandar señales de PWM hay que añadir un parámetro al
fichero */boot/armbianEnv.txt* (marcado en rojo):
![PWM en armbianEnv.txt]({{site.url}}/assets/wonderescape/pwm_config.png "Añadir PWM a Armbian")

También se puede activar desde el comando **armbian-config** yendo a *System > Hardware*:
![PWM en armbian-config]({{site.url}}/assets/wonderescape/pwm_config2.png "Habilitar PWM en Armbian")

Después de esto se necesita reiniciar :)

¿Vale y ahora qué? ¡Pues obviamente necesitarás configurar más cosas! Hay que:

1. Crear el puerto por el que se van a enviar los pulsos.
2. Configurar el periodo y el duty cicle inicial (algo así como el tiempo que el pulso
está en HIGH, en el caso del servomotor indica la posición).
3. La polaridad (puede ser normal o inversa).
4. Activar salida por el puerto.

Aquí todas las líneas para configurar esto en un script que he llamado *init-pwm.sh*:

```sh
#! /bin/bash

# Enable pwm0 (pwm1 doesn't work on armbian):
echo 0 > /sys/class/pwm/pwmchip0/export

# Configuration:
echo 20000000 > /sys/class/pwm/pwmchip0/pwm0/period
echo 500000 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle

echo "normal" > /sys/class/pwm/pwmchip0/pwm0/polarity
echo 1 > /sys/class/pwm/pwmchip0/pwm0/enable

# Seek at begin:
sleep 0.2
val=500000

echo $val > /sys/class/pwm/pwmchip0/pwm0/duty_cycle

# Add permisions for writing:
chmod 664 /sys/class/pwm/pwmchip0/pwm0/duty_cycle
```
Como somos linuxeros sabemos que TODO, absolutamente TODO es un fichero, ¿quieres usar
un componente electrónico? Ahí tienes tu fichero para usarlo.

Importante recalcar este último *chmod* sobre el fichero que se usa para la posición del
servomotor. De este modo los usuarios que no pertenezcan al grupo *root* no podrán 
escribir valores en el fichero. Es decir, sólo se podrá leer la posición del servomotor 
pero no modificarla, cosa vital para que el reto no quede en papel mojado. Sino podrían
copiar la línea del código en la terminal y mover el servo sin "atacar" el binario 
proporcionado (porque se proporciona el código fuente con el reto).
Y el sleep creo que no hace falta, no recuerdo por qué está ahí jajaj

La línea `echo $VALOR > /sys/class/pwm/pwmchip0/pwm0/duty_cycle` es la que establece la
posición del servomotor.

Por último, para no tener que ejecutar el script cada vez que inicia (se pierde la
configuración al hacerlo) tenemos que intentar una de las mil formas que existen para
ello y rezar para que sirva. A mi me ha funcionado meter un fichero en
`/etc/systemd/system/` al que he llamado *init_pwm.service* cuyo contenido es:

```
[Unit]
Description=Init pwm port

[Service]
Type=simple
ExecStart=/bin/bash /root/init-pwm.sh

[Install]
WantedBy=multi-user.target

```

## 2. Código
En un principio pensé en que sería un reto guay para reversing, pero es una fumada tener
que analizar esto en poco tiempo y en una máquina remota con arquitectura diferente a
x86. Además, las estructuras de C en ensamblador suelen perderse como lágrimas en la 
lluvia. Así que proporcionaba el código y al lado el ELF compilado. El algoritmo es algo
así:

![Flujograma abstracto]({{site.url}}/assets/wonderescape/flujograma_abstracto.png "Flujograma alto nivel")

El código es:

```c
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <zlib.h>

#define CLOSED_POSITION 500000
#define OPENED_POSITION 1600000

void activar();
void usage();

struct State {
    char key[16];
    unsigned int crc;
    int active;
};

int main (int argc, char* argv[]) {
    char *keyword = argv[1];
    unsigned int original_crc = 0x0b96a135;
    struct State state;

    if (argc < 2) {
        usage();
        return -1;
    } else if(strlen(keyword) == 0 || strlen(keyword) > sizeof(struct State))
        return -1;

    memset((unsigned char *) &state, 0, sizeof(state));
    strcpy((char*) &state.key, keyword);

    // comprueba el crc de la clave
    state.crc = crc32(0L, Z_NULL, 0);
    state.crc = crc32(state.crc, keyword, strlen(keyword));

    if (state.crc == original_crc) state.active = 1;

    if (state.active) activar();
    return 0;
}

void activar() {
    printf("¡Abriendo trampilla!\n");
    // para mover el servo se usa ese fichero. Se indica el ancho del pulso par la posición
    FILE* fp = fopen("/sys/class/pwm/pwmchip0/pwm0/duty_cycle", "w");
    fprintf(fp, "%d\n", OPENED_POSITION);
    fclose(fp);
    sleep(2);
    fp = fopen("/sys/class/pwm/pwmchip0/pwm0/duty_cycle", "w");
    fprintf(fp, "%d\n", CLOSED_POSITION);
    fclose(fp);
}

void usage() {
    printf("hatch <clave>\n");
}
```
Viendo el código se ven los pasos:
1. Se rellena la estructura con 0s para limpiar posibles valores no controlados.
2. Copia el parámetro al primer atributo de la estructura, que sería la clave.
3. Sobre la clave aplica la suma de crc32 y la guarda en el segundo atributo crc.
4. Comprueba si el crc corresponde con el valor hardcodeado para la palabra "tiempo".
5. Si el crc es correcto pone a 1 el tercer atributo.
6. Si el tercer atributo está "seteado" abre la trampilla.


Y cree un *Makefile* para compilar el código:

```make
defaut:
	gcc -o hatch hatch.c -lz
	chmod u+s hatch
clean:
	rm hatch
```
Se usa el comando *make* como root para que cree el binario como usuario root y además
se establece el SUID para que el programa ejecute con sus privilegios (con chmod).
Recuerda que esto es porque el fichero que establece posición solo tiene permisos de
escritura para root (o su grupo).

El parámetro -lz se indica para que compile usando la librería de zlib (previmamente
instalada con `apt install zlib1g-dev`). Usada para el cálculo del *crc32*.

## 3. ¿Cómo se resuelve?
Todo el código gira entorno a la estructura *State* que guarda la clave, el crc32 y la
el valor de la activación. Aquí un pequeño esquemilla de como está estructurado en bytes
y posiciones:

![Estructura state]({{site.url}}/assets/wonderescape/struct_state.png "Estructura state")

¿Fácil no? Tenemos:
* 16 bytes de tamaño máximo para la clave.
* Una palabra de 4 bytes para el crc32 (1 entero).
* Otra palabra para guardar el entero que funciona a modo de booleano (lo común en C
vaya).

Por tanto se tiene una estructura que ocupa unos **24 bytes**.

Después de comprobar que se ha introducido un parámetro y no es excesivamente largo se
rellena a 0s la estructura para eliminar la morralla:

![memset de state]({{site.url}}/assets/wonderescape/memset_struct.png "memset a 0 de state")

Los pasos los he descrito arriba en el código. En una ejecución normal en la que se sepa la
clave se seguiría este esquema:

![Proceso relleno normal]({{site.url}}/assets/wonderescape/rellenado_struct.png "Proceso relleno normal")

La clave del esquema es la que use para el crc "hardcodeado" que vemos en el código.
Luego se calcula el crc y como es el correcto se pone a 1 el estado, activando la
trampilla.

Si te fijas en el código, en caso de ser el crc incorrecto, no se pone el estado a 0 ya
que se confía en que el memset ya lo ha puesto a 0 (error). Eso significa que, si se
consigue de alguna forma escribir en esa posición de memoria, el `if (state.active)` se
disparará. Ya que en C ese if significa que cualquier cosa diferente a 0 es 1.

¿Entonces cómo se ataca? Pues la función *strcpy* copia un string desde el primer byte
hasta que se encuntra un 0 que indica el final de este. Todos los fallos para poder
vulnerarlo están en este trozo de código:

```c
    //...
    else if(strlen(keyword) == 0 || strlen(keyword) > sizeof(struct State))
        return -1;

    memset((unsigned char *) &state, 0, sizeof(state));
    strcpy((char*) &state.key, keyword);

    // comprueba el crc de la clave
    state.crc = crc32(0L, Z_NULL, 0);
    state.crc = crc32(state.crc, keyword, strlen(keyword));

    if (state.crc == original_crc) state.active = 1;
```

1. La condición `strlen(keyword) > sizeof(struct State)` comprueba que la clave no sea
   más grande que la estructura entera y no el primer campo (realmente lo puse para que
   no hicieran un desbordamiento de pila sin más, que puede llegar a colar si no salta
   el stack canary, pero eso es harina de otro costal...).
2. El *strcpy* tiene por parámetro la dirección de memoria *state.key* pero esto a nivel
   de punteros no deja de ser un bloque de bytes consecutivos en los que ir escribiendo
   sin saber dónde acaban. Por lo tanto si se pasa del primer atributo empezará a
   escribir por encima de los demás en el orden que hayamos definido el struct.
3. Como he dicho, en ningún caso se vuelve a poner a 0 el *state.active*.

Por lo tanto, si se escribe una clave que no supere los 24 bytes pero sea suficientemente
larga para poder escribir en state. Tenemos: 16 bytes para atributo declave y 4 bytes
para crc. Por lo que con una clave con un tamaño mayor a 20 bytes y menor a 24 bytes se
sobreescribe el último campo (realmente menor a 23 bytes ya que el argumento añade un 0
al final para indicar el fin del string).

Aquí una representación de lo dicho:

![Desbordamiento de clave]({{site.url}}/assets/wonderescape/desbordamiento_clave.png "Desbordamiento de clave")

Y funciona claro:

![funcionamiento programa]({{site.url}}/assets/wonderescape/ejecucion.png "funcionamiento del programa")


### 3.1. Bonus
Si usamos el debugger de radare podemos hasta ver los bytes consecutivos en la pila.
¿Para qué? Pues yo qué se, por usar radare. Aquí vemos la pila después del llenado en la
ejecución "legítima":

![pila legítima]({{site.url}}/assets/wonderescape/stack_legit.png "Pila en ejecución legítima")

He subrayado con los mismos colores del esquema para que se vean los bytes que
corresponden a cada atributo. Antes he mencionado que la estructura se perdía al pasar a
compilador, aquí se demuestra:

![variables ejecución legítima]({{site.url}}/assets/wonderescape/variables_legit.png "Variables en ejecución legítima")

Si te fijas en las direcciones puedes renombrar las variables y correspondería a esto:

![variables renombradas]({{site.url}}/assets/wonderescape/variables_legit_rename.png "Variables renombradas")

Ahora para la palabra "*esternocleidomastoideo*" del ejemplo:

![memoria en ejecución ilegítima]({{site.url}}/assets/wonderescape/debug_ilegit.png "Estado en ejecución del ataque")

¿Puedes identificar en memoria cada parte? :p

## 4. Conclusiones
Nunca hay que fiarse de las funciones que actúan sobre memoria sin control de parada,
existe la función *strncpy* que determina el máximo de bytes a escribir. Típico discurso
vaya.

Aquí un video del cacharro casero funcionando con los moñecos:

<video width="720" controls>
    <source src="{{site.url}}/assets/wonderescape/demo_trampilla.mp4" type="video/mp4">
</video>

¿Todo esto para un puto servomotor que gira 90º y ya? Ehm... Eso parece (?).
La trampilla la hice con mis skills de marquetería de la secundaría, mientras vosotros
dabáis cosas molonas con Arduino yo estaba serrando las vertebras de un tiranosaurio
hecho con chapa de madera que fue destruido por mi hermano. Ahora tengo la "maqueta" y no
se qué hacer con eso, me costó unas horas de hacer como para tirarlo a la basura. :/

En fin, espero que te haya gustado la chapa y ya escribiré otro post cuando me apetezca.
Tengo varias cosas en el tintero pero por desgracia no se escriben solas. Dew.

## Links de interés
* [Pasos para activar el PWM](https://forum.armbian.com/topic/3886-pwm-on-h3-and-h2-with-mainline-kernel/)
* [Datasheet del servomotor](http://www.ee.ic.ac.uk/pcheung/teaching/DE1_EE/stores/sg90_datasheet.pdf)
* [Info de SUID](https://es.wikipedia.org/wiki/Setuid)
