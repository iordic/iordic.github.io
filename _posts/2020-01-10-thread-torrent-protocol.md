---
layout: post
title:  "[Twitter backup] Protocolo torrent"
description: ""
date: 2020-01-10 13:35
categories: twitter-thread torrent
twitter-thread: true
---
Este va a ser el primero de mis backups de los hilos que tengo fijados en Twitter. He
decidido hacer esto porque auguro un futuro incierto para la red social y/o para mi
cuenta allí. De este modo puedo tener el dominio sobre ellos y no que depende de los
caprichos personales de un multimillonario narcisista. Al fin y al cabo, esto es texto
plano con copia en mi ordenador personal y en repositorio en la nube, como aquella época
dónde el internet era realmente libre (con sus contras) y ahora solo podemos mirar con
nostalgia...

He decidido que voy a preservar el texto original, con la misma separación entre tuits y
publicándolos con la fecha original. La magia de tener el control es que puedes publicar
hacía el pasado. En fin, lo de publicar con texto original es por eso que dicen de que 
uno debe tener presentes sus errores para ver lo que ha avanzado. Y por pereza, no nos
vamos a flipar aquí tampoco. Si quisiera profundizar en uno de ellos ya haré posts
separados. Allá vamos.
<div class="thread">
    <div class="tweet">
        <p>
            Pues aquí viene, dije que haría un hilo sobre <span class="twitter-hashtag">#torrent</span>. Poneos el parche, vamos a bajar a
            la bodega virtual de las redes p2p en este hilo de torrent para dummies (el protocolo 
            bittorrent, no ese pueblo tan chulo de Valencia). Abro hilo. 😀👇
        </p>
        <span class="number-marker">1 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Si somos piratas semi experimentados sabremos que las principales formas que existen hoy 
            en día para piratear son bajarte los ficheros de alguna web, el uso de servicios de 
            streaming o el uso de torrent (u otros sistemas p2p).
        </p>
        <span class="number-marker">2 / 23</span>
    </div>
    <div class="tweet">
        <p>
            La verdad es que a efectos prácticos solo hay dos formas distintas, una es usando un 
            servidor de intermediario y el otro transfiriendo directamente ese fichero entre máquinas
            iguales. Primero veamos el esquema con servidor intermediario:
        </p>
        <img src="{{site.url}}/assets/twitter/torrent/torrent-thread1.png" alt="Compartir ficheros mediante servidor" title="Piratería centralizada">
        <span class="number-marker">3 / 23</span>
    </div>
    <div class="tweet">
        <p>
            El pirata que tiene el fichero para compartir lo sube a un servidor y el resto de
            personas acceden a ese servidor para descargarlo, en el caso de streaming es lo mismo 
            (técnicamente es una descarga temporal, no le déis mucha importancia).
        </p>
        <span class="number-marker">4 / 23</span>
    </div>
    <div class="tweet">
        <p>
        ¿Y cómo podemos compartir ficheros mediante p2p sin un intermediario? De ninguna forma...
        jajaja De hecho existe un intermediario llamado "tracker", una máquina que se encarga de 
        ayudar a que las conexiones puedan llevarse a cabo. Aunque este no almacena el fichero
        original.
        </p>
        <img src="{{site.url}}/assets/twitter/torrent/torrent-thread2.png" alt="Compartir ficheros mediante p2p con tracker" title="Piratería descentralizada">
        <span class="number-marker">5 / 23</span>
    </div>
    <div class="tweet">
        <p>
            P2P son las siglas de peer-to-peer, en este tipo de red cada máquina conectada se llama
            par, cada máquina ejerce de cliente y al mismo tiempo de servidor, es decir, descarga el 
            contenido y al mismo tiempo se lo transfiere a otros.
        </p>
        <span class="number-marker">6 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Es por esto que las productoras pueden culparte de difundir contenido pirata, a 
            diferencia del esquema anterior en este estás ayudando a propagar el contenido de forma 
            totalmente caótica (de hecho mucho más de lo que estás pensando ahora mismo, luego
            explico) ¿No es bonito? :D.
        </p>
        <span class="number-marker">7 / 23</span>
    </div>
    <div class="tweet">
        <p>
            El pirata original mantiene el fichero en su pc, crea un fichero "torrent" que contiene 
            toda la información necesaria para que el tracker sea capaz de ayudar a los demás en la 
            tarea de descarga. El creador del torrent tiene que tener el programa encendido con el 
            fichero cargado.
        </p>
        <span class="number-marker">8 / 23</span>
    </div>
    <div class="tweet">
        <p>
            El creador del torrent solo tiene que compartir el fichero con la información por el 
            medio que le plazca, lo más habitual es subirlo a alguna web, por ejemplo: la página "the
            pirate bay" (hay un documental muy chulo sobre ella llamado TPB: AFK en youtube mismo).
        </p>
        <span class="number-marker">9 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Puedes estar pensando que cómo se transfiere el fichero al mismo tiempo que lo estás 
            descargando, al fin y al cabo NO LO TIENES. Aquí viene el caos 😁. El secreto está en 
            trocear el fichero en muchas "piezas" del mismo tamaño, cada una con un índice de su 
            posición.
        </p>
        <span class="number-marker">10 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Cuando te descargas una de esas piezas por completo, tu cliente comunica al tracker que 
            la tienes lista para compartirla con otro par. El caos está en que para que no se cree un
            tapón, los clientes suelen descargar las piezas en orden aleatorio.
        </p>
        <span class="number-marker">11 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Entonces, cuando al tracker le piden cierta pieza se encarga de decirle quién la tiene 
            completa y le dice a que pares puede pedírselo. Los pares que comparten se llaman 
            "seeders" y los que descargan "leechers", todos los pares ejercen ambos roles dentro de 
            sus posibilidades.
        </p>
        <span class="number-marker">12 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Estoy intentando evitar muchos aspectos técnicos que incluso a mi se me escapan. Os pongo
            unas capturas hechas con el programa qbittorrent descargando dos ficheros completamente 
            legales (si, existen de esos xD):
        </p>
        <img src="{{site.url}}/assets/twitter/torrent/torrent-thread3.png" alt="Descargando con qbittorrent" title="Pantalla principal Qbittorrent">
        <img src="{{site.url}}/assets/twitter/torrent/torrent-thread4.png" alt="Lista de trackers mostrada en qbittorrent" title="Lista de trackers">
        <span class="number-marker">13 / 23</span>
    </div>
    <div class="tweet">
        <p>
            En la primera captura sale la información después de haber cargado el fichero, en la 
            segunda la lista de trackers. Ahí podemos reconocer términos de los que os he hablado: 
            piezas, "seeders" -> semillas, "leechers" -> pares. El ratio es el cálculo de: 
            transferido/descargado.
        </p>
        <span class="number-marker">14 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Si crees que ese ratio no tiene importancia debes saber que algunos trackers penalizan 
            a la gente que los tiene bajos, un "leecher" o más bien literalmente "sanguijuela" es un 
            término despectivo que usa la comunidad pirata para esa gente que solo descarga y no 
            comparte.
        </p>
        <span class="number-marker">15 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Ya sabemos suficientes aspectos para entender lo que viene ahora, y es que las compañias 
            han empezado a enviar "cartitas" a piratas diciendo que les pagues las pérdidas 
            económicas. ¿Cómo saben que has bajado X película? Tiene que ver con la dirección IP. 
            Vamos a ello. 👇
        </p>
        <span class="number-marker">16 / 23</span>
    </div>
    <div class="tweet">
        <p>
            La IP a efectos prácticos es algo así como la matrícula de tu coche, e internet son las 
            carreteras. Pero unas carreteras llenas de cámaras que van guardando todas las matrículas
            que han pasado por cada tramo xD. Esa "matricula" te la asigna tu compañía telefónica.
        </p>
        <span class="number-marker">17 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Cómo la conexión entre pares es directa para transferir los ficheros, cuando le pides los
            "seeders" al tracker, este te da las IPs de los pares que pueden compartir. Por lo tanto 
            solo tienes que ponerte a bajar una película e ir apuntando IPs en una lista de culpables
            xD:
        </p>
        <img src="{{site.url}}/assets/twitter/torrent/torrent-thread5.png" alt="Listado de pares mostrado por qbittorrent" title="Listado de pares en torrent">
        <span class="number-marker">18 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Luego le piden a la compañía telefónica que vaya dando nombres y ya estas cazado. Hay 
            algunas que lo hacen incluso voluntariamente sin orden judicial cosa que es de verguenza,
            pero ese es otro tema. 🙄
        </p>
        <span class="number-marker">19 / 23</span>
    </div>
    <div class="tweet">
        Aquí tenéis una página muy guay que os saca que torrents habéis descargado usando ese 
        método de registrar las IP rastreando ciertos torrents:
        <a href="https://iknowwhatyoudownload.com/en/peer/">https://iknowwhatyoudownload.com/en/peer/</a>
        <span class="number-marker">20 / 23</span>
    </div>
    <div class="tweet">
        <p>Y aquí un enlace de la documentación oficial que está interesante:</p>
        <a href="https://www.bittorrent.org/beps/bep_0003.html">https://www.bittorrent.org/beps/bep_0003.html</a>
        <span class="number-marker">21 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Pues eso sería todo. Queda en el tintero explicar cómo puedes defenderte de estas 
            denuncias. Lo primero es no pagar y lo segundo negarlo siempre: la IP no es vinculante,
            un vecino puede haberse conectado a tu wifi. Lo último es rezar para que el juez tenga 
            sentido común jajaja
        </p>
        <span class="number-marker">22 / 23</span>
    </div>
    <div class="tweet">
        <p>
            Para defenderte de forma efectiva ya hay que profundizar un poco en el tema técnico 
            (tampoco mucho), aquí un post de un compi de mastodon que está muy bien:
        </p>
        <a href="https://www.privacytools.io/">https://www.privacytools.io/</a>
        <span class="number-marker">23 / 23</span>
    </div>
</div>

### Links de interés

Aquí si que añado contexto ya que estamos:
* [(Documental) The Pirate Bay: Away From Keyboard](https://www.youtube.com/watch?v=41rwckQQ0lA&t=304s)
* [Web de qBittorrent](https://www.qbittorrent.org/)
