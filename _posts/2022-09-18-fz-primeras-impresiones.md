---
layout: post
title:  "Primeras impresiones del Flipper Zero"
description: "Opinión personal sobre el flipper zero"
date:   2022-09-18 17:30
categories: electronica
---
Como algunos sabréis estos últimos días me llegó el Flipper Zero. Yo personalmente hacía
tiempo que le tenía el ojo echado, ya no por todo el tema de ciberseguridad cómo tal,
sino por el concepto en sí de tener todos esos módulos de electrónica juntos en un
formato cuqui. Después de trastear, este humilde señor, os va a decir que impresiones se
ha llevado del cacharro.

## 1. ¿Qué es el Flipper este?
Es básicamente un cacharro portable y embebido que cuenta con un microcontrolador STM32.
De todos modos la imagen de su [propia web](https://flipperzero.one/) lo resume perfecto:

![Vista explosionada del Flipper Zero]({{site.url}}/assets/fz_primeras_impresiones/explosion-singed-200.png "Vista explosionada del Flipper Zero")

En resumen:
* Módulo de **radiofrecuencia** para bandas por debajo de 1 GHz (aquí lo típico 433 MHz).
* Receptor y emisor de **infrarrojos**.
* **RFID**: NFC y 125 KHz (el segundo no sé ni en qué casos se usa, ¿chips de mascotas?).
* **iButton**: sinceramente sigo sin saber para qué se usan ni creo que llegue a hacerlo
  pero al menos ese pin se podría llegar a usar para otras cosas :).
* **GPIO**: pines de entrada y salida al microcontrolador con diversos protocolos.
  Básicamente, los pines que sobran después de tanto módulo instalado, pero darán (matiz
  importante) mucho juego aunque sean pocos pines: i2c, spi, uart, ADC, etc. Lo típico
  vaya. Todo en 16 pines que acaban siendo 11 "útiles" quitando alimentación.
* Esto no lo anuncian pero se puede usar como **bad usb** y la verdad que no va nada mal.

A todo eso se le suma una carcasa bonita y una mascota graciosa. Y batería para aburrirte
del delfín antes de que se agote. También trae motor de vibración y altavoz para hacerlo
mas fancy, de hecho ya hay gente poniéndole musiquitas y eso con reproductor.

## 2. Opinión personal
Aquí ya empiezo a desvariar con lo que yo he visto y pienso. Mucha gente quiere comprarlo
porque con tanto hype piensa que el cacharro será algo cómo esto (dramatización):

![Guante de hackerman de la peli Kung Fury]({{site.url}}/assets/fz_primeras_impresiones/power_glove.jpeg "hackerman power glove")

Es decir, un dispositivo todopoderoso que los "pentesters" van a usar para hackear todos
los dispositivos del mundo, algo así como el anillo único de la electrónica. Luego hay
otros que lo critican simplemente porque está de moda y tienen que demostrar que ellos
están por encima a base de rage puro. Sea cómo sea voy a intentar ser objetivo desde mi
nivel de conocimientos, que tampoco soy aquí master of electrónica ni mucho menos.

Para ponernos en contexto, decir, que yo compré el cacharro con la funda y aparte el
módulo wifi que venden (esto luego lo explico).

Lo primero que voy a decir un poco fuera de contexto es que el bicho tiene agujero de
estos que tenían nuestros antiguos móviles para colgarles un colgante a través de un hilo.
A mi eso me parece de uso obligatorio, no concibo la comodidad sin poder cogerlo de ahí,
si te pillas uno comprate una correa del aliexpress (que no lo trae por desgracia). ¡Mirad!:

![Colgante para el Flipper Zero]({{site.url}}/assets/fz_primeras_impresiones/colgante_fz.png "flipper zero con colgante")

Yo lo cogí prestado de una powerbank que tengo por ahí estropeada (intuyo que con correa
sea más cómodo aún). En fin, vamos a lo importante, voy a hacer subapartados para que sea
más fácil de agrupar. Ah sí y, ¡el tamaño en fotos engaña eh! Es un poco más grande en
persona.

#### 2.1. El módulo Sub-GHz
Lo que más atrae a la gente es el módulo de
radiofrecuencia para trolear a dispositivos como garajes o controles a distancia que aquí
suelen ser a 433 MHZ. O lo que hemos visto que hacía gracia: 
[abrir la tapa de carga de un Tesla](https://twitter.com/0xamit/status/1519402881358643202)
o [esto](https://twitter.com/xenobyte_/status/1554222170888339456) :O. Aunque esto último
a mi me parece un poco putada para los trabajadores, ¡no jodáis a los trabajadores! ;(

Solo lo he probado con mandos que tenía por casa para controlar cacharros, para mandos de
garaje aún no he probado. Tiene un programa para leer las señales y no he sido capaz de
que los coja. Únicamente grabando la señal en modo Raw y luego con el firmware este que
desbloquea las frecuencias de otras regiones también en Read y poniendo la opción de Raw
que te guarda solo lo necesario.

Las cosas con clave o cifradas no las puede guardar si no me equivoco. Aunque luego hay
toda una comunidad detrás que son más piratas que barba negra y están subiendo cosas para
hacer fuerza bruta y protocolos con rolling codes, pero del tema aún se poco.

En resumen: ta bastante verde la cosa, no sólo en esto.

#### 2.2. El BadUSB
Se supone que tiene compatibilidad con los scripts del rubber ducky de Hak5. Yo probé el
ejemplo que venía en el firmware oficial en Windows y la verdad que sin problemas. He
escuchado a gente decir que daba problemas por el layout de teclado y creo que en
firmwares extraoficiales he visto que tenían configuraciones para cambiar entre layouts
(?). No sé, no he vuelto a trastear con eso, no me interesa demasiado aunque supongo que
tiene cierto potencial (aunque como me dijeron, esconder el flipper en un PC pinchado
detrás con lo grande que es pues como que no). Poco más que añadir, no necesita drivers
como me paso con el chino basado en Attiny85 que es una porquería (creo que hay mejores).

#### 2.3. El módulo de IR
También voy a decir poca cosa, puedes guardar señales y reproducirlas o bajarte nuevas e
internet. Incluso la función de mando universal acaba colando en una TV tirandole hasta
que enciende. Pero nada nuevo, esto lo podías hacer con los móviles Xiaomi de hace 10
años que venían con IR.

#### 2.4. El GPIO
Hmm... Aquí he de decir que me siento un poco engañado eh, en la web pusieron esto que me
pareció interesante, cito textualmente:

>  It can also be used as a regular USB to UART/SPI/I2C/etc adapter.

Qué cachondos, claro que puede, desde el punto de vista que el hardware lo permite pero
el firmware no lo trae implementado de momento para SPI ni I2C jajaj. Pero en su defensa
diré que está pendiente según se puede leer en uno de los
[issues](https://github.com/flipperdevices/flipperzero-firmware/issues/1038) (¡es
importante empaparse en github!).

**¿Por qué me parece interesante?**: Si has trasteado lo suficiente con Raspberry Pis o
similares y sistemas Linux, sabrás que incluso los puertos estos están disponibles a
través de un fichero dentro del directorio /dev. De hecho en otros posts he hablado de
usar módulos i2c desde linux, que te da herramientas para poder interactuar con estos.
Pues la putada ahora mismo es que para probarlos tienes que usar una raspi, conectarle el
módulo y luego por ssh estar probando a ejecutar y compilar el programa. Si el usb a i2c
de flipper se implementara, permitiría pinchar el cacharro por usb y que Linux te creara
una interfaz i2c en tu PC de mesa o portátil en la que poder interactuar directamente. A
mi me gustaría bastante, y para SPI idem.

En fin, respecto al uso de GPIO, el firmware release solo trae la opción de usarlo como
USB a UART o probar los pines manualmente: esto es, poner a HIGH el pin seleccionado en
la interfaz. Pero programando se podría meter cualquier cosa de la que los pines sean
capaces, de hecho hay un PR pendiente de mergear que permite usar los pines para escanear
direcciones de dispositivos i2c conectados [aquí](https://github.com/flipperdevices/flipperzero-firmware/pull/1431).
De hecho hay otros custom firmware se lo han traído y empaquetado porque es bastante
fácil.

Por el resto, los contactos mecánicos se nota que son de calité al pinchar los dupont.

#### 2.5. NFC
Apenas lo he tocado, me parece interesante la opción de detectar lectores y creo que en
algunos CFWs hay o quieren que haya app para crackear tarjetas con secciones protegidas.
Si esas cosas funcionan estaría guay.

#### 2.6. Sobre el módulo wifi
El problema es que soy tonto y no leí ni para que era el módulo wifi, lo compre sin más
pensando que le daría capacidades de conectarse al wifi al dispositivo. Por si alguien me
lee antes de comprar voy a intentar explicaros para qué sirve, también podéis leer la 
[documentación oficial](https://docs.flipperzero.one/development/hardware/wifi-debugger-module). 

Es un debugger adaptado al formato del Flipper con el firmware *Black Magic* que por lo
visto es conocido y se usa para debuggear diferentes CPUs y micros. Entiendo que se puede
usar para debuggear otros dispositivos como la propia documentación indica y también
usarse como USB a UART (otro más de los miles que tenemos tirados por casa, además que el
propio flipper también lo hace por separado jaja).

Pero bueno, yendo a lo importante, no se puede usar para pentesting ni nada similar. Para
eso no hay nada oficial, pero han adaptado dos cosas que ya existían para trabajar con el
Flipper a través del GPIO:
* [Marauder](https://github.com/justcallmekoko/ESP32Marauder/wiki/flipper-zero): este no
  lo conocía pero le veo mucho potencial y posiblemente me monte uno.
* [ESP8266 Deauther](https://github.com/SequoiaSan/FlipperZero-Wifi-ESP8266-Deauther-Module):
  este si que lo conocía, no tiene gran utilidad más allá de ir echando a la gente de su
  propia red wifi con ataques de deauth wifi. Lo bueno es que es fácil de usar y montar,
  de hecho creo que se podría hacer mejor formato que el del repositorio este. En su día
  monté uno casero con un nodemcu que tenía por casa y lo regalé a un amigo:

  ![ESP8266 deauther casero]({{site.url}}/assets/fz_primeras_impresiones/nodemcu_deauther.png "ESP8266 deauther casero")

  La adaptación de este bicho al flipper te quita la parte de tener que buscarte la vida
  con ponerle botones, pantallita y batería usando las del flipper, y la verdad que es de
  agradecer (es lo más incómodo). Además creo que se podría montar sobre un ESP12 y
  usando los 8 pines consecutivos que vienen en la parte derecha en vez de cómo lo hace
  el señor del repositorio, así podrías tenerlo en el mismo formato que el debugger tipo
  "plug & play" pinchandolo y ya. Y más importante, dejando libre toda la sección
  izquierda. Si me caliento igual lo intento, que lo difícil es el firmware y está hecho.

**¿Recomiendo el módulo?** Si te sobra la pasta nunca está de más pillarlo, al final te
da la opción de debuggear si en algún momento te da por hacer una app del flipper. Y
también te da la opción de poder subir el firmware por ahí aunque se quede el cacharro
moñeco (relatable).

### 2.3. Interfaz y firmware
Lo primero que voy a criticar es que la mascota tiene un nivel como un intento cutre de
intentar imitar la gamificación que metieron en el pwnagotchi pero en este caso yo la veo
completamente inútil. Yo no sé ni cómo sube de nivel ni lo pone en la documentación, creo
que si sube de nivel aparecen diferentes escenas y frases en el personaje pero es que si
sabes ni cómo sube, motivación 0. He visto que se puede cambiar el nivel a mano incluso
pero bueno, sin más. Si no sabes lo que es el pwnagotchi ya estás tardando en buscarlo,
te dejo el link abajo.

En cuánto a las aplicaciones si te instalas el firmware *release* aún no se pueden subir
de forma externa por la microSD, en el firmware de desarrollo sí pero no te prometen
estabilidad. Y es bastante incómodo tener que recompilar y reflashear el firmware entero
para añadir una app nueva, aunque esto pronto lo meterán en el estable. Yendo a releases
se puede leer lo que quieren sacar en las 
[siguientes versiones](https://github.com/flipperdevices/flipperzero-firmware/releases)
(en Ongoing).

La documentación aún está en bragas, no hay ni la mitad de cosas escritas y es una pena
porque parte de lo que falta es la sección de desarrollo. Que ahora mismo es la más
importante para que la gente ayude. De todos modos te recomiendo encarecidamente mirar en
el repositorio del firmware de GitHub (lo pongo en la sección de debajo), sobretodo si te
has comprado el Flipper, ya que has sido impaciente pues al menos empápate un poco con la
evolución del delfinito. Y aunque los de cibersec os digan que no hace falta saber
programar no os va a quemar la vista leyendo un poco de código de vez en cuando. :)

Además leed los issues y pull requests de la comunidad, veréis todo tipo de ideas y
mejoras que pueden llegar a ser interesantes.

Sobre el firmware si no me equivoco usa FreeRTOS ([aquí](https://github.com/flipperdevices/flipperzero-firmware/issues/17)
una discusión que tuvieron sobre el tema de escoger) así que es un buen momento para
intentar aprender a programar aplicaciones para este sistema, al menos yo voy a
intentarlo. Se usa mucho en dispositivos embebidos y es opensource. Además luego te sirve
para el esp32 si te hace falta.

Y acabando con este punto te recomiendo que no te limites al firmware *release* que se
supone que es el estable, porque si la rama develop esta verde no te quiero ni decir esa.
Partiendo de uno de estos firmwares puedes añadir cosas de otros y recompilar o
empaquetar las apps y subirlas a la microSD si son externas (eso es harina de otro costal
que no voy a explicar aquí).

Además puedes ir al repositorio de Awesome Flipper, lo dejo en los enlaces, es el
repositorio favorito de la gente para encontrar recursos sobre este. En la lista de
firmwares te recomiendo el primero: *Unleashed*, el *RogueMaster* me pareció una
porquería con tanta gilipollez personalizada de dragon ball por la pantalla y el nombre
del firmware en vez del delfín. De verdad que he visto malwares más respetuosos con el
usuario...


## 3. ¿Merece la pena?
TL;DR: realmente no, el precio que tiene es bastante elevado para la utilidad que puedes
llegar a darle hoy en día. Entiendo que los componentes son de calidad y hay todo un
proceso complejo de desarrollo detrás para justificarlo, pero si pretendes algo
"productivo" creo que tienes que esperarte a que esté más maduro. Si no recuerdo mal, leí
que quieren que esté bastante más completo y usable allá por la versión del firmware 1.0
y ahora mismo están por la 0.6. Además, si no te gusta la electrónica te lo recomiendo
menos aún, no te vas a querer "ensuciar" las manos.

Y a mi se me ocurre por ejemplo, aunque nunca lo he comprado ni usado, que un M5Stack
podría llegar a ofrecer las mismas prestaciones y además contaría con wifi por una
tercera parte del precio del Flipper Zero aunque tendrías que buscarte la vida con los
módulos como subghz, nfc, ir y demás. Pero también funciona (o al menos puede) con un
sistema RTOS, por lo que podría ser igual de completo pero menos fancy. Aunque yo lo
compré con toda la ilusión del mundo oiga.

En fin, si has leído hasta aquí espero que te haya parecido fructífero, yo intentaré
disfrutar del delfincito y darle caña. Estoy hypeado pero creo que me sacará de la zona
de confort para muchas cosas que quería aprender y esto las aglutina.

### Links de interés
* [Página web oficial](https://flipperzero.one/)
* [Documentación](https://docs.flipperzero.one/)
* [Repositorio del firmware](https://github.com/flipperdevices/flipperzero-firmware)
* [Black Magic debugger](https://black-magic.org/)
* [Awesome Flipper](https://github.com/djsime1/awesome-flipperzero)
* [Web de pownagotchi](https://pwnagotchi.ai/)
* [Rubber Ducky de Hak5](https://shop.hak5.org/products/usb-rubber-ducky)
* [M5Stack](https://m5stack.com/)