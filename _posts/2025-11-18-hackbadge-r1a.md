---
title: "Hackbadge: galopando tras el murciélago en la oscuridad"
description: "Desarrollando un firmware a contratiempo para un hardware que no tenía"
date: 2025-11-18 17:00
categories: hardware-hacking electronica
header:
  og_image: /assets/images/hackbadge_r1a/og_image.jpg
---
¿Cómo vamos gente? Este finde ha sido la AsturCON (.tech), a la que siempre es divertido
ir. Además, me tocó el sorteo de entradas + camiseta + badge con lucecitas, ¡nunca son 
suficientes leds!

Bueno bueno, esto no va de propaganda hacía la AsturCON (aunque ya estáis yendo 😡), es 
que alguno de mis dos lectores habrá leído el post que escribí hablando sobre 
alternativas al flipper zero y jugando con el cc1101. Aquello que yo acuñé de forma 
cariñosa como "radiogotchis" (si alguien quiere leerlo el post está [aquí]({{site.url}}/2025/02/26/invasion-radiogotchis)).

En el post aquel ya mencioné varias cosas, lo más importante era sobre cómo casi todas las
placas giraban entorno a usar el cc1101, que tendrá relevancia también en este post pero
no es el punto principal. También eché un vistazo a estas alternativas y ver, a groso 
modo, lo que ofrecían. Entre ellos había uno llamado "hackbat" diseñado por gente de mi
terreta (valencia <3), y dije que estaría bien en algún momento hacerse con uno y probar
cositas.

Pues resulta que en la asturcon, a la que acostumbro a ir, daban un taller en el que
enseñaban a usar kicad y, ADEMÁS, te daban un nuevo diseño de hackbat, con formato de
badge electrónico. Este tipo de badge ahora está bastante de moda en muchas CONs de 
ciberseguridad. Y por lo que ví, creo que la misma gente de hackbat tiene una empresa para
hacer badges, pero bueno el tema es, que quien me conozca sabe que yo quería ponerme 
algún día a aprender kicad, que eso lo he mencionado alguna vez. Cuando estudié
electrónica hace eones aprendí OrCAD, pero no me acuerdo de la mitad, solo de la teoría de
buenas prácticas y demás. Además, llevarse un cacharro siempre es bien. La cosa es que me
salió este tuit por la TL:

![tuit kicad asturcon]({{site.url}}/assets/images/hackbadge_r1a/tuit_taller_kicad.png "Tuit anunciando el taller de kicad en AsturCON")

Y obviamente tuve que apuntarme, que además era barato.

## 1. Ingeniería inversa stalkera
Todo esto está muy bien pero, ¿a dónde quiero ir con esto?

Pues en la asturcon anterior (les voy a desgastar el nombre) estuve viendo la charla de 
hackbat que presentó Pablo. Y claro, me interesó, primero por ser con hardware barato y 
luego por lo que he dicho de ser valenciano. Estuve los días posteriores viendo el
repositorio, la web, etc... Y como mencioné en el post de los *radiogotchis*, vi que el
hackbat no se proporcionaba junto a un firmware como tal (también lo dijo en la charla).
Que estaba más enfocado a darte el hardware y que te cocinaras el tuyo propio.

Así que pensé que un escenario posible era ese: que se nos proporcionara el cacharro sin
un firmware complejo. Que yo a favor, pero claro, quería tener algo ejecutando en el
momento de recibir el cacharro. Aunque fuese algo mínimo.

Antes de sacar conclusiones me fui a stalkear al pobre Pablo y ver si tenía algo 
publicado en internet respecto a este nuevo dispositivo, o algo en los anteriores que
me diera info. Pero no encontré nada como el nuevo, sólo que había escrito sobre un badge,
anterior a este, con parecido razonable [aquí](https://hackaday.io/project/197688-hackbat-badge).

Quitando lo curioso e interesante del post no conseguí encontrar código en ningún lado.
Por lo que pensé en ponerme a hacer algo propio, pero, ¿cómo programas un firmware
para un dispositivo que no tienes? Pues intentando replicarlo en una protoboard 😌.
Así que te pones a mirar las fotos del anuncio del taller, intentas encontrar más en el
twitter de la persona, reunes fotos e intentas sacar lo que necesitas.
 
 Primero las fotos del post del anuncio del taller:

![Frontal de hackbadge]({{site.url}}/assets/images/hackbadge_r1a/hackbadge_frontal.png "Imágenes hackbadge panel frontal")

Y yendo a stalkear a Pablo encontré esta foto que me dió pistas sobre la parte trasera:

![Back de hackbadge]({{site.url}}/assets/images/hackbadge_r1a/hackbadge_back_post.png "Imágenes hackbadge panel trasero")

Con esto ya podía saber qué módulos necesitaba, lo que no pude conseguir, porque en las
fotos no se ve bien, fueron los pines a los que estaba conectada cada cosa.

De pura suerte tenía de todo, en el caso de los leds neopixel fue casualidad, porque
compré varios hará un par de años. Y menos mal.

Teniendo los módulos me dispuse a asignar los pines a mi modo, que es usando el pinout
por defecto  del esp32-c3 en esta imagen que es de fiar:

![ESP32-C3 Super Mini Pinout]({{site.url}}/assets/images/hackbadge_r1a/ESP32-C3-Super-Mini-pinout-low.jpg "Pinout del ESP32-C3 supermini")

En resumen:
- SPI: del gpio 4 al 7
- I2C: tuve problemas con el pin 8 así que lo pasé al 10, el 9 lo mantuve.
- botones y neopixel (que van todos al mismo pin) pues un poco a boleo tbh...
- respecto al cc1101, los gdo también pueden ponerse a boleo. Pero he tenido problemas y
  los voy a ir comentando.

> ℹ️ Una cosa que tiene el esp32-c3 es que, normalmente, puedes reasignar spi, i2c y demás a
pines que te dé la gana, pero siempre está bien usar el estándar por si acaso.

He dicho que tuve problemas, el primero de ellos fue que no me cuadraba el número de 
GPIOs del ESP32-C3 con todo lo que había conectado. Para que cuadrase había dos formas de
hacerlo:

* los botones no eran independientes y usaban un mismo pin analógico. Lo que se llama
  "resistor ladder buttons": detectar que botón está presionándose en base al
  valor de tensión en la entrada analógica, que cambia porque cada botón tiene una 
  resistencia en serie con diferente valor del otro. Y es un rollo, ya que tienes que 
  meter a mano en el código rangos de valores para darle holgura, porque no va a dar
  siempre un valor exacto. Y con interrupciones de software se complica algo más la cosa.

* Prescindir del GPIO2 del cc1101 que se suele usar como flag de estado (o pulso 
  asíncrono en la recepción de señal), como ya enseñé en el post de los radiogotchis. 
  Esto es posible porque se puede configurar el gdo0 para ello, en vez de únicamente para
  la transmisión (y no al contrario, el gdo2 no se puede usar para cosas de Tx).

Me estuve comiendo la cabeza con qué opción estaría usando, y el hecho de que en la foto
del cacharro hubiera un pad de testing marcado como "gdo2" me hacía pensar que estaba 
conectado. Así que opté por lo que en los simpson llamarían mensaje superliminal:

![Duda sobre pines]({{site.url}}/assets/images/hackbadge_r1a/pregunta_sobre_gdo2.png "Preguntando en twitter sobre pines usados del esp32-c3")

¡Preguntar directamente a los creadores del taller! 😼 Muy majos los dos por cierto. Y con
esto acaba nuestra tarea de investigación, ahora a los bits y chispas.

## 2. El meta-prototipo del hackbadge
Sabiendo lo que vamos a usar y como lo vamos a usar es hora de conectar cosas:

![Protoboard con componentes]({{site.url}}/assets/images/hackbadge_r1a/prototipado_hardware.jpeg "Prototipo hardware del hackbadge sobre breadboard")

¿Habéis visto el cable management? mi mejor versión jaja porque no es la primera "versión",
váis a ver que no cuadra con fotos posteriores, pero olvidé hacer foto de la placa sin
encender.

Lo primero que hay que hacer es probar que conecte todo con código de mierda:

![Probando hardware]({{site.url}}/assets/images/hackbadge_r1a/probando_conexiones.jpg "Probando cableado del prototipo")

Y weno, le puse dos neopíxeles a diferencia del hackbadge, que tiene cuatro, pero que más
da, total van en serie. Podría poner 100, lo importante es que funcione. En cuanto a los 
botones, obviamente los probé con un `Serial.println()` y ya.

**Pequeño offtopic por si a alguien le es útil sobre la conexión del cc1101:**

Sobre el módulo cc1101 veréis que está pinchado de forma rara, y es que con el formato
que tiene no se puede conectar en una protoboard sin que hagan corto los pines. Ya que
están distribuidos en dos filas por 4 columnas. Usando mi post de [diseño de PCBs con el
paint]({{site.url}}/2024/11/12/paint-pcb) os comparto un adaptador que hice y llevo 
tiempo usando para casos como este:

![Adaptador 2x4 a 1x8]({{site.url}}/assets/images/hackbadge_r1a/adaptador_2x4_protoboard.jpg "Adaptador de 2x8 a 1x8 para protoboard")

Se puede hacer de otras formas, como simplemente manteniendo 4 en cada lado, pero usando 
espacio para pinchar el módulo justo en la parte central de la protoboard. Pero así puedo
tener la antena de forma vertical y es más cómodo.

## 3. Aventuras y desventuras cocinando un firmware propio
He de decir de antemano que en este post no voy a poner código, porque mi intención es
publicar el código de todo el firmware que vaya haciendo en [este](https://github.com/iordic/hackbadge_r1a_firmware)
repositorio. Aquí solo os contaré sobre mis peripecias, el estado actual y futuro de este.

Sobre el hackbat, a mi lo de que venga sin firmware me viene de lujo, porque una cosa que
siempre he querido es aprender bien a usar FreeRTOS para programar estos micros, y espero
que en un futuro otros como los STM32, que son baratos y europeos. Y este proyecto, como
usa varios módulos de forma simultánea, es el idóneo para usar un sistema como FreeRTOS 
para que funcione correctamente.

> Para el que no sepa que es FreeRTOS, es un sistema operativo muy básico que se encarga
  de planificar tareas, al igual que hace un sistema operativo de propósito general, con
  la planificación de procesos, dando la ventaja de prioridades y concurrencia.

Como ya expliqué en mi post, que he mencionado mil veces en este: existen muchos cacharros
con cc1101, muchas versiones y alternativas. Una buena opción es el firmware [bruce](https://github.com/pr3y/Bruce)
que está muy bien y, además, se puede meter en hardware barato y parecido al del hackbadge.
El problema de bruce es que no admite la pantalla de tan poca resolución del badge, lo 
bueno es que al ser esp32 también, se puede adaptar la funcionalidad que queramos (y 
relativamente fácil si sabes programar).

### 3.1. Preparación
Voy a explicar lo que conseguí desarrollar en mi protoboard a dos semanas vista del 
congreso, y yendo a conciertos y otras cosas entre medias. Así que no tengáis 
expectativas altas porque pude dedicar las horas que pude.

Bueno, ya hemos visto en el punto anterior que funciona, y en teoría es un badge de 
congresos. Y uno es informático, lo que caracteriza a un informático es la modestia y 
la falta de ego, por lo que el primer paso es hacer esto:

![Animación fantasmita]({{site.url}}/assets/images/hackbadge_r1a/phantom_animation.gif "Pantalla inicio badge con nombre y fantasmita")

Es broma pero no es broma, a ver, a mucha gente se le pasaría por la cabeza ponerse a
hacer cosas para intentar hacer maldades con el cc1101 u otras. Pero, como he dicho, ¡es
un badge! primero es de buena educación presentarse. Además se supone que es para
llevarlo colgando cual abogado.

Para la animación usé [este repo](https://github.com/L33t-dot-UK/U8g2_Tutorials) y me miré
los videos, yo no he hecho nunca animaciones como comprenderéis. Ya estaba dudando entre
usar esta librería (u8g2) o la de Adafruit, así que ver esto me ayudó a decidirme.

> Otro offtopic: el port de doom a arduino usa la librería de Adafruit y por eso no lo he
  adaptado a mi firmware.

Pero esto no queda aquí (obvio), puesto que también quería añadirle un menú y funciones,
para poder usar el cacharrín en cosas de RF, WiFi, Bluetooth y lo que se me ocurriese. 
Por lo que el siguiente paso es añadirle un menú, y esto suele ser un dolor de 
\*\*\*\*\*\*, así que busqué algo que conociera para adaptarlo.

Hace años probé un repo que era un cacharro con menú y ataques wifi usando esta
pantallita. Así que, como era lo que conocía y me convencía, fui a ese [repo](https://github.com/SpacehuhnTech/esp8266_deauther)
a ver si podía copiar fácil su código para que funcionase en el mío. Sé que habrá 
alternativas mejores pero es un código que ya había visto.

El menú original del repo que digo era así:

![esp8266_deauther menu]({{site.url}}/assets/images/hackbadge_r1a/esp8266_deauther_menu.jpg "Menú del esp8266_deauther")

Simple pero funcional, y el movimiento no era fino sino paginado: cuando bajas, después
del último elemento en pantalla, cambian todos los elementos a la siguiente página.
Eso no me gusta mucho, así que lo hice con iconitos y desplazamiento "fino". Y me quedó
así:

![Vista del menú]({{site.url}}/assets/images/hackbadge_r1a/menu_transitions.gif "Desplazamiento del menú del firmware")

De momento he decidido que pulsación larga es para volver atrás y simple para entrar, eso
igual lo cambio.

Todo esto usando ya FreeRTOS con tareas para la interfaz, otra para los leds y luego,
para lo que voy a meter por probar otras cosas, tengo tareas aparte. Que se resumen en: 
módulo de radio, wifi y los botones.

Para los botones, que mejor que probar un juego simple, así que me copié y adapté un snake
que usase la misma librería que estoy usando yo para el display. Ah y le metí créditos al 
autor, ya que estoy joseando hay que darle publi:

<video width="640" height="360" controls>
    <source src="{{site.url}}/assets/videos/hackbadge_r1a/test_snake.mp4" type="video/mp4">
    Parece que tu navegador no soporta la etiqueta de video :(
</video>

> Hmm, acabo de ver por el video que al perder se pone el score a 0, tendré que arreglarlo.

El juego tiene bastante input lag, y creo que es por la librería que uso para los botones,
que se llama OneButton y permite varios tipos de evento por botón, y debido a eso creo que
tiene ese retardo pero weno, funcionar fuciona. El repositorio del juego está [aquí](https://github.com/johnpathe/arduinoSnake).

Siguiente prueba: copiar algo sencillo del firmware de bruce que use el wifi y ver si lo
consigo hacer funcionar. He decidido para empezar, copiar el beacon spammer de rickroll:

![Beacon Spam Rick Roll]({{site.url}}/assets/images/hackbadge_r1a/wifi_spammer_rickroll.jpg "Ataque de beacon spam rickroll")

Como veréis, ni siquiera me he currado la interfaz, ya lo haré (e igual unifico cosas). 

Por último, lo que implemente es alguna cosa de radio, tenía que asegurarme de que funcionaba
el módulo, tanto al transmitir como al recibir. Para transmitir lo primero que uno hace
pues es ir a lo fácil, ¿no? a probar un jammer simple 🐱. 

> Que por cierto, como digo siempre: dejad de pensar que el cc1101 realmente sirve para 
  jammer con la poca potencia que emite...
  
Aquí demuestro que funciona:

<video width="640" height="360" controls>
    <source src="{{site.url}}/assets/videos/hackbadge_r1a/subghz_jammer.mp4" type="video/mp4">
    Parece que tu navegador no soporta la etiqueta de video :(
</video>

Y para recibir, de momento sólo probé que funcionaba y decodificaba señales usando la
librería RC-Switch:

<video width="640" height="360" controls>
    <source src="{{site.url}}/assets/videos/hackbadge_r1a/subghz_receiver.mp4" type="video/mp4">
    Parece que tu navegador no soporta la etiqueta de video :(
</video>

Otra vez una interfaz para salir al paso, ah, y mencionar que de momento la frecuencia y
la configuración son fijas. Y la configuración (AKA los presets) la he joseado
directamente del fichero de flipper zero. Porque sé que esos funcionan, y en un futuro
pueden incluso servir para leer más fácil los ficheros *.sub*. Si tenéis curiosidad, el
fichero que digo es este: [cc1101_configs.c](https://github.com/flipperdevices/flipperzero-firmware/blob/dev/lib/subghz/devices/cc1101_configs.c).
Y para interpretarlo es algo así como *Clave -> Valor* de cada registro por orden,
excepto la última parte de PA (son varios bytes para establecer la potencia de salida).

¡Y eso es lo único que me dió tiempo a implementar! Entre que soy nuevo con FreeRTOS, y
esto cuesta más tiempo de lo que parece. Creo que es suficiente para probar el cacharro 
al momento de recibirlo.

### 3.2. Se libera la info, últimas adaptaciones y errores inesperados
Pues tal cómo prometieron, unos días antes del evento publicaron el repositorio con el
proyecto de kicad del hackbadge. Y esto me venía de lujo porque así podía adaptar mi
código para que usara los pines correctos (y también recablear el prototipo). Y llegar al
evento e instalarlo en cuanto me lo dieran. Plug & play!

Me he encontrado con un problema que es bastante putada, y es que el hackbadge usa el
gpio 21 del esp32-c3 para el gdo0 del cc1101. Y, sin saber por qué motivo, el pin 21 de
esta placa en particular sólo sirve como OUTPUT y no como input (he leído por internet 
que es normal). Por lo que transmitir funciona bien pero recibir no.

La única solución que encontré de momento es reasignar los pines para usar el gdo0 en
otro gpio. En spi, el pin de mosi solo se usa como output, así que este era el ideal para
mapear al 21. Entonces intercambié esos dos: el de mosi por el del gdo0. El problema es
que no hay ninguna versión del cc1101, que vendan en aliexpress, que encaje de forma que
permita mapear los pines y pinchar directamente el módulo. Por lo que, 
recurriendo de nuevo a mi post de pcbs con el paint, hice un adaptador cutre:

![Intercambiador de pines casero]({{site.url}}/assets/images/hackbadge_r1a/pinout_reassigner.jpg "Remapeador de pines casero")

Esta vez no usé el paint, use el gimp y una libreta a lo cutre:

![Diseño mapeador]({{site.url}}/assets/images/hackbadge_r1a/diseno_reasignador.jpg "Diseño en cuadrícula del remapeador")

> Usando una libreta cuadrículada puedes saber como hacer luego las pistas si interpretas
cada cuadro como un pad de la placa cutre aquella.

Y llevé eso al evento esperando que funcionase. Recordad que aún no tenía el cacharro y
ya estaba pensando en el parche este. Otra cosa es que encajara, que viendo la foto ya
imaginaba que igual costaba, porque justo tiene el portapilas debajo.

### 3.3. Probando por fin el firmware en el hackbadge
Cuando lo recibí, me di cuenta de que usaba el driver SH1106, y la pantalla de mi 
prototipo usaba el SSD1306. Esto provocaba glitches en un lado de la pantalla, pero es 
fácil de arreglar, es cambiar una línea del código. La diferencia entre los dos drivers,
según el autor del u8g2, es:

![SSD1306 vs SH1106]({{site.url}}/assets/images/hackbadge_r1a/diferencias_ssd1306_sh1106.png "Diferencias entre el driver SSD1306 y SH1106")

También es un pelin más grande que la pantalla que yo usaba, lo cuál se agradece (1.3" vs
0.96", que parece poco pero se nota bastante, también se notan más los defectos jaja).

Por último, dejo dos videos demostrando que no me funciona la recepción sin remapear los
pines:

<video controls>
    <source src="{{site.url}}/assets/videos/hackbadge_r1a/demo_subghz_sin_remap.mp4" type="video/mp4">
    Parece que tu navegador no soporta la etiqueta de video :(
</video>

<video controls>
    <source src="{{site.url}}/assets/videos/hackbadge_r1a/demo_subghz_con_remap.mp4" type="video/mp4">
    Parece que tu navegador no soporta la etiqueta de video :(
</video>

Es un poco inconveniente pero funciona, ya pensaré algo mejor. De todos modos, decir que
aunque se hiciera pensando en el cc1101, se pueden llegar a usar para cualquier otro
módulo SPI o para cualquier otra cosa. Al fin y al cabo, si no pinchas el cc1101, son 
GPIOs de libre disposición.

Si alguien no lo tiene, y quiere pedirlo a jlcpcb o similares, creo que le recomiendo 
cambiar con el kicad esos dos pines antes.

## 4. Próximas implementaciones
Cómo habéis visto, de momento no tengo hecho prácticamente nada, y me gustaría tener algo
mínimamente funcional para sacar una release. Aún me faltan varias cosas para lo que yo
considero que puede ser la base, y en esta sección comentaré que ideas tengo.

### 4.1. Lo que necesito para considerarlo una primera versión
Tampoco quería hacer algo muy complejo, pero si que me gustaría conseguir hacer esto:
* Que el receptor vaya almacenando en memoria las señales recibidas. Y que cada una 
  tenga, al igual que en flipper y similares, un menú con estas opciones: hacer replay,
  guardar en la eeprom o descartar la señal.
* Una app para transmitir las señales que tenemos guardadas desde el punto anterior.
* En el menú principal, en configuración, poder elegir la frecuencia entre las 4 
  principales que se usan para propósito general: 315 y 915 en EEUU, y 433.92 y 868 en UE.
* También en el menú de configuración: poder cambiar el brillo de los neopíxel, el
  comportamiento de aleatoriedad, velocidad con que cambian y otras cosas que se me
  ocurran...
* Tengo que cambiar la pantalla principal de animación (a lo que llamé splash) por una
  que no sea mi avatar moviéndose. Algo como un murciélago supongo. Y que la gente pueda
  poner su nombre y pseudónimo.
* Que los neopíxel hagan consas concretas al realizar transmisión, recepción, ataques,
  etc... Por ejemplo: el deauther al atacar se ponía rojo y al recibir azul.

La mayoría de esas cosas no deberían ser difíciles de hacer, pero se puede entender que,
a lo tonto, es bastante curro. Intentaré ir haciéndolo poco a poco sin perder la cordura.
Desde luego ni Lovecraft pudo preveer al monstruo del desarrollo.

### 4.2. Ideas sueltas para un futuro menos próximo
Aquí ya os dejo ideas que he tenido, y que puede (o no) que intente desarrollar en un
futuro, pero no es algo a lo que daré prioridad hasta que no haga lo del punto anterior:

* Aprovechar los pines i2c de la pantalla para otros módulos. Por ejemplo, estaría chulo
  conectarle un RTC para mostrar el tiempo sin tener que conectarse a internet. He 
  pensado en sustituir los pines por unos hembra, con las patillas largas como tenía el 
  nodeMCU, de modo que las conexiones hembra queden por atrás como las del spi: ![pinheader extra largo]({{site.url}}/assets/images/hackbadge_r1a/pinheader_extra_largo.png "pinheader extra largo")
* También estaría guay tomar la idea de badge y del networking y darle una vuelta. He 
  pensado en algo así como automatizar el networking, que la gente se cuelgue el badge y
  entre ellos se manden la info de contacto o cualquier chorrada, esto se puede hacer con
  el protocolo ESP-NOW, que tienen los esp32, sin depender de wifi en modo broadcast.
* Ponerle un panel de administración web para configuración más compleja o que requiera
  teclado, porque poner teclado en la UI es algo que no pienso sufrir jaja y ponerle OTA
  también, que es fácil.
* Obviamente copiarle más código al firmware de Bruce jajaja

## 5. Conclusiones
La verdad que cuando pensaba en escribir este post, y cuando empecé a hacerlo, no
esperaba extenderme tanto. Ya lo siento pero bueno, así plasmo todo lo que ha sido el
percal entorno al cacharrín. Dejaré abajo el enlace al firmware, si me cotilleáis iréís
viendo mis cambios, espero que no acabe en sideproject medio abandonado hasta que no
consiga algo más funcional jaja

Muchas gracias a Sara y Pablo, los creadores del hackbadge, por la infinita paciencia de
aguantarme en twitter preguntando y demás. La verdad que fueron muy majos también en
persona. Les dije que subiría el firmware para que pudieran verlo, y eso haré, aunque
era ya mi idea desde un inicio.

Por lo que vi allí en el evento, había gente que quería algún firmware ya hecho para poder
probarlo. Uno si que me preguntó que si era difícil programarle cosas y le dije lo
que diría un gallego (aunque yo sea valenciano): depende. ¡Pero esa es la actitud que hay
que tener! Esto es como la coña esa que dicen siempre de "los amigos que hacemos por el
camino": lo divertido no está en tener el cacharro y usarlo para "hacer el mal", al final
siendo realistas tampoco es que sirva para hacer demasiadas maldades (y lo mismo os digo
del flipper zero). No te quedes en usar los botones, mira el código, aprende, entiende
cómo funciona, modifica cosas a tu gusto. Y cuando veas que algunas cosas que haces de
repente funcionan, a lo mejor pensarás que son una mierda, pero serán TU mierda.

Nos vemos en el siguiente post, espero que a alguien le haya gustado. Si has llegado
hasta aquí gracias por leerme, ¡hasta pronto! <3

# Enlaces de interés
* [El repositorio de mi firmware](https://github.com/iordic/hackbadge_r1a_firmware)
* [Web de la AsturCON](https://asturcon.tech/)
* [Repositorio del badge de hackbat](https://github.com/thebadg-es/hackbadge_r1a)
* [Repositorio tutorial para hacer animaciones con el oled](https://github.com/L33t-dot-UK/U8g2_Tutorials)
* [Repositorio esp8266_deauther de dónde copié parte de la lógica del menú](https://github.com/SpacehuhnTech/esp8266_deauther)
* [Repositorio del firmware Bruce](https://github.com/pr3y/Bruce)
