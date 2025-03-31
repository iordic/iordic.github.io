---
title: "El campingaz de la electrónica"
description: ""
date: 2025-03-31 22:00
categories: electronica
header:
  og_image: /assets/images/campingaz_electronica/og_image.jpg
---
¿Qué tal gente? He tenido un marzo movidito en el buen sentido, he hecho viajes y gastado
demasiado dinero, pero aunque sea con un post sencillo voy a cumplir la cuota mensual.
Aquí somos de hacer los deberes a última hora...

Este mes compré un cacharrín para soldar componentes smd en placas chiquitas. La historia
de esto tiene que ver con el post anterior que, si lo habéis leído y recordáis, estuve 
jugando con un analizador lógico de estos baratos del aliexpress. Ahora lo explicaré.

El cacharrín para soldar es este:

![Mini placa soldadura SMD]({{site.url}}/assets/images/campingaz_electronica/stock_placa_soldar.png "Miniplaca para soldar SMD")

La cuestión es que vi en github un [proyecto](https://github.com/gusmanb/logicanalyzer)
muy chulo para montarte un analizador lógico, usando un microcontrolador RP2040, que es
mejor que el barato de aliexpress: funciona a mayor frecuencia, tiene más canales, es
floss y ciertas cosas aparte... Algunas ni las he tocado. La cosa es que vi que podías
pedir la pcb y montarte uno en casa. Que además tiene pocos componentes y debería ser 
"fácil".

Nunca había usado esta técnica para soldar, y cómo el trasto era barato (12€), pues tenía
la excusa para probar esto. Sí, me gusta probar cosas porque sí jaja de hecho como salía
barato, pedí varias pcbs juntas. Si sale bien puedo hacer varios de estos para los
amiguis. Aquí una foto del pre:

![Preparando PCB sin soldar]({{site.url}}/assets/images/campingaz_electronica/preparativos_soldar_placa.jpg "Preparativos para soldar PCB")

Parece que en el display ponga 100º pero está a 180º jaja. Tiene un par de cosas de 
config que ni entiendo pero las dejo en default y tira bien. El resto tampoco es que sea 
muy complicado...

Y lo del título del post lo entenderéis si seguís leyendo, pero se resume en que uno se
vicia a ponerle cosas encima como si estuviese haciendo nubes en un camping. Pensaba que
sería otra cosa para fondo de cajón pero, por lo que me ha costado, ya está amortizado.

Al final, ayudándome un poco del soldador de boli para quitar los cortos entre las
patillas de los ICs, parece que acabó funcionando, ¡que no es poco! Aquí una foto del bicho
funcionando, en la foto que parece que esté en un barco hundiéndome:

![PCB ya soldada]({{site.url}}/assets/images/campingaz_electronica/analizador_funcionando.jpg "Analizador lógico ensamblado y funcionando")

Aquí no voy a enseñar cómo soldar con esto porque ni yo sé bien, me pasé añadiendo pasta
porque la punta de la aguja era demasiado gorda para echar menos. Tengo que calibrar muy
bien la fuerza a la próxima, que será pronto.

## Rumbo hacía el diógenes electrónico
Los que no soléis hacer muchas cosas de electrónica me váis a juzgar duramente, pero los
que sí, me entenderán cuando digo que a veces si se me joden cosas las guardo para tener
cacharros que destripar o de los que sacar piezas. Y, aunque parezca diógenes, es una 
buena práctica (puedo justificarlo!).

Para probar a desoldar, tenía por casa un lolin32 lite que no funcionaba. Creo que lo
quemé con tema de meterle tensión o polaridad que no era, sea como sea ya quité 
componentes en su día. Y ahí estaba con una cruz marcada con permanente, preparada para 
ser desguazada y poco más. Efectivamente, cuando muere algo le pongo una cruz con
rotulador para saber que no funciona, porque tengo mala memoria. El mundo es un lugar
frío y cruel.

Por si no sabes que placa es la lolin32 lite aquí dejo un diagrama con los pines. Que he
cogido de google:

![Pinout lolin32 lite]({{site.url}}/assets/images/campingaz_electronica/lolin32_lite_pinout.jpg "pinout del lolin32 lite")

Esta placa en concreto es la que uso en el tracker lora casero, que mostré en otro post.
A la hora de desguazarlo, en lo primero que me fijé es en que, el lolin32 lite tiene una
cosa curiosa que me venía bien: a diferencia de otras placas de desarrollo con el esp32,
que tienen protegido el microcontrolador debajo de una carcasa metálica, esta placa tiene
todo "al aire". Entonces se puede ver que tiene a mano la memoria eeprom. Bueno, deduje
que era la eeprom por razones obvias, pero me aseguré usando el microscopio digital que
tengo, porque uno ya está ciego para usar los ojos, y voilá: 

![EEPROM del lolin32 lite]({{site.url}}/assets/images/campingaz_electronica/eeprom_lolin32_lite.jpg "EEPROM de la placa lolin32 lite")

Que el nombre tenga un "25" es buena señal, las eeprom suelen tener 24 o 25 en el nombre
y suelen tener 8 patillas. Busqué en google "T25S32 datasheet" y ahí estaba el primer
resultado diciendo que era una eeprom por spi.

¿Para qué quiero sacar esa eeprom? Para nada en verdad. Ahí se guarda el firmware, si
fuese de una placa comercial si que se suele intentar sacar de ahí el programa para
intentar hacerle ingeniería inversa. Pero bueno, respondiendo a la pregunta, en hardware
hacking se habla de leer estos chips y te suelen recomendar comprar esto:

![ch341a con pinza]({{site.url}}/assets/images/campingaz_electronica/foto_stock_ch341a.jpg "CH341A con pinza")

Es una plaquita que podéis comprar sin miedo a arruinaros, creo que cuesta euro y pico 
jajaj El ch341a es un chip bastante versátil si los drivers funcionasen bien, pero este 
post no va de eso. La cosa es que la gente lo usa solo para eeproms, además esta plaquita,
que te venden así, tiene el socket ese con palanquita, que la verdad que mis dieses. Y
luego la pinza, la maldita pinza... Se supone que sirve para pinchar una eeprom
directamente sin desoldarla de la placa y leerla así. LA ODIO, ¡nunca engancha bien!

Cuando intento usarla me siento como un mono intentando partir un coco con un palo, al
principio de tener la placa (hace años), pensaba que no funcionaba porque con esa pinza
era incapaz de enganchar bien y leer...

Y después de este rollo que he soltado, lo que quería decir es que, quería tener una
eeprom suelta para poder hacer pruebas y demás. Así que al lío y al campingazzz:

![Desoldando placa con plato térmico]({{site.url}}/assets/images/campingaz_electronica/placa_desoldando.jpg "Desoldando la lolin32 con plato térmico")

Una vez desoldada os muestro como si engancha la pinza una vez el ic suelto:

![EEPROM suelta en pinza de lectura]({{site.url}}/assets/images/campingaz_electronica/pinza_eeprom.jpg "EEPROM suelta en pinza de lectura")

¡Cuándo ya no hace falta! Pero se puede comprobar que funciona al leer:

![Lectura de eeprom con IMSProg]({{site.url}}/assets/images/campingaz_electronica/captura_lectura_eeprom.png "Lectura de eeprom con IMSProg")

Si sois cotillas y miráis la captura os podréis hacer una idea de que tipo de programa
había antes de cascar.

Por último, sabiendo que funciona, "convertimos" el smd a dip con un adaptador que compré
hace mucho también y estaba esperando a ser usado:

![EEPROM smd a dip]({{site.url}}/assets/images/campingaz_electronica/ch341a_y_eeprom.jpg "EEPROM smd a dip")

Pincharla así si que da gusto y no con la pinza del demonio, cómo odio la maldita pinza...

![Conexión eeprom dip en ch341a]({{site.url}}/assets/images/campingaz_electronica/ch341_eeprom_conectada.jpg "Conexión eeprom dip en ch341a")

Y así uno recicla componentes. Esa eeprom podría usarla en una placa funcional, las
memorias spi de 4MiB son bastante estándar y usadas.

## Desenlace
¿Creías que estaba mezclando cosas que no tenían nada que ver? Pues sí, ¡pero ahora
unimos hilos!

Ahora, con esto, tengo una placa para leer eeproms con pines expuestos, y un analizador
lógico que, cómo no lo he desmotrado aún, pensaréis que me estoy tirando un triple con lo
de que funciona. ¡Al fin y al cabo era mi primerita vez!

Pero aquí vengo yo a defender mi honor (y de paso a enseñaros una captura del programilla).

Como dicen los yankis en sus tutoriales: putting all together. Ahora podemos unir
nuestros dos nuevos frankensteins y juguetear:

![Conexión CH341A con analizador lógico]({{site.url}}/assets/images/campingaz_electronica/analizador_con_ch341a.jpg "Conexión CH341A con analizador lógico")

Aquí una captura buena que he conseguido mientras se leía el chip desde el otro programa (IMSProg).
Podéis fijaros que en "miso", que es lo que manda el chip al lector, nos está dando bytes
que coinciden con lo que tengo seleccionado en el logic analyzer (el software del 
analizador):

![Intercepción de lectura de la eeprom con analizador lógico]({{site.url}}/assets/images/campingaz_electronica/capturado_eeprom.jpg "Intercepción de lectura de la eeprom con analizador lógico")

Lo sé, lo sé, shame on me... Usando windows para esa captura, es que en ubuntu cuesta de
instalar porque está hecho con .NET y para más inri usado por una librería de python que
carga ese .net. Lo tengo instalado en otro ubuntu y me dió dolor de cabeza conseguirlo.

### Sobre el analizador lógico
Con este post os he enseñado por encima y con esta captura véis cómo es el programa y el
analizador, del cuál di el coñazo por todas las redes sociales. De momento, y respecto al
PulseView, he de decir que hay cosas que me gustan más de este pero también viceversa.

En pulseview no conseguía lo de usar un trigger para empezar a capturar, aquí si funciona.
Una cosa que si tenía, y este no, es que en PV puedes ver en tiempo real como va
capturando la señal. Es una chorrada pero a mi me gusta verlo, además puedes estar más
rato capturando. Lo de la eeprom tengo que probarlo en el otro, pero decir que aquí no
he conseguido coger bien el cacho que quería.

Aún así, este cacharro sale barato y tiene una frecuencia más que suficiente en
comparación con el otro, que a veces se queda corto. Y que sea totalmente opensource me
gusta. Que esté hecho en .NET no tanto jaja

## Conclusión
La verdad que es bastante práctico para reciclar componentes o incluso para arreglarlos.
No es la primera vez que desueldo smd, tengo una estación de aire, pero siempre se me ha
dado mal usarla la verdad. Es complicado ajustar el flujo de aire, la temperatura y que
los componentes salgan bien, y de hacerlo, que lo hagan sin salir volando 😑.

Ahora estoy a favor del estaño sin plomo, quién lo diría, porque la placa no llega a 
mucho más de 200º y el estaño con plomo no funde a esa temperatura jaja. Además, fui al
museo del Prado y me asustó eso de que Goya acabase pillando saturnismo dese por culpa
del plomo, aunque con lo que me he esnifado ya... Igual ya estoy loco y no lo sé 🤔

Siempre lo digo, es el mejor momento que he conocido para tener de hobby la electrónica,
porque gracias a los chinos es más barata que nunca. Además con los chips actuales se
pueden hacer cosas increíbles que antes no tenían capacidad de hacer. Por ejemplo con el
tema de la radio, hace 20 años nadie imaginaría que habría transceptores que llegan a kms
de distancia, más baratos que el menú del día de un restaurante cutre.

Yo no sé vosotros pero para que los componentes electrónicos acaben en una montaña de 
residuos prefiero buscarles una segunda vida, aunque sea una vida de la más estúpida.
De momento he sacado los componentes del lolin32 lite y de un arduino mini pro (falso)
que también estaba jodido:

![Bolsita con componentes extraídos]({{site.url}}/assets/images/campingaz_electronica/componentes_extraidos.jpg "Bolsita con componentes extraídos")

Creo que hay un lm1117 ahí metido si no recuerdo mal, puedo hacer un regulador de voltaje
reciclando, porque eso no estará quemado. 

Otra cosa que tenía el lolin32, por necesidad, era un chip de usb a serie (concretamente 
el ch341a). Y por si pensáis que no se pueden sacar cosas útiles, tengo por casa un cable
para "programar" un walkie chiquito llamado baofeng bf-t1, y usa el mismo chip usb-ttl:

![Programador baofeng bf-t1]({{site.url}}/assets/images/campingaz_electronica/programador_baofeng_bf_t1.jpg "Programador baofeng bf-t1")

> Nota del redactor (yo): el baofeng bf-t1 es una puta mierda, podría hacer un post o 
  vídeo entero sólo criticando todo lo que tiene mal, que es todo. Quizá lo haga. No lo
  compréis porfa.

Así que ya sabéis, nunca se sabe cuándo va uno a necesitar componentes, y aunque estén
baratos, garrapiñead todo lo que podáis. Además cuando juegas con cosas recicladas y se
joden por hacer el tonto, enfada menos. ¡Y si queréis aprender cosas hay que romper 
cosas!

Espero que os haya gustado este post humilde de un humilde servidor. ¡Hasta pronto! 🐱