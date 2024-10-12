---
title:  "¡El portapack me quemó los dedos!"
description: "Buscando al culpable de la mala carga de batería del portapack"
date:   2024-09-12 17:00
categories: electronica
header:
  og_image: /assets/images/portapack/og_image.jpg
---
Vale, quizás no fuese el portapack, fui yo porque soy un patoso y tuve que coger el
soldador con las manos pero... ¿siempre se me tienen que estar jodiendo todos los 
cacharros? Es que no me sale una eh (añade más victimismo).

¿Qué tal el verano gente? Este ha estado bien, ¿no? Yo he viajado y cacharreado, ha
estado guay. Este no va a ser un post técnico, es para quejarme, que es una de las cosas
que mejor se me da.

## 1. ¿Qué mierdas es el portapack?
Bueno, no sé si habréis oido hablar del **hackrf**, supongo que sí porque cuando se habla
del Flipper Zero siempre están con eso de que es *el primo pequeño* del hackrf, cosa que 
es una absoluta estupidez. ¿Por qué? Porque el *hackrf* simplemente es un transceptor SDR
que se usa desde el PC a través de USB.

> Nota del director: SDR significa que es radio definida por software y transceptor no es
  más que juntar transmisor+receptor porque el hackrf tiene la capacidad de hacer ambas 
  cosas.

No hace falta decir que Flipper Zero abarca muchas más tecnologías, aunque en RF tiene
muchísimas más limitaciones (remarcar que el flipper tiene una calidad de diseño 
increíble tanto en software como hardware, en eso le da mil vueltas al portapack tbh).
Pero bueno, como dicen:

> quién mucho abarca, poco aprieta

El portapack es un proyecto aparte del hackrf, hecho por la comunidad, que añade una capa
de hardware a modo de hat/sombrero como diríamos con las raspi, para usar el hackrf como
dispositivo portátil. Le añade pantalla, botones, batería y tarjeta microSD, además de un
firmware propio para mover todo eso. El repositorio oficial está [aquí](https://github.com/portapack-mayhem/mayhem-firmware).

La parte de hardware del portapack es esta:

![Portapack Mayhem]({{site.url}}/assets/images/portapack/mayhem_edition.png "Foto por delante y por detrás del portapack edición mayhem")

En verdad esa es la edición chula que viene con serigrafía bonita, de normal es más
cutre, pero se entiende. Esto se suele vender por tiendas de Aliexpress, el propio repo 
tiene enlaces a tiendas de "confianza" (he comprado lo suficiente para saber que nadie lo
es en aliexpress). El hackrf es lo caro de todo esto, puedes comprar el original que 
supongo quetendrá más calidad... La verdad que todo el mundo compra el clon chino porque
está tres veces más barato y además te lo venden montado con el portapack (aunque
montarlo no tiene misterio).

**Reto super difícil**: intenta adivinar cuál de los dos es el original y cuál el chino
clónico en la imagen de abajo.

![HackRF original y clon]({{site.url}}/assets/images/portapack/hackrf_original_clon.png "Foto del hackrf original a la izquierda y clon a la derecha")

Os enseño las tripas también (del clon), para que veáis que tiene las hileras de pines
donde se "pincha" el portapack:

![HackRF clon sin carcasa]({{site.url}}/assets/images/portapack/hackrf_clon.png "Foto del hackrf clónico sin carcasa")

Si os fijáis en la primera imagen, dónde está la batería, se la ve rodeada de hileras de
pines. Estas encajan con las tres que rodean al chip grande que tiene la inscripción de
"NXP". Y la batería va encajada justo sobre ese IC.

## 2. Imágenes que preceden a eventos desafortunados
Me compré el portapack porque ya empecé con el tema de la radioafición, después de
obsesionarme con ello al usar el flipper zero. Siempre lo había visto como algo lejano,
difícil y caro. Pero en verdad es más barato que el flipper y las aplicaciones del
portapack son de botón gordo.

¿Qué hice al poco de recibirlo? Desmontarlo como está mandado:

![Portapack desensamblado]({{site.url}}/assets/images/portapack/portapack_desmontado.jpg "Foto del portapack desmontado")

La diferencia con el de la versión de arriba (la mayhem con serigrafías) es que está te
la venden incluso con un altavoz ya incorporado y una batería de más capacidad. En el
pack trae el cable usb y una antena teléscopica para ajustarla a la frecuencia que vayas
a usar. Una cosa graciosa de la radioafición es que muchas veces las antenas uno las 
calcula a ojo de buen cubero... He de decir que yo pagué un poco más para comprar el kit
de antenas adicionales y la verdad que no las he usado, no merecen la pena, son bastante 
mediocres.

Bueno, después del tostón, ¿cuál es la susedura? Desde hace un par de meses que la carga
de la batería no iba del todo bien. Tardaba casi un día en cargar y a mi muy normal no
me parecía así que tuve que desmontarlo y quitar la batería para ver qué pasaba. No es
algo complicado, ¿qué puede salir mal? (TODO).

## 3. Sólo el penitente pasará
Nada más empezar ya maldije a los chinos porque tenían la batería pegada con cinta de
doble cara a la placa y tuve que estar con una navaja haciendo palanca. Supongo que lo
sabréis pero no es la mejor idea del mundo acercar una cosa perforante a una batería que
al pincharla saca fuego...

Al quitarla, que me costó, tenía un problema: normalmente en las plaquillas cutres tipo 
arduino que tienen conector para batería, suele ser un jst ph 2.0 (2mm de separación
entre pines) y el de este maldito cacharro es de 1.25... Aquí una ilustración joseada de
internet:

![Diferentes formatos del conector JST]({{site.url}}/assets/images/portapack/jst_connectors.png "Conectores JST por formato")

Y claro, yo tenía por ahí de 2.0, así que para ver si la batería estaba jodida ya tuve
que desoldar el cable de la batería y ponerle uno que me sirviese.

Para cargar la batería tenía por casa unas plaquitas baratas que pillé hace tiempo porque
quería ver si las usaba en proyectos personales, pero me equivoqué y pillé unas que son
una porquería. Pero para hacer esta prueba me sirven.

Así que cambio el conector de la batería, sueldo el módulo a una placa y luego me quemo
los dedos 😑. Pero gracias a eso pude ver que cargaba:

![Carga de batería]({{site.url}}/assets/images/portapack/primera_carga_bateria.jpg "Prototipo para cargar batería")

Acercar el soldador a la batería tampoco es muy seguro, pero bueno, tras un par de horas
pude ver que la batería estaba bastante cargada. Por lo tanto pensé que el problema
estaría en el circuito de carga que traía el portapack.

He reaprovechado el conector que le había quitado a la batería para poder puentear de
nuevo la batería al portapack. Además he añadido a la plaquita un jumper para poder
seleccionar si usar el módulo externo de carga o conectar la batería al portapack:

![Prototipo carga y conexión]({{site.url}}/assets/images/portapack/prototipo_carga.jpg "Prototipo para cargar batería y conectar al portapack")

Los jumpers son el conmutador de los pobres jaja. Pero con esto puedo usarlo después para
medir la corriente que llega al cargar la batería y aparte medir el voltaje (eso lo 
veremos luego). Viendo que funciona la batería y que enciende el portapack con el
invento, meto la batería en su hueco:

![Portapack con la ñapa]({{site.url}}/assets/images/portapack/prototipo_carga2.jpg "Mismo prototipo con la batería dentro del portapack")

¡Ya con esto podemos hacer las pruebas de fuego! 🔥

## 4. Las pruebas
Descartando que fuese la batería la parte defectuosa hay que comprobar cómo carga el 
portapack. Como la batería original estaba ya llena he cogido otra que tenía por ahí
tirada de 1Ah (la original es de 2.4Ah) y he probado a cargarla midiendo con el 
polímetro.

Primero la tensión:

![Test tensión de carga del portapack]({{site.url}}/assets/images/portapack/test_voltaje.jpg "Testeando tensión de carga del portapack")

El valor me cuadra, si no me equivoco porque lo digo de memoria y tengo muy mala memoria,
para este tipo de baterías la tensión va aumentando progresivamente y la corriente es 
constante. La batería, aunque sea de 3.7v, cargada llega sobre los 4.2V.


Veamos pues la corriente:

![Prueba 1 del test de corriente de carga del portapack]({{site.url}}/assets/images/portapack/test_corriente_1.jpg "Testeando corriente de carga del portapack 1")

Decir que he puesto el polímetro en el amperímetro de fusible gordo por si acaso, no 
hacía falta, pero tampoco necesito precisión. Viendo la lectura de 14mA parece que algo
no cuadra, esa corriente no tiene sentido. He buscado en internet y por ahí decían que
probase con otros cables usb, en mi cabeza eso no tenía sentido pero por probar que no
sea.

Cojo otro cable y lo pruebo:

![Prueba 2 del test de corriente de carga del portapack]({{site.url}}/assets/images/portapack/test_corriente_2.jpg "Testeando corriente de carga del portapack 2")

¡400mA! ¡Esto pinta mejor ya! He probado varias veces cambiando de cable y con este
funciona bien. Así que: ¡EL PUTO CABLE USB! Si es que soy gilipollas, es lo primero que 
debería haber probado...

He de decir que sigo incrédulo, no acabo de fiarme y probaré más estos días. Pero por el
momento:

![Saludo amable al cable]({{site.url}}/assets/images/portapack/puto_cable_asqueroso.jpg "Saludando al cable")

## 5. La ruina
Por ahora la chapuza esta la voy a dejar así. La putada es que la carcasa no me cierra
con eso ahí colgando, y dónde cabría está pegado el altavoz que no quiero quitarle.

Al final pensando que sería la batería empecé pidiendo baterías de aliexpress porque 
tardan como dos meses. Al ser peligrosas no las pueden mandar en avión y lo hacen en 
barco, es un poco ridículo porque si pides un trasto con esa misma batería, lo mandan en
avión. Pero bueno, supongo que no soy quién para juzgarlo.

Luego pensando que igual era el circuito de carga, y aprovechando la excusa, he pedido la
placa de portapack mayhem con la serigrafía chula que vendrá con batería y mucho antes
que las baterías sueltas. Así que ahora tendré overbooking de baterías y una pcb de
sobra. Igual en un futuro compro otro hackrf para aprovecharla y no sé, igual lo regalo o
algo así.

También he comprado conectores JST de 1.25 porque las baterías que he encontado con el 
mismo formato no tenían este conector. Y me va a tocar desoldarles los cables y poner
el conector ese. Además, he pillado placas de carga de baterías de las que hubiera 
querido en un principio, por si en un futuro quisiera hacer el apaño...


Espero que al menos haya sido entretenido de leer, aunque haya sido una pérdida de tiempo
y esfuerzo por una chorrada. Tengo varias ideas pendientes de acabar y de escribir en 
este blog que espero sean más interesantes, a ver si me pongo a ello.

¡Qué tengáis buen inicio de año lectivo!

<hr>

### Update (12/10/2024)
¡Ya me han llegado las baterías y el portapack! El universo sigue conspirando contra mi.

Al final como me sobraba una de las placas y la otra si funcionaba he comprado otro
hackrf con una revisión más actualizada (estaba de oferta a 70€). Según leí, esta (r10) 
no trae mejoras, si miras el changelog de la r9 a la r10 han cambiado los componentes de
esta última a los que tenía la r8. Y decían en las discusiones de github que era por 
problemas de stock con la escasez de chips.

Aquí una foto de las dos revisiones:

![HacRF r9 y r10]({{site.url}}/assets/images/portapack/hackrf_r9_r10.png "hackrf clónicos revisiones r9 y r10 respectivamente")

Usaré la nueva con el mayhem y la anterior como estaba. Ya que estamos pongo una foto de
ambos portapack por delante y por detrás para verlo con perspectiva (he juntado las 
fotos, no tengo 4 :p):

![Portapack H2+ y H2M (ssólo placas)]({{site.url}}/assets/images/portapack/portapack_h2p_h2m.png "Portapack H2+ y H2M por delante y por detrás")

Pedí también carcasa, altavoz (porque el mayhem no lo trae instalado) y, una antena y 
estuche cutres. ¡Y salió mal también! La antena baila porque el tornillo no la aprieta
bien y de la carcasa me faltaban las tuercas de acoplamiento, tuve que improvisar como se
ve en la imagen de debajo unos tornillos de metal con tuerca para cerrarla. Y claro, pues
se lo encasqueté al portapack no tan chulo.

Y del estuche qué decir, pensé que entraría bien y tampoco, si es que...

Después de probar el mayhem edition (H2M por abreviar) he de decir que lo prefiero, y no
solo por estética: los conectores que usa para altavoz y batería son jst 2.0, que es más 
común/normal en este tipo de cacharro. Además la pila también es diferente, en el H2+ es 
una más rara (**CR1220**) mientras que en el H2M se usa la que cualquiera que sea 
mínimamente chapucillas informático ha tocado alguna vez (**CR2032**).

![Estándares H2M y H2+]({{site.url}}/assets/images/portapack/portapack_connectors_batteries.png "Diferentes conectores portapack y pilas. A la izquierda el h2+ y a la derecha el h2m")

Aquí se puede ver todo en conjunto ensamblado:

![Portapack H2M y H2+]({{site.url}}/assets/images/portapack/portapack_mayhem_and_h2_plus.png "Portapack Mayhem edition junto al H2+ y las dos baterías")

Dije que iba a dejar colgando el apaño que hice para cargar la batería pero entre el post
original y la fecha actual se me partió uno de los cables de un tirón, así que ya lo he
dejado "bien" para poder ensamblarlo y punto.

No sé muy bien que voy a hacer con dos portapacks (no sé si vender o donar uno). Y
sobretodo aún menos que hacer con esas dos baterías que ahora me sobran. :(

Antes de cerrar, y para continuar con la serie de catastróficas desdichas que he tenido
con todo esto, decir que unos días después de comprar la placa del mayhem van los
desarrolladores y sacan una versión nueva con bastantes mejoras y encima una placa hackrf
que han tuneado poniéndole puerto usb-c... :expressionless:

![Portapack H4M]({{site.url}}/assets/images/portapack/portapack_h4m.png "Nueva versión del portapack mayhem 4")

Me mola lo de tener GPIO para meter módulos, quieren acercarse a flipper zero en ese
aspecto. También lo de tener el interruptor para apagar y encender, que lo de que se
encienda con el pulsador del encoder es un coñazo, se enciende dentro de la funda a veces.
Tiene además por fin una forma menos tosca de medir el porcentaje de batería por lo que 
veo. Y lo del tipo C era un must que tardaremos en ver en la placa de hackrf original.
los botones que estaban a la derecha ahora rodean al encoder y este es más plano, eso
tambiéne es guay.

En fin, como véis todo lo que podía salir mal salió mal... Este post ha sido más un 
diario que otra cosa pero espero que a quién lo lea le haya entretenido.

# Enlaces de interés
* [HackRF](https://greatscottgadgets.com/hackrf/)
* [Repositorio Portapack](https://github.com/portapack-mayhem/mayhem-firmware)
* [Conectores JST](https://en.wikipedia.org/wiki/JST_connector)