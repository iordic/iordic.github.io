---
title:  "Mejortorrent: ¿por qué está casi todo mal?"
description: "Post sobre sintaxis del lenguaje Smali"
date:   2019-06-08 00:20
categories: android smali reversing apk coding
---

Es una página que llevamos en nuestros corazoncitos y el motivo es que es de las pocas
páginas antiguas que quedan para descargar torrents de series, peliculas, juegos, etc...

Pero vamos a lo importante, ¿qué cosas son malas y deberían mejorar? Vamos por partes.

## 1. Cifrado de conexión

Si usamos una de estas páginas está claro que el motivo no es exactamente lícito. 
Es lógico pensar que la web debería usar un certificado SSL para cifrar todas las 
comunicaciones que tengamos con esta para con ello evitar que terceros puedan ver que
contenido estamos explorando, buscando o descargando.

Pues por lógico que parezca mejortorrent no parece que le de mucha importancia a esto:

![no ssl]({{site.url}}/assets/mejortorrent/noenc.png)

## 2. Contenido estático

El contenido de la web es completamente estático, javascript solo se emplea para el mal.
Es decir, se usa básicamente para los anuncios y sobretodo para abrirte pop-ups
independientemente de donde pinches con el ratón: barra de búsqueda, botón, enlaces...

<video width="640" height="360" controls>
    <source src="{{site.url}}/assets/mejortorrent/busqueda.mp4" type="video/mp4">
    Your browser does not support the video tag.
</video>

Esto podríamos pensar que es algo malo, pero justamente por eso es por lo que es
perfecto: podemos desactivar javascript para toda la web sin que se deforme o deje de 
tener alguna funcionalidad. ¡Funciona exactamente igual y además  te ahorras los 
pop-ups!

Al ser estático las búsquedas se realizan mediante PHP y no con javascript (AJAX).
Si realizamos una búsqueda podemos comprobar que efectivamente, los parámetros se pasan
mediante el método GET (se ven directamente en la barra de direcciones) y son bastante
autodescriptivas, sí, eso que te decía tu profesor que tenías que hacer y sudabas de él:

![dirección búsqueda]({{site.url}}/assets/mejortorrent/direccion.png) 

:p

![dirección descarga]({{site.url}}/assets/mejortorrent/direccion2.png)

Es tan estático que cuando estaba aprendiendo a programar en Python 2.7 hice un script
de poco mas de 100 líneas de código (que usando ciertas librerías podrían reducirse 
considerablemente), aunque bueno es python, es normal. El código era un desastre, además lo hice hace años y creo que ya no funciona del todo, pero aquí viene
una captura de parte de su ejecución:

![Ejecución de script]({{site.url}}/assets/mejortorrent/script.png)

> ¡Qué desastre! Lo sé. Mal identado, la codificación UTF-8 fallando... Pero: ¡Ey,
> Era mi primerito día!

## 3. Cambios de dominio frecuentes

Al ser la página que más usaba para peliculas pues he visto como pasaba por distintos 
dominios: mejortorrent.com, mejortorrent.org, mejortorrent.tv, mejortorrentt.com, etc...

Creo que el motivo por el cual no lo cierran es simplemente que el servidor está fuera
de España, por lo tanto se limitan a realizar bloqueos de dominio a través de los 
distintos ISPs. Podemos comprobarlo de forma simple ejecutando `nslookup mejortorrent.com` y si estamos usando el DNS del ISP nos aparecerá esto:

![bloqueo de dominio]({{site.url}}/assets/mejortorrent/bloqueo.png)

El proveedor tiene en su servidor DNS que cuando solicitemos esa dirección nos devuelva *127.0.0.1* o lo que es lo mismo *localhost*. Es decir, nos dice que nos rediriga a nuestra propia máquina para que falle (a no ser que tengamos un servidor web ejecutando en nuestro equipo xd).

Podemos burlar esto simplemente cambiando el servidor de DNS a otro, si usamos por ejemplo el de google obtenemos:

![bloqueo burlado]({{site.url}}/assets/mejortorrent/desbloqueo.png)

 Por lo tanto los administradores solo tienen que registrar un dominio
nuevo y redireccionarla a la IP del servidor. Y por supuesto, confiar en que los 
usuarios estén usando un servidor de DNS adecuado.

Si te preguntas por qué no cierran el servidor en vez de bloquear el dominio la 
respuesta creo que se debe a que la página esta alojada fuera de España, mas 
concretamente en Ucrania según la propia información que nos facilita la página y
también si realizamos una consulta GeoIP:

![Ucrania 1]({{site.url}}/assets/mejortorrent/ukr1.png)

![Ucrania 3]({{site.url}}/assets/mejortorrent/ukr3.png)

## 4. Minado de criptomonedas

Cuándo se puso de moda usar *CoinHive* para minar la criptomoneda *Monero* todas estas
webs de piratería aprovecharon para añadirlo a su página web en forma de script de 
javascript (otro motivo que teníamos para desactivarlo). Esto hacía que al acceder a la
página tu CPU se ponía al 100% jajaj. Por suerte CoinHive dejo de dar servicio y lo
quitaron. Por desgracia eso hace que a día de hoy no puede poner captura de ello. :(


## 5. Virus

Esto era algo que antes no pasaba y no se a que se debe, pero ahora de forma aleatoria
se descarga un zip cuando pinchas sobre el enlace de descarga del fichero torrent. No
he podido comprobar que es lo que hace este virus pero es obvio que lo es 
([aquí](https://www.mediavida.com/foro/hard-soft/troyano-minador-617386) dicen que es
un troyano para minar criptomonedas).

![zip sospechoso]({{site.url}}/assets/mejortorrent/virus1.png)

Descomprimido:

![descomprimido]({{site.url}}/assets/mejortorrent/virus2.png)

Es un fichero de visual basic script que viene compilado, he usado un decompilador 
online y al parecer el código está ofuscado. O eso o al decompilar se queda así de
feo, tampoco le daré muchas vueltas.

![decompilado]({{site.url}}/assets/mejortorrent/virus3.png)

## 6. ¿Fantasmas?
La página no cambia desde hace años y la cuenta de 
[Twitter](https://twitter.com/MejorTorrent), que ni siquiera sé si será original, está
sin estrenar. Parece que detrás de la página no exista nadie, no entiendo nada. 

Hay una "encuesta" que lleva ahí desde el origen de los tiempos, 4 FAQs que no resuelven
la mitad de los problemas y un correo ambiguo para las dudas:

![correo]({{site.url}}/assets/mejortorrent/para_mandar.jpg)

> Me pone nervioso, ¿cuál es el correo mejortorrent@gmail.com o MejorTorrent@gmail.com?

Casi que parece que no quieran que les contactes. Te dicen amablemente que no mandes 
gilipolleces. Algún día enviaré un correo pero tengo miedo xP

Saldría un buen Creepypasta de esta web...

## 7. Otras cosas

* La estructura de la página web está hecha mediante tablas. Si has estudiado alguna
  vez desarrollo de páginas web sabrás que esto es muy desaconsejable y que google lo 
  penaliza en temas de posicionamiento (SEO).
* No tienen opción de descarga mediante *magnet link*, sólo descargas de ficheros 
  .torrent. 
* Solo suben contenido en castellano (español de España que dicen vamos).
* Creo que no hace falta decir cómo se financian, con entrar queda bastante claro jajaj

# ¿FIN?

A pesar de todas estas cosas odiosas lo seguimos usando, será por costumbre o por 
comodidad pero la verdad que cumple su cometido y suele tener contenido a tiempo. 
Excepto con las series que tardan más. No hace falta decir que no tengo ni idea de quién
está detrás de la web. Me gustaría algún día preguntarles de donde sacan los torrents, 
ya que en la página no pone nada de compartirlos y los ficheros siempre están bien, 
pero eso es algo que no me dirían porque entonces dejaríamos de usar su página. :p

Y tú, ¿odias o amas mejortorrent?, continuará...
