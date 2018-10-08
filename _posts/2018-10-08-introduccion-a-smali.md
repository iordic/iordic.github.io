---
layout: post
title:  "Introducción al lenguaje de Smali"
description: "Post sobre sintaxis del lenguaje Smali"
date:   2018-10-08 00:30
categories: android smali reversing apk coding
---
El objetivo de esta pequeña guía es juntar todos los conceptos básicos que he encontrado dispersos por internet y la propia wiki del repositorio propio de smali, que es un poco desastre. Y de paso traer toda esta información al español, idioma en el que he visto que hay poco sobre el tema.

La mayoría de páginas que te hablan de ingeniería inversa a aplicaciones de Android, ni siquiera te explica los fundamentos del lenguaje, se limitan a decirte como descompilar y recompilar el apk. Y como tratar de traducirlo a lenguaje Java, cosa que es bastante ineficiente según dicen y os explicaré en el siguiente apartado.

Por ello me centraré en explicar cosas del lenguaje y cómo interpretarlo, ya que del proceso de descompilación/recompilación hay mucha información, además de muchas herramientas gratuitas y de código libre que te lo hacen de forma automática (incluso con interfaz gráfica). Quizás haga un post sobre ello...

## ¿Qué es Smali?
Smali, también llamado *baksmali*, es una herramienta que se creó para poder descompilar los ejecutables de android en un lenguaje que pudiese ser legible. De este modo se puede descompilar una aplicación, modificarla y recompilarla para ejecutarla en Android. El lenguaje generado es cercano al ensamblador y su sintaxis está basada en los lenguajes de *Jasmin* y *dedexer* como el propio autor [indica](https://github.com/JesusFreke/smali/blob/master/README.md#about).

## ¿Porqué Smali?
Como sabemos, las aplicaciones nativas de Android se desarrollan en el lenguaje de programación *Java*. Si has programado alguna vez en Java, sabrás que al compilarlo, el código fuente se transforma en ficheros binarios que pueden ser ejecutado por la máquina virtual de Java (*JVM*). A este tipo de ficheros se les conoce como **bytecode** y terminan con la extensión *.class*.

En Android dan un paso más hacia delante, estos ficheros *.class* son recompilados de nuevo en un único fichero *.dex* (contenido en el [apk](https://es.wikipedia.org/wiki/APK_(formato)) que contiene todas las clases usadas por el proyecto/aplicación. Este es un bytecode específico para la máquina virtual de Android (Dalvik/ART).

![Esquema dex](http://siis.cse.psu.edu/pics/class_to_dex.jpg)

> Apunte: si hay demasiadas clases en el proyecto puede haber más de un fichero .dex, ya que este tiene un límite de clases máximas que puede almacenar.

Después de todo esto, estarás pensando que por qué no descompilar directamente del bytecode a Java y ahorrarnos aprender un lenguaje nuevo. Por lo visto, a diferencia de los ficheros *.class* que se pueden descompilar con facilidad mediante distintas herramientas, los bytecode de Android son casi ilegibles.

> Existen herramientas en internet que convierten los ficheros *.dex* a ficheros *.jar* como [dex2jar](https://github.com/pxb1988/dex2jar).
Después otras herramientas pueden abrir las clases contenidas en esos *.jar*, para leer el código fuente directamente de los ficheros *.class* (como por ejemplo [jd-gui](https://github.com/java-decompiler/jd-gui)). Pero esto tiene varios problemas:
* No siempre funcionan. No siempre conseguiremos convertir el *.dex*.
* El código obtenido en Java nos puede servir para entender como funciona la aplicación de una forma más legible, pero si queremos modificar el código y recompilarlo no podremos.

Por todo ello, usamos smali que puede descompilar y recompilar el código con facilidad.

## Sintaxis
Es aconsejable haber visto algún lenguaje en ensamblador y Java (sobretodo en aplicaciones Android) para entender ciertas cosas del código obtenido por smali.

### 1. Registros
En ellos guardaremos los datos de forma temporal para realizar distintas operaciones. Si has visto o programado alguna vez en algún lenguaje ensamblador entenderás bien su función.
Los registros que usa Dalvik son todos de 32 bits (4 bytes), que pueden contener cualquier tipo de dato. Si queremos usar registros de 64 bits (por ejemplo con variables de tipo *long* o *doble*) se usan 2 registros para ello.

Dentro de un mismo método se declaran el número total de registros que se van a usar mediante a directiva *.registers*. Otra opción es la directiva *.locals* que especifica el número de registros totales dentro del método sin contar los parámetros. Los nombres de registros locales empiezan por la letra **v** y los parámetros por **p**. Los parámetros en realidad son registros normales pero se les puso ese nombre por comodidad a la hora de revisar el código.

|Locales |Parámetros |
|:------:|:---------:|
|   v0   |           |
|   v1   |           |
|   v2   |    p0     |
|   v3   |    p1     |
|   v4   |    p2     |

### 2. Tipos
#### 2.1. Primitivos
Los tipos primitivos se representan por una única letra mayúscula:

| Nombre |Tipo de variable                                   |
|:------:|---------------------------------------------------|
|   V    | Void (sólo se puede usar para valores de retorno) |
|   Z    | Booleano                                          |
|   B    | Byte                                              |
|   S    | Short                                             |
|   C    | Char                                              |
|   I    | Entero                                            |
|   J    | Long (64 bits)                                    |
|   F    | Float                                             |
|   D    | Double (64 bits)                                  |

#### 2.2. Objetos
Los objetos se declaran de la forma:
```
Lpaquete/nombre/NombreObjeto;
```
Las distintas partes de la declaración son:
* **L**: situada al principio, indica que lo que le sigue es una variable de tipo objeto.
* **paquete/nombre**: ubicación del objeto.
* **NombreObjeto**: el nombre del objeto.
* **;**: indica el final de la declaración del objeto.

#### 2.3. Vectores
La declaración de vectores se representa mediante el simbolo **[**. Unos ejemplos serían:

* **[I**: equivalente a *int[]*.
* **[[I**: equivalente a *int[][]*.
* **[Ljava/lang/String;**: equivalente a *String[]*.

El número máximo de dimensiones que puede tener un vector es de 255.

### 3. Métodos
#### 3.1. Declaración
La declaración de métodos es la siguiente:
```
.method modificador nombreMetodo(parámetros)Retorno
    .
    .
    .
.end method

```
Siendo:
* **Modificador**: private, public o protected.
* **Parámetros**: parámetros de cualquiera de los mencionados en el punto anterior. Se escriben juntos **sin ningun tipo de separador**.
* **Retorno**: el retorno de la función como uno de los tipos también mencionado en el punto anterior y también sin ninguna separación del paréntesis.

Por ejemplo, el típico método main de una clase Java sería de la forma:
```java
.method public static main([Ljava/lang/String;)V
    .
    .
    .
.end method
```
En este ejemplo vemos una definición de lo que parece ser el típico método *main* que los programadores de Java ya conocerán, en el que se puede apreciar que recibe como parámetro un *String* y devuelve un tipo *void*.

Ahora vamos a ver otro ejemplo de la documentación del autor para que puedas comprobar si lo has entendido:
```java
SMALI: metodo(I[[IILjava/lang/String;[Ljava/lang/Object;)Ljava/lang/String;
 JAVA: String metodo(int, int[][], int, String, Object[])
```

#### 3.2. Llamadas
Las llamadas a métodos se declaran de la forma:
```
LMiObjeto->método(parámetros)V
```
Siendo **MiObjeto** el objeto que contiene el método que vamos a ejecutar. Y lo otro ya lo sabemos porque es idéntico al apartado de declaración.


#### 3.3. Atributos
Según el autor de smali, los atributos toman la forma:
```
Lpaquete/nombre/NombreObjeto;->NombreAtributo:Lpaquete/nombre/NombreObjeto;
```
Siendo la primera declaración de variable el objeto que lo incluye, el nombre del atributo seguido de dos puntos y por último el objeto del tipo al que pertenece el atributo.

Pero yo en cambio al descompilar código me he encontrado también la directiva **.field** sin el objeto al que pertenece y el tipo de variable. Por ejemplo:
```
.field private id:I
```

### 4. Opcodes
Son los códigos de operación que realiza Dalvik con los registros y mediante los cuales se realiza la lógica del programa. Hay una gran cantidad de ellos que abarcan el rango hexadecimal desde 00 hasta ff. Algunos de estos valores no se usan, los podemos encontrar todos en la [documentación oficial](https://source.android.com/devices/tech/dalvik/dalvik-bytecode#instructions). Las etiquetas que asigna smali suelen ser las mismas que las que contiene la tabla, así que podemos usar la herramienta de buscar del navegador, para buscar directamente, la descripción del opcode del que queramos indagar.

### 5. Directivas
No he encontrado de momento ningún recurso donde vengan todas explicadas, al parecer hay muchas. De todos modos, la mayoría son bastante descriptivas y podemos intuir para que sirven simplemente leyéndolas.

### Analizando un ejemplo
Vamos a analizar un ejemplo sencillo del propio repositorio de smali, el código del típico programa de "HolaMundo":
```java
.class public LHelloWorld;

.super Ljava/lang/Object;

.method public static main([Ljava/lang/String;)V
    .registers 2
    sget-object    v0, Ljava/lang/System;->out:Ljava/io/PrintStream;
    const-string   v1, "Hello World!"
    invoke-virtual {v0, v1}, Ljava/io/PrintStream;->println(Ljava/lang/String;)V
    return-void
.end method
```
Vamos a analizarlo desde arriba:
1. Empezamos viendo la declaración de la clase pública que hemos llamado "HelloWorld" y cuyo padre es "Object".
2. Se declara un método "main" que recibe un String como parámetro y devuelve un tipo void.
3. Dentro del método vamos a utilizar dos registros locales.
4. Carga el atributo "out" de tipo "PrintStream", contenido por el objeto "System", en el registro v0.
5. Carga en el registro v1 la referencia de memoria al String "Hello World!".
6. Ejecuta una llamada a la función "println()" pasándole como argumentos el propio objeto "PrintStream" (this) y la referencia del String que está en v1.
7. Devuelve void y por último cierra el método.

## Links de interés
* [Repositorio Github de Smali](https://github.com/JesusFreke/smali/wiki)
* [Documentación de Dalvik/ART](https://source.android.com/devices/tech/dalvik)
* [[GUIDE] Smali coding guide for beginners](https://forum.xda-developers.com/showthread.php?t=2193735)
* [Página de dedexer](http://dedexer.sourceforge.net/)
* [Página de Jasmin](http://jasmin.sourceforge.net/)
* [[Tutorial] Modificacion y sintaxis de los Smalis](https://www.esp-desarrolladores.com/showthread.php?t=4267)
