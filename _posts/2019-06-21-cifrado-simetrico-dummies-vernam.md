---
layout: post
title:  "Cifrado simétrico para dummies: Vernam"
description: "Crea tu primer cifrador simétrico con Python 3"
date:   2019-07-21 18:20
categories: criptografía cifrado simétrico vernam seguridad código programación python
---
A raíz del post que escribí el otro día sobre cifrado simétrico y asimétrico he decidido
que sería instructivo crear un post que hablara sobre el que posiblemente sea el cifrador
simétrico más simple que existe: el cifrado usando el método Vernam.

La idea de usar un algoritmo simple es crear un sencillo script de python que lo 
implemente sin capas de abstracción (como son librerías de terceros). Intentaré mostrar
los resultados y dejaré el código enlazado abajo para quién quiera revisarlo.

¡Empezemos!

# Entorno usado
Para las pruebas y los resultados he usado una máquina GNU/Linux. Ya que dispone de
herramientas muy cómodas para acceder y manipular los datos de los ficheros. 

Una de estas herramientas es el comando *xxd* que muestra todos los bytes del contenido
de un fichero en hexadecimal y su traducción a ASCII. Lo he usado para las pruebas y voy
a explicarlo con una captura para que no te pierdas. Veamos un ejemplo:

![captura de xxd]({{site.url}}/assets/vernam/xxd.png)

Vale, voy a explicar las secciones de *xxd* según los colores que he marcado:

* **Azul**: esta sección representa en hexadecimal la posición de memoria del fichero que
corresponde al primer byte de esa línea. Por ejemplo, la primera nos dice que el byte 48
que corresponde al caracter 'H' está en la primera posición. No le daremos mucha
importancia a esta sección.

* **Rojo**: posiblemente la más importante. Representa en hexadecimal el valor en 'crudo'
de cada byte del fichero, sin importar el tipo de fichero que sea. Cada byte son dos dígitos.

* **Verde**: esta sección intenta mostrar los caracteres que pertenecen al byte de cada
posición de la sección que tiene a su izquierda. Si no es posible convertir a un caracter
ASCII nos muestra un punto. Cada byte es un caracter, por lo tanto es obvio pensar que
esta sección ocupa la mitad de espacio que la anterior.

Este comando tiene muchos parámetros útiles, pero no nos hacen falta para esto. Ahora
vamos con lo divertido.

# Cifrado Vernam
El cifrado Vernam es muy sencillo, solo tienes que entender qué es la operación XOR a
nivel de bytes y la conversión de texto a hexadecimal con la tabla ASCII.
Lo de la taba ASCII es para comprender los ejemplos, a Vernam le da igual si tu fichero 
tiene texto o una foto de tu gato. He joseado este esquema de internet porque era el más
fácil de entender:

![esquema vernam]({{site.url}}/assets/vernam/vernam_schema.gif)

Explico un poco como sería: 
1. Creamos una clave de cifrado que tiene el mismo tamaño (en bytes) que el mensaje que
vamos a cifrar.
2. Por cada byte del mensaje a cifrar realizamos la operación XOR con el byte de la misma
posición en la clave.

Parece absurdo, ¿a que sí? Pues es el único cifrado irrompible siempre que no se repita
la clave de cifrado para cualquier otro fichero/mensaje. Luego explico el motivo.

# El código
## Cifrado/descifrado
Lo he codificado en python3 porque es un lenguaje que adoro, puedes hacerlo en C si
te gusta sufrir ;). Aquí tenemos la parte que hace toda la magia:

```python
def cifrar(clave, datos):

    n = len(datos)

    if len(clave) != n:
        print("Error: la longitud de clave y los datos no coincide.")
        return
    
    resultado = []

    for i in range(0, n):
        resultado.append(clave[i] ^ datos[i])

    return resultado

```

Los parámetros clave y datos son arrays de bytes. Tan simple como eso.

¿Recuerdas el concepto de cifrado simétrico? ¿Cómo harías para descifrar algo que has
cifrado con la función de arriba? Así:

```python
def descifrar(clave, datos):
    return cifrar(clave, datos)
```

Espero que lo entiendas xD

## Generación de clave
Para generar la clave otra función sencillita:

```python
def generar_clave(datos):
len(datos)
    clave = os.urandom(n_bytes)
    return clave
```

Aquí usamos una función que viene en las librerías estándar de python: ```urandom(n)``` q
ue sirve para generar el número de bytes aleatorios que le digamos. Se usa la librería 
```os``` porque le pide al sistema operativo que los genere él.

## Otros
Aparte de estas funciones que son el núcleo de nuestro cifrado le he añadido manejo de
parámetros y lectura/escritura en ficheros, como he dicho subiré el código completo y
dejaré el enlace abajo.

# Pruebas
## Cifrar
Creamos un fichero de texto, así será más ilustrativo. Después usamos el script para
cifrar ese fichero, con lo que nos generará el fichero que contiene la clave y una copia 
con el fichero cifrado. Aquí una captura:

![Ejecución cifrado]({{site.url}}/assets/vernam/cifrado.png)

## Descifrar
Eliminamos el fichero de texto original para quedarnos solo con el fichero cifrado, así
podemos comprobar que funciona el script, descifrando y obteniendo el resultado original:

![Ejecución descifrado]({{site.url}}/assets/vernam/descifrado.png)


# ¿Por qué no hay que repetir la clave nunca en Vernam?
Aunque sea difícil de repetir porque cada fichero tiene su tamaño, existen entornos donde
los datos siempre tienen las mismas dimensiones. Si tuvieramos la gran idea de usar la
misma clave para todo estaríamos cometiendo un grave error.

La explicación es que la operación XOR la carga el diablo: con una sola de las dos
entradas y su salida podemos obtener la otra entrada.

Eso significa que si de algún modo consiguieran obtener uno de los ficheros sin cifrar
con su equivalente cifrado se desmontaría todo el sistema, ya que seríamos capaces de 
obtener la clave de cifrado.

Os lo voy a demostrar. Para ello creamos un segundo fichero de texto plano que tenga 
exactamente la misma longitud que el fichero usado anteriormente y lo comprobamos por si
acaso:

![Comprobación fichero 2]({{site.url}}/assets/vernam/comprobaciones.png)

Como vemos tenemos dos ficheros con textos diferentes pero exactamente la misma longitud
entre ellos y la misma que la clave. Ahora para realizar la demostración, ciframos el
segundo fichero, borramos el segundo fichero original y la clave de cifrado. Recuerda que
en este paso tenemos el fichero uno cifrado y su equivalente sin cifrar:

![Preparación para la prueba]({{site.url}}/assets/vernam/preparacion.png)

Vale, ahora tenemos los siguientes ficheros:
* Fichero 1 cifrado
* Fichero 1 sin cifrar
* Fichero 2 cifrado

Ahora usamos uno de los dos ficheros 1 como clave y el otro como si fuese el fichero 
cifrado. No importa el orden (el orden de los factores no altera el producto ;D). Con
esto obtendremos un fichero que será la clave que habíamos usado para cifrarlos:

![Extracción de la clave]({{site.url}}/assets/vernam/extraccion.png)

(Lo de mover el fichero lo hago para que al "descifrar" no sobreescriba el fichero 
original, pero esto no importa mucho, una vez tengamos ya la clave nos daría igual).

Una vez obtenida la clave la usamos como para descifrar el fichero 2:

![Descifrado del fichero 2]({{site.url}}/assets/vernam/descifrado2.png)

Y podemos comprobar que el fichero 2 se ha descifrado perfectamente :O.

**Conclusión**: Si la clave es única para cada fichero no nos sirve para nada, total ya 
teníamos ese fichero sin cifrar. Por eso se dice que es seguro siempre que la clave sea 
única para cada mensaje.

## ¿Por qué no ciframos ficheros con Vernam?
Por dos sencillos motivos:

1. Guardar la clave sin exponerla y de forma segura es complicado.

2. El tamaño de la clave ha de ser igual al fichero a cifrar, eso significa que si
queremos cifrar por ejemplo una película de 4 GiB, la clave ocupará otros 4 GiB. A esto
debemos sumarle lo de que cada clave ha de ser única, imaginate cifrar un disco duro
entero de 1 TiB: otro TiB para la clave :D.

# Links de interés
* [Conversor ASCII a Hexadecimal](https://www.rapidtables.com/convert/number/ascii-to-hex.html)
* [Tabla ASCII](https://www.ascii-code.com/)
* [Código](https://gist.github.com/iordic/76508bdf29cd4625a9e5d015b32d6b4a) completo del script usado para este post
