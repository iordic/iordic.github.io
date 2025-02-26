---
title: "La invasión de los Radiogotchis"
description: "Entendiendo y experimentando con el módulo cc1101"
date: 2025-02-26 17:00
categories: electronica radio
header:
  og_image: /assets/images/invasion_radiogotchis/og_image.jpg
---
Buenas gente, como dice el dicho, año nuevo post nuevo. Ahora pensaréis: ni que fuese aún
enero para hablar de año nuevo. Me propuse hacer almenos un post mensual y, ¡juro que lo
intenté! Pero no paran de salir contratiempos, estoy a punto de abrir un puestecillo en
la calle para vender limonada...

¿Qué mierdas son los "radiogotchis"? Pues no sé, me acabo de inventar la palabra, pero
creo que todos nos hacemos una idea: no paran de salir por esporas diferentes cacharrines
que se autodenominan "alternativa a flipper zero" y, si nos pasamos de sensacionalistas,
hay hasta flipados que lo llaman "el nuevo cacharro que viene a destronar a flipper 
zero". Y es que ya hay todo tipo de bichos: capibaras, cuervos, murciélagos, delfines, 
gatos, willy..?, por eso se me ocurrió lo de radiogotchis. Y los medios de comunicación
siempre van a aprovechar el hype para rascar visitas:

![Alternativas flipper zero]({{site.url}}/assets/images/invasion_radiogotchis/flipper-zero-alternatives.jpg "Titulares sobre alternativas a flipper zero")

Aunque la realidad está muy alejada de ello. He de decir que para nada es hate, a mi me 
encanta que salgan tantas alternativas porque la mayoría son FLOSS y a mi me encanta 
cotillear por el GitHub. Creedme que no es sólo que sea friki y solo me interesen las 
cosas intelectuales ni nada así, si te pasas por los issues de muchos de estos proyectos
parece que estés viendo una telenovela y te aseguro que muchas veces te vas a reír.

Para comprender por qué no se puede hablar de alternativas a flipper tan solo hay que ver
cómo está montado el firmware que lleva éste y el resto de cacharros. El flipper cuesta
4-5x el precio de las alternativas, pero es que el diseño de todo está muy bien hecho.
Hasta el famoso portapack, que es infinitamente superior al flipper en cosas de radio, 
tiene un firmware que no le llega a la cintura... Y si hablamos de diseño de la parte de
hardware estamos en las mismas.

Por cierto, dos de estás alternativas son españolas, el EvilCrowRF y el hackbat, que
es además valenciano (como un servidor <3).

En fin, el por qué hablo tanto del flipper zero es por la característica que comparte con
sus imitadores y voy a explicar ahora: el chip integrado que usan para el tema de 
radiofrecuencia.

## 1. ¿Tiene sentido prohibir el flipper zero?
A poco que hayas estado enterado de la trayectoría de flipper zero sabrás que ha pasado
por varias dificultades para distribuir los cacharros. Les pilló la guerra de Rusia y el
equipo está formado por rusos, por lo que las trabas ya fueron grandes de inicio. Luego
se hizo más viral el caso de que Canadá (y no recuerdo si más países) prohibieron su
venta porque "se podían robar coches con él". Creo que me perdí el tutorial en el que se
usaba el GPIO del flipper zero para hacerle un puente al coche... Yo soy de la opinión de
que los políticos no tienen que saber de cosas técnicas para legislar, pero joer, que
precisamente tienen técnicos detrás para asesorarles en estas cosas. Y en este caso hay
dos opciones: o el político de turno narcisista no ha hecho ni puto caso al técnico, como
es habitual, o el técnico es más incompetente que un humano con empatía haciendo de 
recursos humanos.

También es verdad que no ayuda el hecho de que salgan cuatro frikis a los que ni les
gusta la electrónica, el hacking, ni nada más allá de los likes de redes sociales, a hacer 
videos en los que dicen robarle la silla de ruedas eléctrica a tu abuela con el flipper 
zero. Bruh, que el flipper no es barato, ¿lo has comprado para hacer vídeos haciendo el 
parguela? QUE EN MUCHOS HE LLEGADO INCLUSO A VER LA PANTALLA APAGADA O CON EL MENÚ 
PRINCIPAL MIENTRAS "HACKEABAN". Perdón, en este post estaré especialmente faltoso. A lo
que íbamos: ¿tiene sentido prohibirlo? Para responderos tenéis a los del equipo de
flipper zero en su [respuesta al gobierno canadiense](https://blog.flipper.net/response-to-canadian-government/)
(os recomiendo el video del final del post, está curioso).

En verdad, el flipper fue el cacharro que empezó a despertar mi curiosidad por la 
radioafición, cosa que le agradezco. Y también la de mucha gente hacía el hacking más
allá de lo puramente software, decantándose un poco hacía el hardware. Lo bueno de esto 
es, que ahora mucha más gente tendrá interés hacía la electrónica.

Y es que, es verdad que con el flipper zero han empezado estas modas de los cacharrines 
tamaño bolsillo, de joder la marrana por radiofrecuencia. Pero ni mucho menos es una 
novedad, simplemente ha captado el interés de más gente al ser tan vistoso y fácil de 
usar. Por ejemplo, hace bastantes años ya salió este proyectín de un hacker famoso que
modificó un juguete para realizar ataques sobre puertas de garaje (de código fijo):

![Open Sesame]({{site.url}}/assets/images/invasion_radiogotchis/open-sesame.jpg "Dispositivo Open Sesame")

Cuando lo ví hace tiempo me fascinó, y más que tenía cero idea de estas cosas. ¿Vas a 
prohibir un juguete por si acaso alguien lo modifica para abrir garajes y robar?

La cosa es que casi todos estos cacharrines que están saliendo de debajo de las piedras
usan el chip cc1101 de Texas Instruments (flipper zero incluido), un chip transceptor de 
RF que modula señales digitales a diferentes tipos de señales en (AM, FM, ...) en un gran
rango de frecuencias en MHz (banda UHF para los radioamiguis). Pensaréis que es porque 
este chip es tecnología puntera o algo así, la verdad es que es simplemente porque es 
versátil y barato. Tiene bastantes limitaciones también. Este post va a ser un poco raro,
un batiburrillo de ideas y pruebas con este módulo. Así que allá vamos.

## 2. Comprendiendo un poco al CC1101
No voy a entrar en profundidad en todos los detalles técnicos de este chip porque 
básicamente no puedo, la documentación es extensa y tiene muchas cosas que ni sé. Dejo el
datasheet en la sección de enlaces por si queréis bichear, yo lo he hecho y ayuda a
comprender cosas del código. Pero vamos, personalmente no pienso estudiarme el datasheet 
y no voy a saber más allá de lo que he aprendido por leer código/experimentación. En este
blog nos (semi)rebelamos contra el RTFM, no estudiaba ni en la carrera voy a hacerlo para
escribir esto... Como dijo un sabio:

![tuit vx-underground]({{site.url}}/assets/images/invasion_radiogotchis/tuit-vx-underground.jpg "Tuit de coña de Vx-Underground")

Como he mencionado antes, el CC1101 es un chip transceptor (recibe y emite) de señales
digitales para la banda UHF en diferentes modulaciones. Muchas palabras lo sé, ¿qué
mierdas significa esto? Pues que "convierte" 0s y 1s digitales en señales de radio. Y que
derivado de lo anterior puede mandar información contenida en bytes modulando bit a bit.
Importante destacar que solo modula señales digitales, nada de señales analógicas cómo
sería la modulación de voz humana en FM que hacen los walkies convencionales, he aquí la
primera gran limitación de nuestro amigui.

Sus características a grandes rasgos son:
* Trabaja en los rangos de frecuencias: **300-348, 387-464, 779-928 MHz**. Lo suelen 
  llamar subghz por ello, porque trabaja en bandas por debajo de 1GHz, pero si creías que
  lo hacía en todo el abanico de 300 a 928 te equivocabas, tiene partes ciegas (como tu
  intentando recordando la noche del viernes) y con ello tenemos la segunda limitación.
* Modulación posible en: **ASK/OOK, 2-FSK, 4-FSK, GFSK y MSK**. Esto puede sonar a chino
  (y así es pero quédate con que son AM el primero y el resto derivados de FM). Los 
  manditos más simples usan generalmente OOK porque es barato y fácil de implementar.
* Permite mandar señales de forma síncrona o asíncrona. Y esto realmente es algo de lo 
  que va a ir este post y las pruebas que haga.

### 2.1. ¿Señales digitales, analógicas, qué?
Cuando digo que no es capaz de modular voz ni señales analógicas me refiero tanto a 
emitir como recibir señales de este tipo (no escapaz de demodularlas). Los de flipper
zero se han empeñado en intentar escuchar señales de walkies pero esto funciona de 
aquella manera, y no lo digo yo, [ellos mismos lo avisan](https://docs.flipper.net/sub-ghz/read#9w8q-).
Te dicen que grites al walkie para poder escuchar algo, aquí os dejo mi prueba si queréis
juzgar vosotros mismos si sirve realmente:

<video width="640" height="360" controls>
    <source src="{{site.url}}/assets/videos/invasion_radiogotchis/flipper-zero-walkie.mp4" type="video/mp4">
    Parece que tu navegador no soporta la etiqueta de video :(
</video>

Algo se entiende, digo "probando probando". Lo he probado en otra habitación a unos 10m 
con el baofeng en potencia baja para no pasarnos. He probado a decir "con los dedos de 
las manos..." y no se escuchaba nada así que dejo este.

> ¡AH SI! Antes de seguir, y por motivos legales, decir que todo esto lo he grabado en un
caja de arena de faraday (soy un gato). Y que si en algún momento véis en videos que hay
frecuencias y señales raras que no se ajustan mucho a la normativa: es solo vuestra 
imaginación y estáis todos locos.

### 2.2. Sobre el desbloqueo del rango de frecuencias
Si tenéis el flipper zero seguro que habéis instalado o escuchado que hay que instalar un
firmware alternativo porque "desbloquea todo su potencial" pero, ¿es verdad esto? ¿puede
romper el cacharro?

Cuando dicen lo del "desbloqueo regional" se refieren a que hay frecuencias en las que
puede transmitir el CC1101 pero está prohibido por normativa (y muy castigado).
Más arriba he dicho los rangos de frecuencias en los que trabaja el chip, pero el equipo
de flipper zero limita (por software) las frecuencias en las que trabaja para poder pasar
la normativa europea y poder venderse legalmente (exacto, esos hilos de twitter pesetas
generados por IA, diciendo "productos ilegales que aún puedes comprar en Amazon 🤪" son 
tremenda gilipollez sensacionalista).

* **¿Qué dice la normativa europea?** Que las frecuencias de uso libre (no hace falta
   licencia) para cacharros mierdosos son 433.92 y 868 MHz únicamente. En USA: 315 y 915,
   por lo que según tu región bloquean un rango u otro.

* **¿Significa que puedes emitir cualquier cosa en esas dos frecuencias?** NOOO, aparte
  de eso se especifica la potencia máxima (muy baja, creo que hablamos de milivatios) y 
  qué usos se permiten.

Lo que hacen los firmwares no oficiales para desbloquear esto es modificar esa parte del 
código y a correr. Al fin y al cabo es lo bueno que tiene el FLOSS, que puedes parchear 
cosas. No es que sea un hackeo de la hostia ni nada de eso. Sabiendo esto, os podéis 
figurar que de peligroso para el cacharro tiene cero, ya que simplemente es permitirle al
integrado trabajar en condiciones para las que está diseñado. 

> ⚠️ **OJO:** otro cantar es el tema de que lo que si hacen algunos firmwares es extender
el rango de frecuencias, por encima de las que está destinado el propio cc1101. Y esto
dicen que si puede dañarlo por tema de temperatura y demás porque, aunque técnicamente es
capaz, no está preparado para ello.

Pero a lo que ibamos, **¿merece la pena hacer ese "desbloqueo"?** Pues yo diría que no, 
hay que pensar que la mayoría de cosas que vas a querer ""hackear"" cumplen la normativa
europea, y probablemente casi todo lo que te encuentres funcione a 433.92 MHz. Así que 
queda muy guay decir que está "desbloqueado" pero casi seguro que no os sirva de nada. 
Aunque a veces uno encuentra cacharros a 315 o 915 porque alguien ha comprado cosas de 
china que no pasan la normativa jajaj

### 2.3. ¿Cómo se usa?
Pues para interactuar con él se usa el protocolo SPI, que es uno de los más extendidos 
cuando usas módulos de electrónica con arduino y demás. En wikipedia podemos ver el 
esquema de bloques:

![Esquema de bloques de SPI]({{site.url}}/assets/images/invasion_radiogotchis/spi.jpg "Esquema de bloques lógicos de SPI")

Viendo el esquema creo que es bastante fácil de entender: se comparten las líneas del bus
de reloj (SCLK), envío de datos hacía el módulo (MOSI) y recepción de este (MISO). Y por
cada módulo tenemos que tener una línea de selección de chip (SS) que activa o desactiva
el módulo que se quiera, y mientras esté activado tendrá uso exclusivo del bus. Esto
último es una desventaja si andamos cortos de pins GPIO en el microcontrolador. Existen
protocolos como i2c que no precisan de esto, pero eso es harina de otro costal...
Lo bueno de SPI es que es rápido.

A través del protocolo SPI se realiza toda la configuración del módulo, que es bastante
extensa, cosas cómo: ganancia, frecuencia, modulación, modos de operación, desviación, 
amplitud... Si no me creéis mirad el datasheet en la sección de enlaces y fliparéis, hay que
estudiar y todo. La verdad es que os he mentido, he dicho que no iba a leerme el manual
pero  he tenido que bichearlo varias veces para entender algunas cosas, soy un fraude 😭.

Durante el año pasado (o quizá el anterior) compré demasiados modulitos de estos, 
seguramente si compras alguno, acabes pillándolos de esta versión:

![Pinout cc1101 v2]({{site.url}}/assets/images/invasion_radiogotchis/cc1101_v2_pinout.jpg "Pinout del módulo cc1101 v2")

He puesto yo a la derecha el dibujito del pinout sacado del datasheet estrategicamente.
Siguiendo la imagen se puede ver que los módulos "exponen" los pines de SPI que se ven en
el diagrama pero aparte otros dos pines "GDO0" y "GDO2" (y GDO1 si no ignoramos el miso).
Esos pines los ofrece el fabricante para hacer varias cositas, mayormente usarlos como 
envío de datos en serie o flags de estado, útil para interrupciones.

Citando a un sabio (M. Rajoy): "sobre lo segundo ya tal" (si has estudiado informática 
sabrás para qué sirven las interrupciones de hardware y por qué son importantes).

Para explicar el primer punto primero tienes que saber que a la hora de transmitir o 
recibir señales, el cc1101 tiene 3 formas de hacerlo:
* Mediante el uso de una **pila FIFO**: para transmitir se envían los bytes de la señal
  a través del bus SPI y el módulo los guarda en un buffer y los transmite por orden de
  llegada (de ahí lo de fifo). Para recibir, justo lo contrario, se llena el buffer y el
  microcontrolador puede obtenerlos a través de SPI. La duda aquí es saber cuándo tenemos
  datos disponibles para leerlos, pues hay dos formas:
  1. Preguntando por SPI a un registro especial que indica si hay datos disponibles 
     (y cuántos).
  2. Uno de los GDO mencionados, puede usarse como flag de estado que se active cuando 
     hay datos disponibles. Esto se puede usar como interrupción en el micro, si no lo
     has estudiado nunca: al recibir la interrupción se puede decir al microcontrolador
     que deje lo que está haciendo y se ponga a recoger esos datos. Y luego vuelva a lo
     suyo.
* Modo **síncrono en serie**: usando uno de los GDO como señal de reloj y otra para 
  mandar los datos bit a bit. Esto en verdad no lo he usado pero sería interesante para
  algunos casos.
* Modo **asíncrono**: se asigna uno de los GDO como Tx o Rx. En el primer caso, lo que 
  hace es, directamente modula y transmite el estado de ese pin (LOW o HIGH). Para el
  caso de Rx lo contrario, lo que demodula lo saca en el estado recibido al pin (recordad
  que solo está pensado para señales digitales, supongo que ahora véis el sentido).

Decir que antes de estos puntos se tiene que haber configurado todo el tema de la radio:
frecuencia, modulación, etc...

Si has leído documentación, código de las librerías o demos de este módulo por GitHub,
igual has visto sobre el GDO0 o GDO2. La mayoría de códigos que he visto usan el modo
asíncrono, es bastante versátil y no depende de que el chip sea capaz o no de interpretar
la señal. En flipper zero a poco que hayas leído algunos ficheros habrás visto el típico
formato "raw". Por ejemplo [este](https://raw.githubusercontent.com/UberGuidoZ/Flipper/refs/heads/main/Sub-GHz/Ceiling_Fans/FT1211R/1.sub) 
de un ventilador:

```
Filetype: Flipper SubGhz RAW File
Version: 1
Frequency: 433920000
Preset: FuriHalSubGhzPresetOok650Async
Protocol: RAW
RAW_Data: -136 567 -132 99 -168 433 -266 97 -430 197 -134 3743 -166 923 -66 13349 -202 265 -364 131 -100 133 -498 199 -400 131 -202 233 -560 395 -98 625 -66 231 -134 133 -66 331 -268 67 -66 197 -332 1165 -66 35651 -536 199 -570 163 -98 95 -1112 427 -360 1185 -134 433 -564 863 -232 167 -232 163 -270 65 -432 1427 -132 465 -200 85695 -2192 97 -1286 461 -658 131 -166 295 -100 325 -64 99 -756 99 -858 131 -232 295 -66 983 -100 190497 -3002 67 -494 231 -428 131 -362 131 -98 229 -328 131 -132 129 -396 131 -558 425 -1354 99 -100 297 -132 167 -134 431 -364 131 -266 131 -66 26901 -5296 65 -5402 97 -232 129 -100 425 -198 65 -2588 165 -1130 65 -566 131 -300 65 -266 99 -432 265 -862 197 -1590 165 -466 231 -100 165 -300 167 -98 331 -168 199 -134 30461 -2450 65 -1002 65 -232 65 -530 65 -266 231 -66 231 -98 199 -100 99 -168 297 -166 133 -266 165 -200 99 -2184 163 -362 65 -100 661 -98 2687 -1312 161 -2800 97 -196 97 -64 163 -296 229 -132 65 -924 63 -560 861 -466 97 -100 529 -266 1509 -674 131 -464 165 -1088 131 -228 163 -4036 65 -894 63 -562 129 -132 961 -100 133 -132 65 -66 233 -432 595 -266 131 -232 595 -232 57273 -30218 353 -5688 877 -330 285 -882 287 -924 283 -934 849 -352 867 -320 853 -350 863 -326 277 -914 285 -922 877 -346 247 -926 877 -352 247 -932 865 -350 833 -356 271 -910 285 -924 283 -904 289 -906 277 -930 879 -348 249 -926 873 -336 283 -5706 853 -350 281 -926 259 -932 279 -914 879 -346 853 -346 851 -336 849 -342 251 -960 245 -960 843 -338 283 -922 845 -362 247 -954 845 -328 875 -350 245 -932 269 -934 253 -958 245 -958 273 -926 841 -352 281 -906 857 -350 279 -5708 889 -320 247 -958 275 -916 275 -934 865 -350 835 -322 899 -316 865 -326 277 -916 273 -932 869 -352 245 -940 853 -354 281 -926 843 -338 877 -334 247 -930 279 -942 259 -944 277 -896 259 -968 867 -320 273 -916 883 -324 275 -5712 873 -338 283 -922 273 -928 273 -916 883 -320 871 -316 889 -322 871 -318 281 -934 265 -934 855 -322 299 -912 885 -322 271 -914 869 -326 879 -342 247 -958 247 -934 261 -950 277 -904 261 -954 867 -324 273 -916 881 -324 275 -5710 875 -342 285 -904 289 -904 277 -924 871 -350 863 -324 877 -320 889 -322 271 -918 273 -934 865 -320 281 -932 877 -310 285 -922 873 -336 845 -350 283 -894 309 -914 275 -898 289 -908 313 -896 877 -348 249 -928 875 -336 285 -126588 131 -100 165 -66 131 -366 99 -468 497 -100 99
RAW_Data: -1190 657 -98 1051 -100 493 -164 395 -66 314561 -3944 65 -532 131 -132 563 -1226 65 -100 165 -3250 763 -630 99 -100 131 -134 23305 -1358 131 -462 67 -1556 399 -1728 65 -302 299 -366 757 -66 131 -430 197 -826 67 -262 327 -230 327 -658 229 -230 82149 -2620 165 -962 65 -364 199 -132 67 -1262 99 -502 65 -330 165 -532 199 -494 331 -66 199 -98 231 -134 631 -98 165 -234 99 -298 759 -98 201567 -4660 99 -166 67 -1258 99 -3998 395 -462 1117

```

Si lees en preset el churro ese significa: modulación AM/OOK, 650khz de ancho de banda y
modo asíncrono. Por lo tanto en raw data se determina en microsegundos cuanto dura cada
nivel, cuyo estado se indica por el signo. Por ejemplo: -136 significa que estará a 
estado bajo 136 segundos, luego pasará a alto durante 567 us y así... Y esto se hará 
activando el pin GDO0 cambiando entre LOW y HIGH de forma manual cuando pasen esos 
tiempos.

Aquí os habréis dado cuenta de que a pesar de que antes hablaba de GDOx en general, ahora
he dicho GDO0. Esto es porque para transmitir solo admite GDO0, para recibir si se puede
usar cualquiera de los tres. Normalmente la gente usa el GDO2 para recibir, ya que el 
GDO1 es usado por MISO. También he visto a gente usar directamente GDO0 para Tx y para 
Rx. Al fin y al cabo el chip no puede transmitir y recibir al mismo tiempo (no es lo que
llamarían "full duplex"), de este modo evitan ocupar dos pines de microcontrolador para
algo que se podría hacer en uno. Y cuando no vas sobrado de pines desde luego compensa.

Para recibir en asíncrono las opciones que se suelen tener y he visto:
* Cronometrar el tiempo entre cambios de estado e ir apilando tiempos en un buffer.
* Establecer un tiempo de muestreo y medir el estado cada vez que pase el tiempo.

## 3. Jugando con el cc1101
En este blog se valora la belleza que radica en el cutrismo. Entonces para hacer mis 
pruebas me he montado una versión mínima y funcional de un esp32 con un cc1101 que luce
así:

![Versión minimalista de esp32 con cc1101]({{site.url}}/assets/images/invasion_radiogotchis/prototipo_minimal.jpg "Versión minimalista ESP32+CC1101")

Y sí, si has leído el post anterior, he usado el paint también para hacer las pistas de
este. Y bueno, para probar cosas creé una aplicación web simple y cutre junto con el 
código (en Arduino + PlatformIO):

![Web de demo cc1101]({{site.url}}/assets/images/invasion_radiogotchis/cc1101_web_demo.png "Web de demo para explicar el cc1101 en el post")

Desde aquí pido perdón a los frontend, CSS y yo nos entendemos mal, y aunque no lo creáis
esa mierda, con ese formato, me costó mucho de sacar jajaj

De esto no voy a subir código, primero porque una de las partes, que era probar el modo
síncrono, no he conseguido tener buenos resultados y capturarlo con el flipper. Y
sinceramente ya me cansé de ello, ya probaré en un futuro... Y segundo, porque quizás
salga un segundo post de esto. Quiero hacer un firmware que sea minimamente funcional y
enseñar paso a paso como hacer tu propio radiogotchi casero (no me canso del término). 
Sólo he mencionado esto porque es lo que usaré más abajo.

### 3.1. ¿Puede el EvilCrowRFv2 usar los dos módulos de forma simultánea?
Hace un tiempecín vi en el grupo de discord de evilcrow a alguien preguntando si era
posible usarlo para emitir en dos frecuencias diferentes. Bueno, la respuesta es 
"depende". En el evilcrow los dos módulos comparten el bus spi, y eso significa que no se
les puede mandar información a los dos simultáneamente, hay que activar uno con el chip
select (CS), mandarle la info y luego pasar al otro. ¡Si activas los dos a la vez la lías
parda!

Pero cuando estableces el modo asíncrono ni siquiera hace falta que estés activando el
bus spi con CS, lo que entra por el pin de Tx es lo que emite directamente, para Rx igual
pero a la inversa.

Aquí por ejemplo podéis ver una prueba de un ESP32 conectado a dos módulos, configurando
uno como Tx asíncrono y otro como Rx asíncrono. El primero tiene un botón directamente
conectado al gdo0 y el otro un led a gdo2, usando el mismo bus spi:

<video width="640" height="360" controls>
    <source src="{{site.url}}/assets/videos/invasion_radiogotchis/simple_async_demo.mp4" type="video/mp4">
    Parece que tu navegador no soporta la etiqueta de video :(
</video>

Ignoremos lo de que el led vaya al revés, ahí conecté al revés el led o configuré cómo
funcionamiento inverso el registro de configuración de gdo2 jajaj, ah sí, y con el botón
de la derecha los cambio de estado Idle a Tx/Rx.

Bueno, sabiendo esto y extrapolando al evilcrow, ¿se puede emitir con los dos módulos a
la vez? Pues creo que resulta obvio decir que de forma asíncrona sí podrías, de hecho el
ejemplo más sencillo es hacer jamming en dos frecuencias distintas de forma simultánea.
Aquí una demo de ello asignando cada uno de los dos botones del ecrfv2 a la acción de 
emitir por uno u otro módulo:

<video width="640" height="360" controls>
    <source src="{{site.url}}/assets/videos/invasion_radiogotchis/multijammer_ecrfv2.mp4" type="video/mp4">
    Parece que tu navegador no soporta la etiqueta de video :(
</video>

Perdón la calidad de mierda del vídeo, lo grabé poniendo el móvil sobre un cristal y salió
mal. ¡Y me daba pereza repetir el vídeo!
> Sobre lo de por qué al activar las dos hay más ruido a los lados tiene que ver con las
  armónicas, si queréis saber más sacaos el títulito de radioaficionado.

El código es tan simple como esto:
<script src="https://gist.github.com/iordic/c4599cc58b40b296ff7f62b0c1e0bb69.js"></script>

Simplemente hay que poner gdo0 a estado alto, y el módulo por si mismo estará emitiendo
sin pausa. ¡Radioruido nonstop!

Y teóricamente incluso podrías usar un módulo para estar emitiendo mientras tienes otro
en Rx si usas interrupciones: estás emitiendo y si llega algo, interrumpes y te pones a
guardar lo que llega. Pero claro, el problema que tiene el EvilCrow es que tiene las dos
antenas muy cerca, y sin probarlo siquiera intuyo que los armónicos de la que emite
fastidiarán a la otra, y crearán un "falso positivo" por llamarlo de alguna forma. Aunque
ahora que lo he pensado igual es algo que se podría probar. Usando frecuencias muy
separadas igual hay suerte, rollo las legales 433.92 y 868 en cada uno...

#### Apunte sobre jamming:

⚠️ **Advertencia**: usar jammers está totalmente prohibido por ley.

La advertencia de arriba queda muy chula, es como eso de "no hagas esto en casa" y
aparecen después dos señores metiendo el culo en un estanque de pirañas. Y tu piensas:
joer, me acaban de joder la tarde, era el plan que tenía hoy pero hay que cumplir las
normas.

En fin, en todos los vídeos te dicen la cosa esa de que está prohibido y demás, pero por
un momento, vamos a ser realistas y no sensacionalistas... Es verdad que hacer eso es
ilegal, pero por otro lado el cc1101 emite con una potencia irrisoria. La gente se empeña
en usarlo para jamming y es que no sirve a no ser que le metas un amplificador a la
salida... De hecho si tenéis el flipper haced la prueba: probad una de esas aplicaciones
para hacer de jammer al lado de un receptor, e intentad usar el mando original, veréis
como en la mayoría de casos incluso no hace nada... No váis a ir a la cárcel por esto, 
porque directamente no lo va a notar ni le va a importar a nadie.

Lo que si he visto en youtube es una "demo" de cómo los ladrones usaban walkies baofeng
como inhibidor y eso si que es algo que yo no subiría a redes, aunque no sea tu intención
hacer el mal, porque eso si funciona y puede traerte problemitas (y para mas inri son 
cacharros de 20€) jajaj 

Así que mi advertencia realista es:

⚠️ **Advertencia**: dejad de hacer el parguela pensando que el flipper sirve como inhibidor.

### 3.2. Probando cosas con un analizador lógico

**¿Qué es un analizador lógico?**

Si no sabéis qué es un analizador lógico, lo resumiré diciendo que un cacharrín que 
permite ver el estado de pines I/O digitales a lo largo del tiempo. Lo de lógico pues eso,
sólo puedes ver si se encuentra en estado lógico alto (1) o bajo (0). 

Lo bueno de la época que nos ha tocado vivir es que la electrónica está más barata que 
nunca (gracias aliexpress). Por ejemplo yo tengo este clon chino que cuesta unos 5-6€:

![Analizador lógico clónico]({{site.url}}/assets/images/invasion_radiogotchis/logic_analyzer.jpg "Analizador lógico clónico barato del aliexpress")

No es que sea muy allá pero joer, por 6€ qué más quieres, y ahora os enseño como cumple
perfectamente su propósito. Y bueno, sobra decir que el código que voy a usar para las
pruebas es el de la demo que he programado, ¡que para eso ha sido!

A lo que ibamos: los programas que los usan suelen tener opción para decodificar 
protocolos, esto significa que pueden interpretar los bytes que se están mandando a 
través de un bus, si conocemos el protocolo que se usa. Y sino, a veces, se puede deducir
viendo el comportamiento que tiene.

Ya sabéis que yo siempre voy a defender usar FLOSS, a no ser que sea inusable, y uso 
PulseView que está decente. Además funciona en linux también, y con mi setup actual es un
requisito obligatorio.

Mirad, con pulseview y pinchándome a los pines del cc1101 se puede ver por ejemplo la 
configuración que nuestro microcontrolador le va diciendo por SPI 
(mosi va de master a slave):

![Explicación del método init cc1101]({{site.url}}/assets/images/invasion_radiogotchis/init_spi_cc1101.jpg "Configuración de inicio del módulo cc1101 en el analizador lógico")

He puesto en la imagen el código que se ejecuta al iniciar, junto con la leyenda de 
macros definidas con el valor real, para que veáis cómo se va aplicando la configuración.
Me gusta esta librería mucho porque es simple y fácil de leer, sé que existe radiolib y
creo que funciona de lujo pero el código está más enmarañado para aprender... Sobre la 
imagen: en el primer byte dice a que registro quiere escribir y luego los valores 
aplicados. Si vais al datasheet podéis ver lo que está configurando.

Ampliad la imagen para ver bien los detalles.

Por ejemplo, en el paso 3 que es fácil de entender, si vais a la documentación veréis que
sirve para establecer el pin GDO2 como salida asíncrona:

![GDO2 config en datasheet]({{site.url}}/assets/images/invasion_radiogotchis/config_gdo2.png "Configuración del pin GDO2 explicada en el datasheet")

☝️🤓 Tiene sentido, en el código la condición de ccMode es *false* porque por defecto es
así, este atributo se usa para decir si se quiere usar de forma síncrona o asíncrona.

Y también podéis ver con nuestro querido analizador barato qué pasa con los pines gdo0 y
gdo2. Pues también podemos ver el comportamiento cuando los configuramos para emitir o
recibir. Por ejemplo, configuramos el módulo para recibir de forma asíncrona por el pin
gdo2 y desde el flipper emitimos la señal de ejemplo de flipper zero que he puesto en el
punto 2.3. Y podemos ver la duración en microsegundos de cada estado y comparar con el
fichero:

![Prueba Rx en GDO2]({{site.url}}/assets/images/invasion_radiogotchis/logic_analyzer_rx.png "Prueba de recepción en gdo2 con analizador lógico")

Puedes ver que más o menos son valores aproximados, nunca vas recibir los valores exactos
a cómo se han emitido. De hecho me atrevo a poner la mano en el fuego a que, si emites en
un flipper zero ese fichero y lo guardas en otro como raw, no tendrá los mismos valores
exactos.

Ahora hago la prueba para Tx, poniendo los valores en microsegundos para ver si en el
analizador lógico salen los tiempos con precisión. Con la miniweb esta envio varios
valores de prueba:

![Tx async web]({{site.url}}/assets/images/invasion_radiogotchis/demo_web_config.png "Envío de pulsos en async")

Y también se puede ver que no son exactamente igual a los medidos:

![Prueba Tx en GDO0]({{site.url}}/assets/images/invasion_radiogotchis/logic_analyzer_tx.png "Prueba de emisión en gdo0 con analizador lógico")


## 4. ¿Merecen la pena los radiogotchis? ¿Cuál me compro?
Tengo críticas para todos en verdad, bueno para el flipper menos, solo que es caro y yo
apuesto per ser un pesetero. Os recomendaría que si estáis aquí por la "cencia" y la
diversión, juguéis con modulos y placas arduino-like a pelo. No compréis cosas por el
hype si no es para intentar aprender. Al final la utilidad que tienen es bastante escasa.
Diría eso de "no hagáis el mal bla bla...", pero tampoco es que podáis hacer mucho mal
con estas cosas.

⚠️ **Sobre los módulos que venden en aliexpress**: os habréis fijado que cuando váis a 
comprar os dan a elegir la frecuencia, comprar placas para 433 o 868, y esto al menos a
mi me hacía pensar: joder, ¿será que sólo funcionan en esa frecuencia o qué? Pues si y 
no... Si nos volvemos a nuestro amigo el datasheet, hay una sección en la que te proponen
el circuito que se puede usar dependiendo de para qué frecuencias quieres usarlo:

![Esquemas recomendados para el cc1101 según frecuencias]({{site.url}}/assets/images/invasion_radiogotchis/cc1101_typical_schematics.png "Esquemas recomendados para el cc1101 según frecuencias")

Tenéis que pensar que Texas Instruments no inventó este chipset para que la gente hiciera
el gamberro y quisiera usarlo en muchas frecuencias a la vez. Lo que intuyo es que lo
hicieron lo más versátil posible para que pudieras usarlo en diferentes escenarios.

Entonces los fabricantes chinos cogen y hacen un módulo con el esquema de arriba y otro
con el de abajo. Y tú ya te apañas con ello. La diferencia entre ambos esquemas está en
el tema del circuito que conecta la antena al chip, que actúa como filtro y acoplamiento
de antena. Esto es algo que se estudia para la radioafición, pero básicamente se supone
que tu haces un circuito que encaje perfectamente con la antena y no haya pérdidas de
potencia por el camino. Entonces ese circuito está diseñado para funcionar bien con la
frecuencia que pone, técnicamente puedes usar otras, porque el chip puede, pero el 
rendimiento no será tan bueno, habrá más ruido, atenuación de la señal... La antena, en
teoría, también está calculada para ser resonante con la frecuencia que dicen. Digo en
teoría porque suelen ponerte una porquería que no cumple con lo que dice. Pero bueno, 
para jugar os sirve igual. De hecho es otra prueba que se puede hacer, midiendo los
niveles de intensidad con el tinySA al emitir 🤔.

> ☝️🤓 Si queréis saber más buscad sobre el ROE y la potencia reflejada. Si la impedancia
  no está bien adaptada parte de la potencia vuelve hacía el dispositivo en vez de salir
  por la antena.

### 4.1. EvilCrow
EvilCrow está guay, a mi me ha servido para aprender bastantes cosas, aquí un joseo de
las fotos de su github. Existen dos versiones (v1 y v2, arriba y abajo respectivamente):

![Comparación de versiones evilcrow]({{site.url}}/assets/images/invasion_radiogotchis/evilcrowrf_v1_v2.jpg "EvilCrowRF v1 arriba y v2 abajo")

La segunda versión añadió el módulo nrf24 para hacer mousejacking y cualquier otra cosa
que se te ocurra con 2.4GHz. Además de el módulo de tarjeta microsd para poder meter
ficheros. Pero, y hay un pero, los módulos cc1101 de la versión dos vienen como pcb
soldada a 433MHz y en la v1 tiene pinta de que los filtros estaban más currados. Igual
la v1 tiene mejor rendimiento para otras frecuencias, es algo que desconozco. Otra contra
o pro, según lo veas, es que te tocará picar y lidiar con más cosas de código. Ah, y me 
parece que se está poniendo demasiado caro tbh. Yo creo que debería costar como 20€, pero 
eso ya es opinión personal.

### 4.2. Hackbat
Poco que comentar, a ver si me hago con uno algún día, me gusta lo de que esté pensado
para comprar los módulos y pincharlos encima. Aunque yo personalmente lo haría sólo con
módulos para gente que no sabe soldar smd chiquito, porque teniendo algunos componentes
así no es tan amigable para noobs. Destacar que el firmware/código es inexistente, así
que de pillarlo te toca hacer todo de 0. Tiene pinta de ser barato de ensamblar, eso sí.

### 4.3. Willy
El creador me cae mal (y juro que es casualidad lo de que sea francés), eso es todo. Ah, 
y no puedes decir que estás planteando una alternativa a flipper zero cuando me estás 
cobrando el firmware a 35 pavos, ni que fueses microsoft. Le han acusado desde diferentes
proyectos de sofware libre (como flipper) de coger código con licencias de uso libre para
hacer código cerrado y venderlo, cosa que incumple la licencia. Pero bueno, ya sabemos 
que con estas cosas "nothing ever happens". En serio, no le déis vuestro dinero ni 
vuestra atención a una persona que sólo busca rédito económico a base de replicar cosas
que en un principio se crearon para aprender y jugar. O haced lo que queráis, yo desde 
luego, aunque funcionase de lujo, si no es de código libre no es para mi. Cómo vas a 
aprender si no puedes ni ver el código que lleva... El firmware va bien, al menos el que 
hizo para el evilcrow (es el mismo señor) y es bonito.

### 4.4. CapibaraZero
Tiene muy buena pinta, al usar el ESP32 tiene wifi que no tiene el flipper, los módulos
abarcan prácticamente lo mismo que el flipper. Y he visto en aliexpress que el trasto 
cuesta entre 70-80€, no está mal la verdad, parece el más realista como alternativa. Yo
desde luego lo probaría, pero me duele gastarme 80€. El firmware está aún empezando, me 
gusta que sea con platformIO y Arduino. La documentación muy guay.

### 4.5. Superior Boy v2
"El flipper zero que puedes hacer tu mismo con una raspberry pi" (y que no vas a hacer
por pereza y economía familiar). ¿Esto dónde tiene el código y la documentación? Sólo hay
un enlace a discord 🤔.

### 4.6. HackRF + Portapack
Cada vez que decís hackrf para referiros al portapack muere un gatito. Este ni siquiera
es una alternativa a flipper ni lo que yo considero un radiogotchi, término que acabo de
inventarme. Es un cacharro que está sólo enfocado a radiofrecuencia y además no usa un
chip para RF digital, directamente usa un FPGA y es un SDR. Lo que le da bastante más
capacidades de las que tiene cualquiera de arriba, en cuanto a radio se refiere. Eso sí,
la batería dura poco y nada. Si quieres aprender de radio es lo que recomiendo...
Acaban de sacar hace poco una versión con GPIOs expuestos para poder usar módulos 
externos, a ver qué sale de todo eso.

### 4.7. Flipper Zero
Sólo tengo queja del precio, pero es que lo vale, el diseño es precioso, la batería dura
muchísimo, el firmware es una maravilla, es que ninguno que haya visto le llega a la
altura, se nota calidad en los materiales... Eso sí, luego no sabes en qué usarlo, se
enfada contigo y te hace chantaje emocional 💔 

Si lo compras que sea sabiendo que es un capricho, y uno caro vaya.

## 5. Conclusiones
Hay más alternativas, pero ahora mismo no sé cuáles, solo he probado el evilcrow y el 
flipper. Así que mi opinión está basada en lo que veo por los repos y tal.

Lo de los puntos anteriores es medio en broma, cada uno tiene su curro detrás, y yo creo
que no  tendría la constancia para sacarlos. Es genial que salgan alternativas, y más que
muchas de ellas son a precios más asequibles. Yo creo que todo el mundo debería tener la
opción de tener acceso a la tecnología. Y no solo por necesidad, incluso como pasatiempo.

Sobre el post, decir que me he quedado con ganas de probar más cosas, y me ha frustrado
un poco no haber obtenido buenos resultados con la emisión síncrona, pero mi idea es 
hacer una especie de segunda parte de esto. Además hacerlo probando la librería de
radiolib, que tiene más cosas. Necesitaba cerrar este post ya que me estaba haciendo 
bola. Realmente no me acaba de gustar cómo ha quedado pero no voy a rehacerlo ya, ya he
trasteado demasiado con esto y toca avanzar.

Sé que es un poco batiburrillo de cosas que quizás parezcan inconexas o no, no sé. De 
todos modos espero que os haya parecido didáctico y/o entretenido. En enero no lo saqué
porque he tenido varios líos a varios niveles, pero a ver si consigo mi propósito de
sacar otro el mes que viene. Porque con esto me fuerzo también a cacharrear y no divagar.
Gracias por leerme. ¡Hasta el próximo! <3

# Enlaces de interés
* [Documentación Texas Instruments - CC1101](https://www.ti.com/product/es-mx/CC1101)
* [Proyecto OpenSesame](https://samy.pl/opensesame/)
* [CC1101 - Luis Llamas](https://www.luisllamas.es/comunicacion-rf-385-433-y-815mhz-con-arduino-y-cc1101/)
* [The hackbat](https://thehackbat.com/)
* [Repositorio de EvilCrowRF](https://github.com/joelsernamoreno/EvilCrowRF-V2)
* [Documentación chachi no oficial de Flipper Zero](https://github.com/jamisonderek/flipper-zero-tutorials/wiki)
* [Web de PulseView](https://sigrok.org/doc/pulseview/unstable/manual.html)
* [Librería de CC1101 para aprender](https://github.com/LSatan/SmartRC-CC1101-Driver-Lib)
* [Repositorio EvilCrowRFv2](https://github.com/joelsernamoreno/EvilCrowRF-V2)
* [CapibaraZero](https://capibarazero.com/)
* [Superior Boy v2](https://www.hackster.io/superior-tech/superior-boy-v2-advanced-education-cyber-security-device-148b4d)