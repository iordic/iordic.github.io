---
title: "Ez hardware hacking: IPCam china"
description: "Haciendo ingeniería inversa a una cámara IP china"
date: 2025-10-03 17:00
categories: hardware-hacking electronica
header:
  og_image: /assets/images/hacking_ipcam/og_image.jpg
---
Antes de empezar, decir que de hardware hacking sé lo justo, ojalá saber más. Bueno ojalá
tampoco, poco a poco, que es algo que si quiero aprender más. Lo poco que sé es por tener
conocimientos de electrónica digital y un poquitín de base teórica de sistemas operativos.

Me han dejado una cámara IP vieja, de estas de los chinos, bueno no sé si dejado o dado,
igualmente puede ser expropiada en nombre del sindigato. La cosa es que en la caja trae
un enlace a una aplicación propietaria, en formato QR, y ese enlace no funcionaba:

![Enlaces QR de descarga]({{site.url}}/assets/images/hacking_ipcam/enlaces_packaging.jpg "Enlaces de descarga QR en el empaquetado")


![Enlace descarga]({{site.url}}/assets/images/hacking_ipcam/enlace_descarga_app.png "Enlace de descarga caído")

Entonces, surgió la duda: ¿hay algo que se pueda hacer para volver a usarla? ¿realmente 
funciona?

## 1. Respuesta rápida (y aburrida)

No os voy a mentir, es lo primero que debería haber hecho y es lo último que hice porque
soy un poco impulsivo y un poco tonto. Podría decir que es porque quería entrar en las 
tripas, que también, pero es que me cegué con eso y fui directamente al meollo jaja

El problema básicamente es que debería apuntar a lo que parece una nueva url: [eye4.app](https://eye4.app)

La cosa es que si buscas 4 segundos en Google llegas a la aplicación que te sirve:

![eye4 - Play Store]({{site.url}}/assets/images/hacking_ipcam/app_store_eye4.png "Aplicación eye4 en play store")

La he probado y efectivamente sirve, te hace escanear un QR en la base y directamente se
víncula a la aplicación:

![Info ipcam base]({{site.url}}/assets/images/hacking_ipcam/info_base_camara.jpg "Información en la base del cacharro")

El QR realmente contiene la misma info que el UID escrito. 

![Probando Eye4]({{site.url}}/assets/images/hacking_ipcam/captura_app_vinculada.jpg "Eye4 con la ipcam ya vinculada")

![Foto externa pruebas]({{site.url}}/assets/images/hacking_ipcam/foto_externa_probando_app.jpg "Foto desde fuera mientras probaba la aplicación")

¿Recordáis el [router del post de openwrt]({{site.url}}/2020/09/04/openwrt-con-i2c)?
Luego iremos a lo de por qué mierdas aparece en la foto. Está por una cosa :p

Ah sí, el cable ese a modo de coleta cámara chepuda también está por cosas.


## 2. Echando un ojo al hardware
Todo lo que voy a describir en este bloque se basa básicamente en ojear y buscar la info
por internet, luego en otras secciones empezarán las pruebas, pero es importante primero
ver de qué se compone el sistema.

Bueno, cómo he dicho, antes de empezar por lo más lógico lo que hice fue desmontar el
cacharro. Me gusta demasiado destripar cosas.

Ahora que he desmontado la cámara entera puedo decir que el sistema lo componen dos PCBs.
En la base tiene una pcb sencilla, que voy a enseñar por los dos lados. He puesto 
numeritos para explicar cosas.

Primero la parte de abajo de la placa, la que vemos nada más desmontar la tapa de abajo:
![PCB base bottom]({{site.url}}/assets/images/hacking_ipcam/pcb_base_abajo.jpg "Cara inferior de la PCB de la base")

Y por el otro lado tenemos esto:
![PCB base upper]({{site.url}}/assets/images/hacking_ipcam/pcb_base_arriba.jpg "Cara superior de la PCB de la base")

Habéis visto que dominio del dibujo, apreciad que lo he hecho con la derecha. Explico
cada punto:
1. Esto aunque no lo parezca de primeras es una tarjeta wifi que realmente funciona por
   USB. Que a ver, no he probado a desoldarla y pincharla a un pc, pero veréis luego por
   qué lo digo.
2. Este cable flex sube para arriba a la segunda placa, que realmente sería la primera
   porque es básicamente la principal. Pero bueno, lleva la alimentación y la
   comunicación con la placa de abajo (ethernet, wifi, motor, etc..).
3. Este conector lleva cables al servomotor de la base, un servo de 5 hilos.
4. Esto lo marco porque me pareció curioso, un conector de altavoz sin conectar, pero lo 
   más curioso es que, en la placa de arriba si hay un altavoz conectado.
5. El circuito de alimentación para todo el sistema.
6. Este es un microcontrolador que me da mucha rabia, porque no hay manera de encontrar
   el datasheet en google. De hecho la placa de arriba tiene otro exactamente igual, y
   parece evidente que su función es controlar el servomotor. Lo que no sé es el 
   protocolo que usará para comunicarse con la CPU/SoC principal, sería cuestión de 
   pincharle un analizador lógico o algo así...
7. El circuito para el conector ethernet.

Ahora pasamos a la PCB de arriba. Las fotos van a ser lamentables pero bueno, creo que se
puede ver más o menos, ahora no me apetece desmontar la cámara de nuevo.

La parte frontal (esta pcb se coloca en vertical):
![Frontal PCB cam]({{site.url}}/assets/images/hacking_ipcam/pcb_cam_front.jpg "Parte frontal de la pcb principal de la cámara")

La parte trasera:
![Trasera PCB cam]({{site.url}}/assets/images/hacking_ipcam/pcb_cam_back.jpg "Parte trasera de la pcb principal de la cámara")

Creo que no hace falta decir y señalar cuál es el módulo de cámara. Veamos:
1. La parte más importante de todo, el SoC que contiene la CPU. He encontrado el pdf, y
   de hecho, lo subo [aquí]({{site.url}}/assets/docs/hacking_ipcam/Hi3518 DataSheet.pdf) 
   por si desaparece en un futuro. Luego entramos en esto.
2. Es un conector que se autodescribe en la PCB, va conectado al/los led/s de la cámara.
   Tiene un led rojo y también leds infrarrojos (IR), lo típico para tener visión
   nocturna barata. No sé si va también a eso, porque en la pcb hay otro conector que pone
   "IR-CUT" y va al módulo de cam, igual desde esta se encienden o apagan, no sé.
3. El botón de reset típico que tienes que meter un palillo. Reinicia el cacharro, no sé
   si borra cosas, supongo que sí, porque guarda las cosas como el culo. Eso luego lo
   veremos.
4. Slot para tarjetas microSD, sirve para guardar grabaciones y fotos. De hecho se pueden
   programar horarios para ello, eso mola.
5. En la foto no se ve pero, pone que es otro conector para el motor, en este caso maneja
   el motor del eje Y. El que mueve de arriba a abajo vaya.
6. Otra vez el maldito microcontrolador del que no hay datasheet abierto, justo al lado
   del conector de motor también, como en la otra placa. Por lo que se confirma que este
   se usa para controlar el motor.
7. Conector de altavoz, este si que tenía un altavoz conectado jaja
8. El conector que hay al otro extremo del cable flex, este conecta las dos placas.
9. Otra pieza importante, una eeprom SPI, que contiene el firmware de la cámara. También
   encontré el datasheet y lo voy a guardar [aquí]({{site.url}}/assets/docs/hacking_ipcam/GD25Q127C.pdf).
10. No se ve un cagao pero son unos pads soldables en los que pone en orden: GND, TX, RX,
    3v. De hecho en la foto del frontal son los 4 agujeritos que están al lado del SoC. 
    Vamos, que es un puerto UART y han sido tan majos hasta de marcar en la pcb qué cable
    conectar a cada pad. Esto va a ser lo más importante de este post.
11. Otros dos pads que pone TX1 y RX1, estos no los he probado, intuyo que es otro puerto
    UART para otra cosa. Ya investigaré si es necesario.

En el pdf encontramos esto, no sé si es 100% aplicable al SoC concreto, un diagrama de 
bloques lógicos del SoC viene en el pdf:

![Bloques lógicos Hi3518]({{site.url}}/assets/images/hacking_ipcam/diagrama_bloques_logico_Hi3518.png "Diagrama de bloques lógico del SoC Hi3518")

> ℹ️ Para quién no sepa que es SoC (system on chip): se dice así porque es un encapsulado
  que no contiene solo la CPU, sino que contiene otros subsistemas que técnicamente son 
  periféricos de la CPU. Es hacer un sistema entero para un propósito específico. En este
  caso,como es un chip que se va a usar para montar cámaras IP, viene todo encasquetado y
  se reduce la complejidad a la hora de diseñar la placa. Por ejemplo: podemos ver, entre
  otros, como contiene un subsistema para el procesado de video y otro de imagen.

También viene un diagrama de bloques lógicos de cómo se suele implementar una cámara IP
completa con este SoC:

![Bloques lógicos IPCam]({{site.url}}/assets/images/hacking_ipcam/diagrama_bloques_logicos_ipcam.png "Diagrama de bloques lógico para implementar cámara IP con el SoC Hi3518")

De este bloque se pude suponer que aquello era una tarjeta wifi usb. Además, tiene 
sentido, la tarjeta tiene 4 pines, y en los extremos ponía vcc y gnd. Luego en las 
pruebas creo que se puede verificar del todo, iremos a ello si no me olvido jaja

## 3. La experimentación

### 3.1. Pinchando el UART y obteniendo info
Aquí he de reconocer que tuve que tirar un poco de ayuda de ChatGPT para ayudarme a según
qué cosas, ya que que buscar cosas tan concretas en Google es más complicado. Me ayudó
especialmente para usar el menú de U-Boot y volcar el firmware, pero bueno, eso ahora lo
explicaré.

Vais a ver capturas con fechas y horas random porque no lo he hecho del tirón, y puede
que algunas cosas las haya tenido que repetir a posteriori para este post, no me lo
tengáis en cuenta :P.

Lo primero que hice como se ve en la primera foto fue ~~lobotomizarlo~~ soldar unos
cables a los pads de UART para poder conectarme y ver si tenía suerte. Y así fue, lanzas
el picocom y te empieza a escupir toda la info como si le estuvieses sometiendo al
tercer grado. Voy a diseccionar lo que vea interesante en capturas.

Primero, el arranque de U-Boot:

![Arranque U-Boot]({{site.url}}/assets/images/hacking_ipcam/arranque_uboot.png)

De momento ya nos dice el chip de EEPROM usado y el tamaño que tiene, que ya sabíamos,
pero nunca está de más una doble verificación. Ahora el arranque de linux:

![Arranque Linux]({{site.url}}/assets/images/hacking_ipcam/arranque_linux.png "Arranque del sistema Linux")

Se puede ver entre otros datos: 
* la versión del kernel de linux, que es bastante vieja
* la cpu y el nombre del chip/SoC, que ya sabíamos
* la cantidad de memoria RAM disponible

Y un trozo más abajo viene una de las cosas más importantes:

![Info Log particiones]({{site.url}}/assets/images/hacking_ipcam/log_info_particiones.png "Información de particiones en el log")

Eso nos muestra la información de cómo están guardadas las particiones dentro de la 
eeprom. Y con ayuda de ChatGPT he entendido para qué servían, que no tengo experiencia en
esto en concreto, especialmente para saber la diferencia entre las dos últimas
particiones. Entonces, el mapa de memoria de la eeprom se resume en:

![Mapa eeprom]({{site.url}}/assets/images/hacking_ipcam/mapa_eeprom.png "Diagrama de bloques del mapa de la eeprom")

Seguímos bajando en el log y vemos como inicia ya el sistema con sus demonios (y quién 
soy yo para juzgar, quién no tiene los suyos internos):

![Inicio del sistema]({{site.url}}/assets/images/hacking_ipcam/inicio_linux.png "Inicio del sistema")

Ahí, a parte del ascii art cutre que se agradece, vemos unas credenciales, hago spoiler:
son las de la web, pero esto aún no lo sabemos. Además, coincidía con la contraseña 
escrita en la base de la cámara antes de cambiarlaa (contraseña a la que, en un ataque de
originalidad y porque me obligó la app al instalarla, le añadí más 8s).

Y nos topamos más abajo con la información del puerto e IP, que se usa para la web, en
caso de estar conectada a alguna red:

![alt text]({{site.url}}/assets/images/hacking_ipcam/log_info_web_port.png "Puerto del servidor web e IP de la cámara")

Aquí he hecho trampa porque está conectada a través de ethernet, con el router mencionado
arriba. Los motivos tienen que ver con el siguiente apartado. Pero respecto a la captura,
creo que se lee bastante bien lo que significa.

> ℹ️ Dato curioso y que me enfada: no sé por qué, si configuro el puerto web a otro, no
  se guarda correctamente. Cada vez que reinicias la cámara se cambia el puerto por uno
  al azar, y es bastante molesto.

De todos modos, ¡no me olvido de la tarjeta wifi eh!, aquí sale la info del chip:

![Info log wifi board]({{site.url}}/assets/images/hacking_ipcam/log_info_tarjeta_wifi.png "Log de información de tarjeta wifi")

Luego un poco más abajo info sobre drivers: de imagen, de i2c, de motores, etc...

Sale el modelo exacto de módulo de cámara al cargar el driver:

![Info log cam module]({{site.url}}/assets/images/hacking_ipcam/log_info_modulo_cam.png "Log de información del módulo de cámara")

Petición NTP ya que la placa no tiene pila para guardar la hora:

![Info log ntp request]({{site.url}}/assets/images/hacking_ipcam/log_info_ntp_request.png "Log de información de petición NTP")

Y luego, entre otras cosas sin importancia, ya empieza a authenticarse contra el servidor
chino para establecer el stream, ese que luego puedes ver desde la app:

![Info log P2P Eye4]({{site.url}}/assets/images/hacking_ipcam/log_info_p2p_eye4.png "Log de authenticación contra Eye4")

Usa un protocolo raro p2p para el stream, no sé mucho de eso. Fíjaos en la línea de la
petición GET tan rara, eso lo hace para obtener la IP del dominio sin fiarse del DNS que
tengamos. De hecho más abajo en el log muestra la respuesta:

![DNS Web query]({{site.url}}/assets/images/hacking_ipcam/query_ip_web.png "Petición de IP por web")

Si alguna vez necesitáis resolver IPs por http (no sé en qué escenario podría pasar) 
esta gente tan maja os lo resuelve, y además correctamente 🤠:

![DNS custom Web query]({{site.url}}/assets/images/hacking_ipcam/query_ip_web_custom.png "Petición IP por web custom")

Y aquí manda info de la cámara al servidor en cuestión:

![POST data eye4]({{site.url}}/assets/images/hacking_ipcam/log_info_post_data_eye4.png "Publicación de datos en Eye4")

Y abajo manda más datos pero weno, por último voy a poner este log:

![Onvif server start]({{site.url}}/assets/images/hacking_ipcam/log_info_onvif.png "Arranque servicio ONVIF")

Ni sabía que existía este protocolo llamado onvif. He buscado en internet pero no he 
profundizado en ello. Supongo que con algún programa pueda conectarme, ya probaré.

Y con esto acaba todo lo que se saca por UART de momento, porque al pulsar intro si que
deja meter credenciales. Pero no son las mismas del wifi.

### 3.2. La interfaz web
A la interfaz web se puede acceder con la IP y el puerto descubierto por UART. Es una
interfaz bastante sencilla, para logearte pide credenciales en un formulario propio del
navegador, de estos que usan algunos routers cutres, o el típico de htaccess de apache...

Al acceder da una lista de opciones, entras a la de tu navegador y te lleva al stream:

![alt text]({{site.url}}/assets/images/hacking_ipcam/web_ui_stream.jpg)

Luego hay opciones de configuración:

![alt text]({{site.url}}/assets/images/hacking_ipcam/web_ui_settings.png)

¡Y justo ahí es dónde no me hace ni caso al cambiar el puerto!

### 3.3. Volcado del firmware
Una de las cosas más interesantes suele ser poder extraer todo lo que guarde la memoria
eeprom, porque como hemos visto en la consola uart, ahí se guarda la partición de
arranque, el kernel de linux compilado y el sistema de ficheros que se monta al ejecutar.

Para ello podría volcar el chip usando la pinza del [post de reciclado de componentes]({{site.url}}/2025/03/31/campingaz-electronica).
Exacto, hoy tenemos especial recopilatorio de los simpson con referencias a posts del
pasado. Bueno a lo que íbamos, podría usar la pinza, pero si me habéis leído por twitter
o ese post, sabréis que odio esa pinza con todo mi ser por los disgustos que da y lo mal
que agarra.

Además, me sonaba que u-boot a veces permite extraer un dump de la eeprom por uart. Nunca
he extraído la eeprom así, una vez intenté lo contrario y brickee un cacharro. Pero bueno,
ahora solo voy a leer, no tiene que pasar nada, además la cámara no es mía (que no lo lea
la dueña).

Si os habéis fijado en la primera captura de UART, aparecía una frase que decía "pulsar
cualquier tecla para parar el autoarranque". Si pulsas, por ejemplo intro, puedes acceder
al menú de uboot:

![U-Boot enter]({{site.url}}/assets/images/hacking_ipcam/uboot_enter.jpg "Acceso a u-boot")

Y si pedimos las opciones disponibles nos da este listado:

![U-Boot help options]({{site.url}}/assets/images/hacking_ipcam/uboot_help.JPG "Comandos de U-Boot disponibles")

Supongo que la versión de u-boot también debe ser vieja, porque según he visto, en
versiones más modernas hay más opciones y más complejas.

Aquí, con algo de ayuda del chattie, vemos que la única opción que parece viable es usar
el comando para subir ficheros a un servidor tftp. Para ello hay que settear variables de
IP, de modo que pueda conectarse. También, aunque no es necesario, una variable para
determinar la dirección de memoria RAM dónde volcaremos la SPI y desde dónde leeremos
para subir al tftp. Digo que no es necesario porque puedes escribirla cada vez a mano.

Para declarar las variables hacemos esto:

```sh
setenv ipaddr 192.168.1.50       # IP de la cámara
setenv serverip 192.168.1.100    # IP de tu PC con TFTP
setenv loadaddr 0x42000000       # dirección de memoria RAM donde cargar los datos de la eeprom
saveenv                          # guardar valores
```

Luego con `printenv` se puede comprobar si se han guardado los valores:

![Environment variables U-boot]({{site.url}}/assets/images/hacking_ipcam/uboot_envs.JPG "Variables de entorno de U-Boot")

¿Recordáis lo del router? Pues ha llegado el momento de aclarar por qué lo necesito.

Tras varios intentos frustrados de conectar directamente la cámara al pc (por ethernet)
no lo conseguí, a pesar de poner misma máscara de red e ips dentro en las variables.
Entonces, para descartar, tenía que probar con algo que tuviese dhcp y lo que tenía por 
ahí tirado era este router. Según chattie podría haber probado con un switch, pero no 
tenía uno a mano. Aunque yo creo que era tema de dhcp. Al fin y al cabo, si no me 
equivoco, diría que las tarjetas de red en ordenador modernos hacen automáticamente la 
conexión cruzada.

Y bueno, dicho lo dicho, al final se  arregló con el router con openwrt. Aquí la chatarra
electrónica a veces sirve para cosas jaja

Para subir los ficheros, y habiendo seteado las variables de entorno, usamos estos
comandos:

```sh
sf probe 0   # inicializar SPI NOR

# Volcar bootloader por tftp
sf read ${loadaddr} 0x0 0x100000    # leer a RAM con offset 0x0, leer offset+0x100000 bytes
tftp ${loadaddr} boot.bin 0x100000  # subir por tftp a fichero boot.bin desde RAM los primeros 0x100000

# Volcar partición del kernel (desde 0x100000 a 0x400000 como hemos visto en la imagen)
sf read ${loadaddr} 0x100000 0x300000
tftp ${loadaddr} kernel.bin 0x300000

#Volcar RootFS
sf read ${loadaddr} 0x100000 0x300000
tftp ${loadaddr} kernel.bin 0x300000

#Volcar system
sf read ${loadaddr} 0xb00000 0x500000
tftp ${loadaddr} system.bin 0x500000
```

Esta parte la he hecho en windows (shame on me) porque era dónde estaba en ese momento.
Además me alegra haberlo hecho así, porque el programita que me recomendó chattie era lo
que necesitaba, una aplicación muy básica y portable para hacer el apaño (*tftpd64*):

![TFTP U-Boot partition upload]({{site.url}}/assets/images/hacking_ipcam/uboot_tftp_send.jpg "Subiendo el primer fichero con tftp")

Ejecutamos el volcado para las cuatro particiones y ya tenemos el backup de toda la 
memoria eeprom. Se supone que ahora los puedo unir con cat: `cat boot.bin, kernel.bin, rootfs.bin, system.bin > full_flash.bin`.
Pero realmente puedo volver a ejecutarlo especificando toda la memoria y que lo haga de 
"pe a pa", al fin y al cabo memoria RAM hay de sobra para copiarlo.

### 3.4. Examinar los ficheros
Ya tenemos las particiones en ficheros separados, ahora lo lógico sería pasar el
`binwalk`. Que, para quien no lo conozca, es una herramienta capaz de identificar
particiones y tipos de sistemas de ficheros en dumps como estos. También suele 
poder extraer los ficheros.

Podéis ver lo que saca al ejecutarlo sobre todos los volcados:

![Binwalk exec]({{site.url}}/assets/images/hacking_ipcam/binwalk_execution.png "Ejecución de binwalk sobre los ficheros")

En esta sección me interesa la parte del rootfs y de system para intentar sacar los 
ficheros de cada partición.

Para el rootfs nisiquiera hace falta binwalk, el comando `file` y el propio gestor
de ficheros ya reconocen que es una imagen de tipo *squashfs*. Y con más o menos esfuerzo
puedes acabar montándolo y viendo los ficheros:

![Mount SquashFS]({{site.url}}/assets/images/hacking_ipcam/mount_squashfs.png "Montar fichero con squashfs")

En mi caso tuve que instalar un plugin para dolphin que lo hacía, pero por ejemplo kali
viene preparado y lo monta sin problemas desde el explorador de ficheros.

![RootFS files]({{site.url}}/assets/images/hacking_ipcam/rootfs_partition_files.png "Ficheros de la partición RootFS")

En esta partición no hay nada muy reseñable, ficheros estándar de busybox. Lo único
interesante es esto:

![RootFS init file]({{site.url}}/assets/images/hacking_ipcam/rootfs_init_file.png "Fichero de inicio en arranque busybox")

Ahí, aparte del banner ascii cutrón que ya habíamos visto por uart, habla de un supuesto
fichero llamado "ipcam.sh" que arranca al iniciar busybox. Al ser un script custom estará
en la partición de system.

Ahora viene lo que me ha dado problemas, se ve que extraer un fichero con partición jffs2
(jefferson se llamaba?) no es tan rápido. Pero al final tras probar muchas cosas conseguí
montarlo siguiendo estos pasos:

```bash
sudo modprobe mtdram total_size=16384 erase_size=64
sudo modprobe mtdblock

sudo dd if=system.bin of=/dev/mtd0

sudo mkdir /mnt/system
sudo mount -t jffs2 /dev/mtdblock0 /mnt/system
```
Lo que hace, o eso interpreté, es montar en memoria ram una memoria flash simulada. Luego
copiamos el contenido del fichero a esta y montamos el "dispositivo" especificando que es
*jffs2*.

Y conseguimos montarlo y acceder:

![System partition mounted]({{site.url}}/assets/images/hacking_ipcam/system_mounted.png "Partición system montada")

Y aquí ya tenemos acceso a ese script "ipcam.sh" que ejecuta una serie de binarios:

![ipcam.sh script]({{site.url}}/assets/images/hacking_ipcam/ipcam_sh_show.png "Contenido del fichero ipcam.sh")

Voy a enseñar más cosas que he visto interesantes en la partición. Aquí se ve el
contenido de una carpeta "param", que tiene algunas configuraciones en ficheros .ini,
como por ejemplo el id de fábrica o el dominio "push.eye4.cn", pero también un usuario
(es una mickey herramienta que servirá para la siguiente sección 🧐):

![Param folder files]({{site.url}}/assets/images/hacking_ipcam/param_folder.png "Ficheros en param y contenido de passwd")

Una carpeta con los binarios en *system/folder*, os marco los que por nombre me parecen
interesantes:

![system/bin folder]({{site.url}}/assets/images/hacking_ipcam/system_bin_folder.png "Binarios dentro de system")

El fichero brush ese sale en ipcam.sh, pero weno, seguimos. Luego otra carpeta que 
contiene librerias en *system/lib*:

![system/lib folder]({{site.url}}/assets/images/hacking_ipcam/system_lib_folder.png "Librerías dentro de system")

Y por último hay una carpeta *www* que contiene todos los ficheros de la interfaz web y de
configuración:

![web folder files]({{site.url}}/assets/images/hacking_ipcam/system_www_folder.png "Carpeta www con ficheros web")

Ahí hay muchas cosas interesantes, los ficheros *-b.ini son como los fichero base que
contienen la configuración de fábrica. Y los que están sin -b la configuración guardada
actualmente.

Y eso es todo respecto a los ficheros, no hay mucho más.

### 3.5. Consiguiendo acceso a la consola
Al final de pura suerte conseguí acceder a la terminal. Si recordáis, en la partición 
*system* hay un fichero *param/passwd* que contiene un tal usuario **vstarcam2017**. 
Y no sé cómo, si copias y pegas pulsando intro, en algún momento accedes. Entiendo que
es porque no tiene contraseña, pero es que si lo haces cómo dicta la razón y la lógica no
va, es hacer la del mono con el teclado y entra:

![UART login success]({{site.url}}/assets/images/hacking_ipcam/login_uart.png "Acceso a uart con usuario de fichero passwd")

Tampoco ayuda el hecho de estar teniendo que lidiar con un log que no para nunca de sacar
líneas y líneas, que en su mayoría no sirven para nada o son redundantes.

Sinceramente, no es que haya hecho gran cosa dentro de la terminal loggeado, aunque me 
sirvió para conseguír saber cuál era el programa que levantaba el servicio web (y los 
otros por lo visto). Un tal "encoder":

![netstat - tcp listening processes]({{site.url}}/assets/images/hacking_ipcam/info_netstat.png "Listado de procesos escuchando en puertos TCP")

### 3.6. ¿Quién eres *encoder*?
Es el fichero que abre todos los puertos del servidor, debe ser un tipo importante jaja

De todos modos primero buscamos, usando nuestro vecino y amigo `grep`, sus usos dentro todos los ficheros de la partición,
ya que en el arranque no estaba:

![encoder mentions grep]({{site.url}}/assets/images/hacking_ipcam/encoder_uses.png "Ficheros dónde se menciona a encoder")

Y parece que quién lo usa es el "wifidaemon", que está en el script de *ipcam.sh*. Vamos a
tirar mano de otro viejo amigo (*radare2*).

![radare2 -A wifidaemon]({{site.url}}/assets/images/hacking_ipcam/r2_wifidaemon_init.png "Análisis de wifidaemon con radare2")

Y voy a hacer uno de mis diagramillas con los pasos que he hecho para sacar info del binariosto:

![wifidaemon using encoder]({{site.url}}/assets/images/hacking_ipcam/encoder_uses_at_wifidaemon.png "Usos de encoder dentro de wifidaemon")

No se ve nah, abrid la imagen en otra pestaña si eso, tengo que ver cómo implementar en 
el blog que salga un popup ampliando la imagen al pulsar encima. Me falta frontend.

La explicación: busco strings dentro del binario donde ponga "encoder" y, sabiendo las 
direcciones de memoria, busco referencias a ella en métodos dentro del binario. Con esto
se puede ver que se llama desde el método principal *main* y que simplemente hace una 
llamada a sistema [system()](https://man7.org/linux/man-pages/man3/system.3.html) para
ejecutar el binario. Y que tiene un `killall` para cuando actualiza el firmware (supongo).

Por cierto, está compilado con flags de debugger, por eso muestra todo el código al lado,
una práctica poco habitual en un producto comercial. A mi me viene de lujo que hayan sido
tan cutres, así puedo leer el código fácil.

Ahora a ver si consigo sacar algo de encoder.

![radare encoder analysis]({{site.url}}/assets/images/hacking_ipcam/r2_analyse_encoder.png "Análisis de encoder con radare2")

Sabes que la cosa va a estar jodida cuando ves esto a pesar de tener también símbolos de debug:

![radare list functions encoder]({{site.url}}/assets/images/hacking_ipcam/r2_list_functions_encoder.png "Listado de funciones con radare2")

Es decir, hay cómo 4000 funciones definidas y muchas de ellas con nombres fcn.AbunchOfNumbers...

Es un fichero al que habría que echar muchas horas, es demasiado grande, tiene partes en
las que carga módulos, en las que abre ficheros, procesa peticiones http... supongo que
es el compilado resultado de algún proyecto hecho para hacer de servidor.

Aquí podemos ver referencias a ficheros en la carpeta *www*:

![encoder www strings]({{site.url}}/assets/images/hacking_ipcam/encoder_www_strings.png "Strings con 'www' en el valor de encoder")

Usos de módulos:

![encoder motogpio.ko references]({{site.url}}/assets/images/hacking_ipcam/encoder_motogpio_ko_refs.png "Referencias a 'insmod motogpio.ko'")

Y una cosa curiosa es que muchos métodos importados tienen de nombre *HI_MPI_loquesea*:

![encoder hi_mpi_ functions imports]({{site.url}}/assets/images/hacking_ipcam/encoder_strings_hi_mpi.png "Funciones con nombres llamados hi_mpi_xxxxxx")

Eso suele ser indicativo de que se está usando un sdk o algo similar. He buscado en
Google y parece que existe un supuesto sdk llamado coloquialmente "Haisi" para los chips
Hi35xx, pero no se chino, mirad [esto](https://bbs.16rd.com/thread-575988-1-1.html) y [esto otro](https://bbs.16rd.com/thread-580040-1-1.html)
y sacad conclusiones.

![chinese post 1]({{site.url}}/assets/images/hacking_ipcam/haisi_post.png "Post en chino hablando del sdk")

![chinese post 2]({{site.url}}/assets/images/hacking_ipcam/haisi_post2.png "Post en chino hablando del sdk 2")

Ahí pone que hay un pdf con más info, listo para descargar, pero creo que hay que poner 
perras, paso. Bueno más que pasar que no tengo monedas raras desas. En un futuro si me 
aburro intentaré sacarle jugo al bin.

## Bonus hack: ¿Se nos está yendo de las manos esto de los CVE?
Me he enterado por el incibe que hay vulnerabilidades para cámaras como esta. A ver, 
tampoco es que me sorprenda, es lo habitual. 

Leyendo he encontrado [esta web](https://pierrekim.github.io/blog/2017-03-08-camera-goahead-0day.html)
en la que hay un listado con vulnerabilidades que afectan a cámaras del mismo rollo,
esta es un rebranding de la que sale en la imagen de la web. Pero es que algunas 
supuestas vulnerabilidades son un poco ridículas, sobretodo las que pone que para
explotarlas tienes que estar preautenticado como admin, pues a ver...

Por ejemplo, nos dice que sabiendo las credenciales podemos obtener el fichero de
configuración ejecutando esto:

![Obtener fichero system.ini]({{site.url}}/assets/images/hacking_ipcam/cve_obtener_config.png "Obtener configyración de la cámara")

Pero es que yo que sé, es cómo decir que es vulnerable porque saco las credenciales así:

![Obtener credenciales navegador]({{site.url}}/assets/images/hacking_ipcam/credenciales_desde_navegador.png "Obtener las credenciales desde el navegador")

PERO SI YA TE SABES LAS CREDENCIALES PARA ENTRAR. A ver, hago un pequeño inciso dándole
la razón, y es que si hubiera más de un usuario quizás sería un vector de ataque para
sacar las credenciales del admin y escalar privilegios. Y que no debería poder acceder
a ese fichero desde la web pero meh.

Por cierto, debería tapar la contraseña en la captura, pero cómo se me va a olvidar cuál
era a la hora de redactar el siguiente post, usaré este post de gestor de contraseñas. El
keepass es para cobardes.

De todos modos, ¡el sistema no permite tener más de un usuario! Y ese fichero aparte de 
las credenciales tiene poco de interesante. 

La web mola, eso sí, porque tiene los PoC detallados, y eso siempre es bien.

# 4. Próximos pasos
Aquí la antesala al siguiente post, si es que al final lo hago, pero se queda en el
tintero todo esto. Que encima es la parte que molaría conseguir.

## 4.1. Reemplazar el firmware por otro
He encontrado un firmware de código abierto que puede que sirva para la cámara, y me 
gustaría probarlo para librar a la pobre cámara de las cadenas del firmware chino, con lo
que ello implica: mandar cosas a servidores chungos... El firmware se llama OpenIPC y en 
[esta web](https://openipc.org/cameras/vendors/hisilicon/socs/hi3518ev200) se puede
descargar el binario ya compilado para meterlo en la eeprom y probar.

También está el [Github de OpenIPC](https://github.com/OpenIPC/firmware) para cotillear
y/o compilarlo desde el código fuente. De momento cosas de las que no me fio pero hasta
que no lo pruebe no puedo saber:

* creo que viene por defecto activada una opción para mandar el stream a sus servidores.
  Se supone que se puede desactivar, pero claro, si se te olvida te pueden ver en pelotas
  en la sección ["open wall"](https://openipc.org/open-wall). Igual me equivoco eh.
* no tiene pinta de que, de base, los motores vayan a funcionar en cuanto meta el 
  firmware dentro de la eeprom, ojalá, pero 0 fe. Tendría que investigar más eso, e 
  intentar sacar  los drivers o la lógica del firmware chino pero claro, kernel 3.x....

Pero weno, eso es harina de otro costal.

## 4.2. Modificar sistema de ficheros y sobreescribir la eeprom
En el punto anterior, dentro de la web de OpenIPC nos dice incluso como sobreescribir la
memoria eeprom, pero en resumen es hacer la inversa a lo que ya hemos hecho con el tftpd 
para obtener el dump. Si editas los ficheros puedes cargar la partición que te interese a
memoria, en las mismas direcciones que se encontraba la original. 

Aquí el único problema que he visto es que reempaquetar el sistema jffs2 igual no es 
algo que consiga a la primera. Ya que ha dado problemas para montar. Realmente esa 
partición, que es la de system, es la que nos interesaría modificar. Y la de rootfs quizás
para cambiar qué cosas ejecuta al arrancar y poco más.

## 4.3. Reemplazar el kernel por una versión más moderna
Por último, también molaría poder reemplazar el kernel por uno que sea un poco más
moderno, eso abriría posibilidades (supongo) para instalar utilidades y librerías más
modernas.

[Aquí](https://mark4h.blogspot.com/2017/07/hi3518-camera-module-part-1-replacing.html)
hay un post que explica como compilar un kernel para un SoC cómo este (diferente versión)
y subirlo. No he conseguido compilar ninguno de los dos repositorios que enlaza en el
post, creo que es porque es código demasiado viejo, pero volveré a intentarlo.


## ¿FIN?
Y esto es un hasta pronto, porque cómo he dicho, espero ampliar en una segunda parte.
Antes de irme, decir que la dueña legítima del cacharro ha decidido que tengo que
insertar esta imagen en el post para dar el visto bueno:

![Foto equipo hackvlc]({{site.url}}/assets/images/hacking_ipcam/reanimando_ipcams_team.jpeg "Foto de rigor hackvlc")

Semper fidelis, reanimando cámaras IP. ¡Nos vemos! <3

# Enlaces de interés
Esta sección suelo ponerla, básicamente voy apilando urls que he ido mirando y podrían
volver a serme útiles a mi o a otra persona.
* [Datasheet del SoC Hi3518 - Gracias Hackaday, nunca decepcionas <3](https://cdn.hackaday.io/files/19356828127104/Hi3518%20DataSheet.pdf)
* [Datasheet de la EEPROM](https://www.alldatasheet.com/datasheet-pdf/view/1151510/GIGADEVICE/GD25Q127CSIG.html)
* [App Eye4 en Google Play](https://play.google.com/store/apps/details?id=vstc.vscam.client&hl=es_EC)
* [Incibe hablando de nuestra amiga](https://www.incibe.es/en/incibe-cert/early-warning/vulnerabilities/cve-2017-5674)
* [Listado de vulnerabilidades](https://pierrekim.github.io/blog/2017-03-08-camera-goahead-0day.html)
* [Post hablando de cambiar el firmware](https://mark4h.blogspot.com/2017/07/hi3518-camera-module-part-1-replacing.html)
* [Obtenr referencias dentro de binarios con radare2](https://rada.re/advent/06.html)
* [Github de OpenIPC](https://github.com/OpenIPC/firmware)
* [Web instalación OpenIPC para nuestro SoC](https://openipc.org/cameras/vendors/hisilicon/socs/hi3518ev200)
* [TFTP portable para windows - tftpd64](https://pjo2.github.io/tftpd64/)
* [Página man de system()](https://man7.org/linux/man-pages/man3/system.3.html)
* [uClib - librería estándar que han usado para compilar binarios](https://www.uclibc.org/)