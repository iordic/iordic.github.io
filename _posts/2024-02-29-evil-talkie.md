---
title:  "EvilTalkie: ¿evilcrow puede hablarte por el walkie?"
description: "Usando Arduino para enviar TTS por radio"
date:   2024-02-29 15:00
categories: radio electronica hacking
---

Te preguntarás de dónde viene un nombre tan molón, simplemente es producto del mejunje de
cosas que vamos a tocar aquí. Tranqui, que no te voy a vender ninguna cosa, solo a pedir
comida.

La idea que tuve es simple: ¿se podrá usar el módulo CC1101 para mandar TTS a los walkie
talkies sin licencia (aka PMR446)?

Es el módulo que usar Flipper Zero y EvilCrowRF (a partir de ahora ecrf), y siempre con 
estos cacharros uno piensa en "hackear" cosas, pero no han tenido en cuenta lo divertido 
que podría suponer poder hacer que se raye alguien porque un robot le está hablando por 
walkie.

## 1. Lo que nos dice la teoría
He de decir que empiezo a escribir este artículo sin tener aún nada probado, porque no
tengo a mano ahora mismo lo necesario para probar si mi teoría es acertada. Si no lo es,
es posible que nunca lleguéis a ver este post, o puede que sí para que veáis que no todos
los proyectos acaban saliendo "bien" o son viables. De corazón espero que funcione.

En teoría uno puede pensar que es imposible, al fin y al cabo, el cc1101 es un chip que
está pensado para mandar señales digitales. Se modulan los 0 y 1 con la portadora según
el tipo de modulación que usemos (FM, AM, PM, etc... no se si hay más tipos ahora mismo).
En esta imagen joseada se puede ver un ejemplo:

![image]({{site.url}}/assets/evil_talkie/signal-modulation-methods-binary-digits-amplitudes-series.jpg)

En cambio, los walkies tipo PMR446 de toda la vida no usan sistema digital en ningún 
momento (espero que no vengan los coleguis radiofrikis a funarme porque hoy en día es mentira):
se mezcla la señal portadora con la señal moduladora (en este caso algo tan analógico 
como tu voz) y se transmite. Cuando llega al receptor se separan las señales y se 
reproduce la moduladora sin necesidad de usarse un proceso de decodificación digital,
con un circuito analógico basta.

¿Y por qué os cuento este rollo? El cc1101 en ningún momento va a poder mezclar audio con
la portadora porque simplemente no funciona así. Tú le dices los bytes que quieres mandar
y este modula la señal para representar esa información.

Pero ahora bien, hay una trampa que se usa en electrónica digital para reproducir sonido:
el uso de señales de pulsos (aka PWM) para reproducir determinados pitidos. Y en base a
estas señales se han creado sintetizadores de música o diferentes algoritmos TTS (texto 
a voz) bastante rudimentarios. La cosa es que son capaces de reproducir sonidos usando 
solamente valores de nivel alto o bajo usando una mezcla de frecuencias determinadas.

Hay una cosa más sobre el cc1101 que no he contado: se puede poner en modo "asíncrono" y
variar a mano el cambio de estado entre bits usando uno de sus pines (GDO0) en el momento
que se quiera. Es como funciona el protocolo RAW de flipper zero, por ejemplo, este trozo:

`RAW_Data: 361 -68 2635 -66 ...`

Se representaría como: la señal tiene nivel alto durante 661 microsegundos (us), cambia a 
bajo durante 68us, otra vez a alto durante 2635us, etc.. Si te interesa indagar, puedes 
leer sobre ello [aquí](https://github.com/flipperdevices/flipperzero-firmware/blob/dev/documentation/file_formats/SubGhzFileFormats.md#raw-files).

Sabiendo que el cc1101 es capaz de trabajar con microsegundos y que las frecuencias 
audibles apenas superan el kilohercio (orden de milisegundos) se puede suponer que es 
perfectamente capaz de detectar los cambios de nivel de una señal PWM destinada a 
producir sonido sin perder info. Y si esa señal se puede escuchar inyectando directamente
la salida PWM a un altavoz, debería también serla después de que un equipo analógico 
demodule la recibida porque en teoría sería la misma señal ☝️🤓.

> A todo esto decir que no es que vaya a ser muy útil, solo es por la cencia que no se ace
  sola, en verdad este módulo apenas tiene alcance y los walkies pueden filtrar lo que 
  escuchan configurando subtonos para no oír a otra gente en el mismo canal. Así que si
  alguien lo quiere usar, teniendo como idea hacer daño de verdad, lo tiene crudo. De hecho
  el propio ecrf/flipper zero tienen apl´icaciones para hacer jamming pero no es que
  sirvan para mucho con la potencia a la que emiten.

Y hasta aquí lo teorizado, espero que no te hayas dormido, ahora falta que funcione la
parte práctica.

## 2. ¿Qué necesitamos?
Pues en mi cabeza está todo esto:
- El ecrf
- La librería que maneja el cc1101
- Librería para la síntesis de voz

¿Pero qué tiene que ver todo esto con el ecrf? Pues que es muy fácil de programar
porque se puede hacer con Arduino, de hecho el software que usa está hecho así y doy 
gracias a Joel (su creador) por ello. He aprendido como manejar el cc1101 y cómo funciona
leyendo el código del repositorio de ECRFv2. Y como ya viene todo ensamblado en la placa
se puede probar el código directamente.

He encontrado esta librería en la web de Arduino, que viendo las demos, parece que suena
muy bien: [Talkie](https://www.arduino.cc/reference/en/libraries/talkie/).

Casualmente, mirando el repositorio pone que para el ESP32 (microcontrolador que usa ecrf),
se usa el pin 25 para generar la señal. Ese pin está conectado al GDO0 del segundo módulo 
que usa ecrf, y si recordáis lo antes dicho, es el pin por donde se emiten la señal de 
forma asíncrona. Por lo tanto parece que sea el destino 😎.

> **Nota post-experimentación**: en verdad no estoy seguro que lo que use esta librería sea
  PWM, tiene más pinta de ser DAC (conv´ersión de digital a analógico) que llevan algunos
  microcontroladores.

## 3. ¡Al lío!
Es la hora de probar cosas, después de pelearme un poco con el IDE de Arduino que es un
petardo, he conseguido ciertos resultados pero no estoy contento del todo. Os enseño el
resultado:

<video width="640" height="360" controls>
    <source src="{{site.url}}/assets/evil_talkie/alpha-bravo-charlie.mp4" type="video/mp4">
    Parece que tu navegador no soporta la etiqueta de video :(
</video>

Es básicamente un programa diciendo en bucle "Alpha Bravo Charlie". ¿Por qué estás
palabras y no otras? Pues porque eran las que tenía a mano, porque tienen relación con
la radioafición y esas cosas... (en verdad estuve buscando insultos pero no he encontrado).
Las palabras de ese lenguaje vienen [aquí](https://github.com/ArminJo/Talkie/blob/master/src/Vocab_US_Large.cpp)
(hay otros).

El SDR de fondo es para que veáis la señal y porque lo he tenido que usar para encontrar
la frecuencia, lo que se escucha es en el baofeng. Ahora podrán decirme que voy a ir a la
cárcel por haber emitido en esta frecuencia, si lo lee el señor juez, lo he hecho en
laboratorio controlado en una habitación con jaula de faraday y mi madre me dió permiso.

El código es bastante sencillo:

```c++
#include "Talkie.h"
#include "Vocab_US_Large.h"

#include "ELECHOUSE_CC1101_SRC_DRV.h"   // He usado la modificada que viene en el repo de ecrfv2

Talkie voice;

// Pines usados por ecrf
#define CC1101_SCK  14
#define CC1101_MISO 12
#define CC1101_MOSI 13
#define CC1101_SS0   5 
#define CC1101_SS1 27

#define MOD0_GDO0 2
#define MOD0_GDO2 4
#define MOD1_GDO0 25
#define MOD1_GDO2 26


void setup() {
    Serial.begin(9600);
    // Esta parte me la copié del código refactorizado que hice de ecrf por no pensar mucho (solo uso el segundo)
    ELECHOUSE_cc1101.addSpiPin(CC1101_SCK, CC1101_MISO, CC1101_MOSI, CC1101_SS0, 0); // (0) first module
    ELECHOUSE_cc1101.addSpiPin(CC1101_SCK, CC1101_MISO, CC1101_MOSI, CC1101_SS1, 1); // (1) second module
}

void loop() {
  Serial.print("Speaking...");
  // Esto igual se puede mover a setup() ya que no se cambian los parámetros, pero era una prueba rápida
  ELECHOUSE_cc1101.setModul(1);
  ELECHOUSE_cc1101.Init();
  ELECHOUSE_cc1101.setModulation(0);
  ELECHOUSE_cc1101.setMHZ(446.03125);   // frecuencia del canal 3 de PMR446
  ELECHOUSE_cc1101.setDeviation(0);
  ELECHOUSE_cc1101.SetTx();
  // Aquí se empieza a decir que palabras usar por orden
  voice.say(sp2_ALPHA);
  voice.say(sp2_BRAVO);
  voice.say(sp2_CHARLIE);
  ELECHOUSE_cc1101.setSidle();
  Serial.println("DONE");
  delay(2000);
}
```

## 4. Conclusiones y posible futuro
¿Qué opináis? El resultado es bastante decepcionante, por un lado mi teoría era cierta,
pero por otro han surgido problemas que no esperaba tbh. 

Los problemas que hay, si os habéis fijado, son:
1. El ancho de banda es demasiado grande para NarrowFM, aunque se puede escuchar más o 
   menos, he estado mirando la documentación y no tiene pinta de poderse ajustar. Parece
   que depende bastante del cristal que lleve la placa.
2. La frecuencia que le pongo acaba siendo diferente de la que le digo y tengo que
   buscarla a mano, por lo que no se escucharía tampoco en un canal estándar de PMR446.
   Esto debe ser por el tipo de señal que le llega por GDO0, igual hay que jugar con
   offset si se puede o algo así, o ajustarla echando la cuenta de la vieja.

Si consigo hacer que funcione bien me gustaría intentar una cosa más difícil: crear una
app en flipper zero para que funcione con SAM emitiendo lo que le escribas. Ahora existe
una app que hace eso pero por el altavoz, sería adaptarla. Primero probaré SAM en el ecrf,
pero eso será en otro post porque no tengo ganas de ponerme ya y me ha quedado extenso.

⚠️ Nunca está de más deciros que no hagáis esto por ahí si no sabéis un mínimo de normativa
sobre radiofrecuencia, no quiero tener que ir a buscaros al calabozo a pagar la fianza
porque me siento responsable 😾.

Y aquí cierro, ¡gracias por leer! :)

# Enlaces de interés
* [Librería Talkie para Arduino](https://github.com/ArminJo/Talkie)
* [Repositorio EvilCrowRFv2](https://github.com/joelsernamoreno/EvilCrowRF-V2)
