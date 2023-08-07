---
title:  "¿Por qué los programas de Windows no funcionan en Linux y viceversa?"
description: "Humilde explicación de llamadas a sistema y wine"
date:   2019-11-03 18:00
categories: programación compiladores wine linux syscalls
---
Para aclarar, si eres programador o has programado alguna vez, te estoy hablando de
programas desarrollados con lenguajes de programación compilados. Luego mencionaré muy
brevemente loslenguajes interpretados. También es recomendable que sepas algo sobre
programación para tener más facilidad a la hora de entender algunas cosas que mencionaré,
no un nivel alto sólo nociones básicas. Empiezo:

Es uno de los pensamientos más comunes cuando nos introducimos en el mundo de GNU/Linux.
Si tienes algo de experiencia en estos sistemas ya estarás pensando en wine y quejandote
de mi post, pero no seas impaciente, luego voy con eso ;). La respuesta tiene que ver con
el comportamiento de un ordenador y la jerarquía de capas que utiliza. El esquema más
simple que existe por internet y sacado de wikipedia es:

<img src="https://upload.wikimedia.org/wikipedia/commons/d/dc/Operating_system_placement-es.svg" 
alt="Operating system placement-es.svg" width="250" height="370">

Los programas que usamos a diario están englobados en la capa de aplicación. El sistema
operativo dispone de ciertas funcionalidades que sólo él es capaz de realizar, como por
ejemplo crear ficheros, acceder a información del hardware del equipo, gestionar 
conexiones de red, etc.

Estas funcionalidades están protegidas por el sistema operativo y no se pueden ejecutar
de forma directa a través de un programa común. Para acceder a estas funcionalidades, el
programa debe pedirle al sistema operativo que sea el quien las ejecute, este se encarga
de gestionar y devolver el resultado de la ejecución de estas funciones especiales. Estas
peticiones se realizan mediante las denominadas "llamadas al sistema" (aka syscalls en
jerga informática) y son cosas tan simples como crear un fichero nuevo o tan complejas
como gestionar una conexión de red. 

Si tienes curiosidad, [aquí](https://en.wikipedia.org/wiki/System_call#Categories_of_system_calls)
tienes una lista bastante descriptiva por categorías.

En linux, las regiones que separan la ejecución de un programa de usuario de los 
servicios mencionados disponibles por el sistema operativo se llaman: user space y kernel
space respectivamente. Y la comunicación entre ellos se realiza mediante los syscalls.

Aún no te he dicho por qué motivo no se ejecutan los programas de Windows en Linux,
¿verdad? :p. En este punto ya deberías intuirlo, el problema es que básicamente cada uno
de los sistemas operativos tiene su propia arquitectura y con ello las llamadas a sistema
no son iguales ni las mismas exactamente.

## Wrappers: ayudando a los programadores

Si has programado alguna vez y no sabías de la existencia de las llamadas a sistema te
estarás pensando por qué nunca has tenido que programar estas y por qué para programas
sencillos tu código es exactamente igual en Windows que en Linux. Aquí es donde entran en
juego los llamados wrappers, son librerías estándar que añaden una capa de abstracción e
implementan por debajo las llamadas al sistema. Por este motivo cuando vayas a compilar
tu código, el compilador usará la librería implementada para el sistema
operativo específico sobre el que estés trabajando de forma totalmente opaca.

## Capas de abstracción para evitar recompilar el código

Por así decirlo, a la hora de facilitar el uso de los programas a los usuarios se puede
añadir una capa de abstracción más para quitarnos dolores de cabeza: una capa por encima
del programa o una capa por debajo de este.

### Por encima: lenguajes interpretados

El caso más común es el de los programas no compilados mediante el uso de interpretes
como por ejemplo los programas implementados en Java, Python, etc...

El intérprete es un programa ya compilado que se encarga de traducir las instrucciones
a código máquina en tiempo de ejecución y es el encargado de realizar las llamadas
necesarias para interactúar con el sistema operativo. Así el usuario sólo necesita
preocuparse por instalar la versión compilada del intérprete para su sistema operativo
y ya podrá ejecutar cualquier programa implementado en este lenguaje. De este modo se
añade portabilidad a distintos sistemas operativos.

### Por debajo: uso de emuladores o wine

Los emuladores o máquinas virtuales simplemente simulan el comportamiento de una 
arquitectura concreta de modo que mediante estas se puede ejecutar un programa por encima
de una arquitectura distinta a la destinada.

Sí alguna vez has usado wine quizás hayas leído eso del acrónimo "Wine is not an
emulator", aunque todos lo solemos llamar emulador cuando explicamos la existencia de
este programa a algún amigo o familiar. 

¿Por qué dicen que wine no es un emulador? En su página web tienen una wiki muy completa
e interesante. Dentro de ella tienen una sección en la que habla sobre su [arquitectura](https://wiki.winehq.org/Wine_Developer%27s_Guide/Architecture_Overview).
En ella nos dicen que wine no es un emulador simplemente porque no crea una máquina 
virtual ni te hace instalar nada de windows en él, simplemente es una colección de 
librerías propias que implementan el comportamiento de las llamadas a sistema de Windows
y lo traducen en llamadas que puede comprender Linux. Te animo a que leas el enlace que
he puesto ya que está muy bien explicado (inglés) y se extiende bastante más.

Estas dos técnicas se usan cuando tenemos un programa y no tenemos disponible su código
fuente, sólo el fichero compilado para una arquitectura específica.

## Jugando con strace y wine

En linux disponemos de herramientas para realizar un trazado de las llamadas a sistema
realizadas por un programa. Una de estas herramientas que viene preinstalada es strace.
Te animo a que la pruebes y veas como te muestra las llamadas a sistema que el programa
realiza, yo he realizado las pruebas con este código en C:
```c
#include <stdlib.h>
#include <stdio.h>


int main() {
    FILE *fichero = fopen("salida.txt", "w");
    if (fichero == NULL)
        return -1;
    fprintf(fichero, "Hola mundo\n");
    fclose(fichero);
    return 0;
}

```
Cómo ves más simple no puede ser, crea un fichero y escribe en él. Compilamos el código
con gcc: `gcc -o escribe escribe.c` y luego ejecutamos con: `strace ./escribe`. En cada
línea te mostrará que llamada ha realizado y dentro de los paréntesis que parámetros ha
usado. En internet puedes encontrar la documentación acorde buscando la instrucción
seguida de *syscall*.

Para Windows también existen herramientas para trazar las llamadas a sistema realizadas
por los procesos pero no he encontrado ninguno que me guste. Pero como la cosa va sobre
Wine puedes jugar con él y ver que llamadas a sistema realiza un programa compilado para
correr en Windows. Para ello compila el programa desde un equipo con windows y asegurate
de que tienes wine instalado. Ahora puedes ejecutarlo en la terminal de la forma: 
`WINEDEBUG=+relay wine escribe.exe` y con esto empezará a ejecutarlo mostrando por la
terminal la lista de llamadas que el programa va realizando a la API de Windows (es decir el conjunto de llamadas a sistema disponibles que tiene).

Pues como dice la famosa frase: eso es todo amigos. Y como yo digo: comparte, corrige y
colabora. ¡Hasta la próxima!

## Links de interés

* [API de Windows](https://en.wikipedia.org/wiki/Windows_API)
* [Interfaces del kernel de linux](https://en.wikipedia.org/wiki/Linux_kernel_interfaces)
* [Definición llamada al sistema](https://en.wikipedia.org/wiki/System_call)
* [Definición de función Wrapper](https://en.wikipedia.org/wiki/Wrapper_function)
* [Explicación espacio usuario y kernel](http://www.linfo.org/kernel_space.html)
* [Wiki de Wine](https://wiki.winehq.org/Main_Page)
* [Lista de syscalls de linux](http://man7.org/linux/man-pages/man2/syscalls.2.html)
* [Lista de componentes de Windows](https://en.wikipedia.org/wiki/List_of_Microsoft_Windows_components)
