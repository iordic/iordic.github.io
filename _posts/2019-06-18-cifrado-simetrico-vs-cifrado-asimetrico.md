---
title:  "Cifrado: ¿simétrico o asimétrico?"
description: "Post sobre comparativas de cifrado simétrico vs asimétrico"
date:   2019-07-18 18:20
categories: criptografía cifrado simétrico asimétrico seguridad
---
En este post intentaré explicar las diferencias entre cifrado simétrico y asimétrico de
modo conceptual, es decir, no voy a entrar en detalles de como funcionan los algoritmos
ni cuáles existen. Voy a explicar cuál es el principio de ambos y cuál el campo de
aplicación que tienen, ya que realmente uno no es mejor que otro, simplemente tienen usos
distintos.

Más adelante intentaré explicar en que se basan los ransomware ayudándome de este post
para no tener que volver a explicar las diferencias entre uno y otro, puede que en ese
momento si que mencione algoritmos concretos, ya veremos. ¡Empecemos! :)

# ¿Qué significa cifrar?
El concepto es tan simple como el siguiente esquema:

![Esquema cifrado]({{site.url}}/assets/images/cifrado/esq_cifrado.png)

Básicamente consiste en convertir datos que pueden ser leídos por cualquier persona en
otro fichero o paquete de datos que no queremos que pueda ser leído por alguien 
indeseado. Para esta conversión se usa una clave, que al fin y al cabo en términos de
informática, no es más que un conjunto de bytes que usa el algoritmo como un parámetro 
para tratar el fichero de datos (también conjunto de bytes a efectos prácticos).

# ¿En qué consiste el cifrado simétrico?
Se usa el término simétrico para todos los algoritmos que usan la misma clave para
realizar el cifrado y el descifrado de los datos. El esquema sería:

![Esquema cifrado simétrico]({{site.url}}/assets/images/cifrado/esq_simetrico.png)

Viendo el esquema podrás entender porque se le da este nombre, ya que la forma de
descifrar es como "darle la vuelta" a la de cifrar y tenemos exactamente lo mismo.
El problema de este tipo de cifrado es que si alguien consigue la clave ya puede acceder
a la información que tanto apreciabas. :(

¿Cómo guardamos la clave de modo que nadie nos la robe? Pues para generar esta clave se
usa una contraseña y otras cosas que no mencionaré para no liarte (hay incluso un chiste
muy malo relacionado con esto :D). Por lo tanto a la hora de cifrar y descifrar se genera
la llave a partir de la contraseña que debería estar únicamente en tu cabecita.

Realmente existen sistemas de cifrado simétrico primitivos que no usan contraseña y son
claves que si se guardan en fichero. Pero en el mundo real y cuerdo se usa este método.

# ¿Y que hay del asimétrico?
En el cifrado asimétrico en cambio se usan dos claves, una nos sirve para cifrar y la
otra para descifrar. El algoritmo funciona de forma que no se puede usar la clave de una
de las dos funciones para realizar la opuesta. El esquema es el siguiente:

![Esquema cifrado asimétrico]({{site.url}}/assets/images/cifrado/esq_asimetrico.png)

El par de claves tiene unos nombres curiosos y voy a explicar el motivo:

* **Clave pública**: es la clave que se usa para cifrar los datos, el nombre se debe a
que lo común de esta clave es publicarlo o distribuirlo para todo el mundo sin miedo.
Aunque lo estes pensando no es una locura, la gracia está precisamente ahí, en que 
únicamente sirve para cifrar. Por lo tanto te da igual que cualquiera la tenga, para nada
le vale a alguien con malas intenciones cifrar algo y luego no tener acceso a ello.

* **Clave privada**: es la clave que se usa para descifrar los datos, es privada
precisamente porque solo tú deberías tener acceso a ella. Si alguien la obtiene date por
jodid@. Podrá descifrar cualquier cosa y hacerse pasar por ti (ahora hablamos de eso).

Si has entendido lo anterior comprenderás que no puedes intentar esto:

![Esquema fallo asimétrico]({{site.url}}/assets/images/cifrado/esq_asimetrico_fail.png)

Puede que hayas pensado aunque suene absurdo si es posible hacerlo al revés, al fin y al
cabo son claves, es decir un conjunto de bytes que usamos como parámetro y se puede meter
en el algoritmo ;). Si lo has pensado te mereces una galletita porque sí, de hecho aunque
suene absurdo se hace. Pero, ¿para qué queremos cifrar algo con la clave privada y qué
pueda descifrarse con la clave pública a la que tiene acceso todo el mundo?

¿Recuerdas lo de "hacerse pasar por ti"? Pues aquí vengo con esto. Como te he mencionado
a la clave privada solo tú tienes acceso (porque eres una persona responsable y la has
guardado estupendamente :p), por lo tanto si alguien puede descifrar esos datos con la 
clave pública significa que solo tú has podido realizar ese "cifrado" y se puede asegurar
que esos datos los has enviado tú.

A este procedimiento se le llama "firmar" y su esquema es precisamente este:

![Esquema firma]({{site.url}}/assets/images/cifrado/esq_firma.png)

En los algoritmos de cifrado asimétrico si que hay que guardar el par de claves, aquí no
funcionamos ya con contraseña como en simétrico. Vaya faena... 

Entonces, ¿cómo nos aseguramos de que no nos roben la clave privada? Pues aquí viene una
cosa que igual no te esperas. Vamos a hacer un poco de magia:

![Protección clave privada]({{site.url}}/assets/images/cifrado/proteger_privada.png)

Vaya, esto si que no te lo esperabas eh... Pues sí, resulta que donde creías que eran
rivales resulta que eran complementos perfectos. ;)

El fichero que contiene la clave privada se cifra con una clave simétrica y de esta forma
puedes guardarla de forma segura. ¿Recuerdas que la clave simétrica solo está en tu 
cabeza? :p

# Usos de cada algoritmo
Vale, ahora te estarás preguntando que porque no usamos siempre algoritmos de cifrado
asimétrico para cifrar absolutamente todo y limitamos el simétrico solo para asegurar su
clave privada. Verás, resulta que el cifrado simétrico tiene alguna ventaja sobre el
asimétrico, la mayor de ellas que es mucho más rápido cifrando. Esto se debe a que los
algoritmos simétricos suelen realizar operaciones de manipulación de bytes y los
algoritmos asimétricos están basados en cálculos complejos como por ejemplo calcular
números primos. Esto se traduce en mayor complejidad computacional, es decir, a tu 
procesador no le gusta la idea.

Ahora sabiendo esto, para que usar cada uno:

* **Algoritmos simétricos**: al ser más rápido se usa para cifrar grandes cantidades de
datos, en esencia discos duros, ficheros, etc... Sobretodo cosas en tu ordenador local.

* **Algoritmos asimétricos**: al poder distribuir la clave pública sin problemas por 
cualquier canal sin miedo a que obtengan la clave es perfecto para cifrar comunicaciones
sin comprometer la clave de descifrado.

# ¿Cifrar comunicaciones?

## El error de usar cifrado simétrico
En efecto, pensemos en el posible esquema de comunicaciones cifradas mediante algoritmos
simétricos:

![Comunicación con simétrico]({{site.url}}/assets/images/cifrado/ataque_simetrico.png)

Para poder establecer comunicación el equipo A con el equipo B necesitan compartirse la
clave de cifrado. Al pasarla por un canal inseguro (no cifrado) cualquiera que tenga
acceso a este canal podría estar vigilando los datos que pasan por este y al ver la clave
quedársela para poder descifrar los futuros mensajes.

Después de acordar la clave dará igual que cifres los mensajes, el espía ya tiene tu
clave y puede estar descifrando los mensajes también. Recuerda que tiene acceso al canal.

Por absurdo que parezca he escuchado a mucha gente plantear cifrar las comunicaciones con
algoritmos simétricos. :O

(Lo del canal de comunicaciones que parece un buffet libre para la NSA no es así 
realmente xD. Es un poco más complejo pero lo he simplificado para que se entienda 
mejor).

## Usando cifrado asimétrico

Aquí el esquema de como sería implementar una comunicación con cifrado asimétrico a
grandes rasgos:

![Comunicación con asimétrico]({{site.url}}/assets/images/cifrado/com_asimetrico.png)

Bien, con lo que hemos aprendido antes podemos ver el esquema para entender que aunque
aquí el canal sea inseguro y haya alguien intentando espiarnos podemos compartir la clave
pública con nuestro interlocutor sin ningún problema. Él nos mandará su clave pública y
podremos comunicarnos sin problemas ^^.

El equipo A usará la clave pública de B para que solamente él pueda descifrar el mensaje
y él hará lo mismo pero usando nuestra clave pública. Parece perfecto, ¿no? Pues no, aún
tenemos un pequeño problemita...

Y el problema es precisamente que no tenemos forma de saber si la clave pública que hemos
recibido pertenece realmente al equipo B. Por lo tanto alguien dentro del mismo canal
podría intentar engañarnos haciendose pasar por él y pasarnos una clave pública suya para
poder descifrar los mensajes con su clave privada. Aquí el esquema:

![Ataque MITM]({{site.url}}/assets/images/cifrado/ataque_mitm.png)

Este tipo de ataque se llama "Man In The Middle" 
([MITM](https://es.wikipedia.org/wiki/Ataque_de_intermediario)) y tiene otras 
aplicaciones dentro del mundo de las comunicaciones. Viendo el esquema puedes ver que un
falsante puede engañar a ambos equipos al mismo tiempo y encima puede hacerlo de modo que
ninguno de los dos se de cuenta de ello.

## Evitando el ataque MITM
Vaya, solo hay complicaciones, seguro que ya has desistido y no quieres jugar nunca más
con tu amiga la criptografía. Tranqui, existe una solución a esto último. Casi todo tiene
solución en esta vida. 

¿Recuerdas eso que queda por ahí arriba llamado firma? ¡Ves! al final todo tiene su 
motivo :D. Pues te dejo este diagramilla por aquí y te explico debajo la interpretación:

![defensa mitm]({{site.url}}/assets/images/cifrado/entidad_confianza.png)

Vaya, menudo jaleo, bueno a ver si lo explico bien... Realmente es difícil evitar que nos
hagan la suplantación, por lo tanto vamos a apostar por otra estrategia: asegurarnos de
que el certificado es legítimo y si no lo es pues cortamos comunicaciones (como el cuento
de los 7 cabritillos que pedían a su madre que enseñara la patita por debajo de la puerta
jajaj).

Bueno, al lío, para conseguir esto no hay más remedio que recurrir a que una tercera
persona en la que confiamos sea la encargada de firmar las claves públicas. De este
modo al recibir una clave pública ajena podemos comprobar si está firmada por alguien de
confianza. Para comprobarlo tendremos una lista de claves públicas de confianza ya
guardadas en nuestro equipo, al recibir la clave firmada podremos acceder a nuestra lista
y ver si concuerda con alguna de las que tenemos. Si no concuerda con ninguna descartamos
la clave pública recibida y cortamos comunicaciones por si acaso.

En este caso el farsante para poder engañarnos tendría que conseguir que alguien en quien
confiamos le firmara la clave pública. Pero nosotros no confiamos en cualquiera, ¿verdad?

> Es curioso como en la vida real las entidades de confianza suelen ser grandes empresas
> o multinacionales. Entidades a las que no les dejaría cuidar ni del cactus, pero así
> está montado el mundo. xD

## Ejemplos reales
El famoso "candadito" que dicen que tienes que mirar siempre al entrar a una web es a
grandes rasgos un certificado firmado por una de estas entidades de confianza que
contiene precisamente una clave pública para que puedas mandarle el mensaje cifrado.
Si eres un poco curioso puedes acceder a una web, pulsar sobre el candadito y ver el
certificado:

![certificado debian]({{site.url}}/assets/images/cifrado/cert_deb.png)

En este ejemplo puedes observar en la parte de arriba para quien se ha emitido y quién es
el que ha "firmado" el certificado: *Let's Encrypt*. Y si eres más curioso aún puedes
incluso ver su clave pública:

![clave pública debian]({{site.url}}/assets/images/cifrado/debian_clave.png)

Aquí un ejemplo de una clave pública que la habría firmado alguien en quien no confiamos:

![Entidad emisora desconocida]({{site.url}}/assets/images/cifrado/cert_desconocido.png)

Os dejo el enlace de la página de ejemplo abajo para que juguéis con ella :p.

En el navegador tienes un apartado en configuración con la lista de todos los
certificados que te vienen instalado con él. Si al acceder a una página no encuentra a la
entidad firmadora dentro de esa lista te lanzará un error y te impedirá acceder a la
página por tu propia seguridad. Así se evita mucho phishing en verdad.

El concepto de "certificado" ya es un poco más complejo que todo lo mencionado arriba, ya
que contiene entre otras cosas caducidad de la clave y otras cosas como checksum... pero
eso ahora no es importante. Quizás en un futuro (?).

# Conclusiones
Espero no haberte aburrido mucho en este post sobre comparaciones entre cifrados,
seguramente haga más posts sobre cifrado en el futuro porque es un tema que me encanta y
aún tengo mucho que decir sobre esto. Y posiblemente sobre algoritmos concretos.

Almenos ya deberías ser capaz de entender que diferencias hay entre cada uno de los dos,
si quieres profundizar en el tema ya deberías meterte a estudiar algoritmos del mundo
real. Hoy en día para cifrado simétrico el más conocido es AES-256 y para asimétrico
RSA. AES da para 3 o 4 posts si quisiera extenderme...

Si encontráis algún error de concepto, errata o pensáis que algo está mal explicado no
dudéis en mandar pull requests, contactarme por correo o twitter.

Nos vemos en futuros posts. ^^ ¡Saludos!


# Links de interés

* [BadSSL](https://badssl.com/): sitio con ejemplos de certificados inválidos.
