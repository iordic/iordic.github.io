---
title:  "[Twitter backup] Método de censura basado en IP de Vodafone"
description: "Método que usa vodafone para bloquear webs de piratería y otros"
date: 2021-05-13 22:58
categories: twitter-thread torrent
twitter-thread: true
---
<div class="thread">
    <div class="tweet">
        <p>
            Venga, explico rápido sobre lo que he podido ver de la censura esta que usa 
            Vodafone, esa que han usado hoy sin querer contra un dominio que ha jodido 
            Twitch.
        </p>
        <p>Mini hilo sobre ello (o eso espero, luego acabo pasándome) 👇</p>
        <span class="number-marker">1 / 9</span>
    </div>
    <div class="tweet">
        <p>
            A ver, hay una web muy oportuna con la que se pueden hacer pruebas. Nuestra 
            querida sci-hub 🙌.
        </p>
        <p>
            ¿Por qué es perfecta para las pruebas? Porque tiene dos dominios: uno capado
            y otro no capado.
        </p>
        <p>
            <a href="http://sci-hub.se">http://sci-hub.se</a> y <a href="http://sci-hub.st">http://sci-hub.st</a> :D
        </p>
        <span class="number-marker">2 / 9</span>
    </div>
    <div class="tweet">
        <p>
            Si accedes a <a href="http://sci-hub.se">http://sci-hub.se</a> desde Vodafone
            en HTTP te va a salir un mensaje muy simpático y desde HTTPS un error de 
            certificado. Un certificado autofirmado por una  entidad española 😎, que 
            entre sus clientes tiene a Vodafone :)
        </p>
        <img src="{{site.url}}/assets/twitter/allot_censorship/censorship-thread1.png" alt="Página inaccesible debido a la censura" title="Sci-hub censurada">
        <img src="{{site.url}}/assets/twitter/allot_censorship/censorship-thread2.png" alt="Error de certificado al intentar acceder a Sci-hub" title="Error de certificado">
        <img src="{{site.url}}/assets/twitter/allot_censorship/censorship-thread3.jpeg" alt="Información del certificado autofirmado" title="certificado autofirmado Allot">
        <img src="{{site.url}}/assets/twitter/allot_censorship/censorship-thread4.jpeg" alt="Página web de allot" title="web allot">
        <span class="number-marker">3 / 9</span>
    </div>
    <div class="tweet">
        <p>
            Por otro lado, si accedes al otro dominio, todo correcto (de hecho te fuerza
            a HTTPS que con el otro no pasa).
        </p>
        <img src="{{site.url}}/assets/twitter/allot_censorship/censorship-thread5.jpeg" alt="Mirror web de Sci-hub" title="Sci-hub web">
        <span class="number-marker">4 / 9</span>
    </div>
    <div class="tweet">
        <p>
            ¿Pero cómo se puede saber que no es redirección de DNS sino modificación de 
            paquetes? Fácil, mirad lo que dice el servidor DNS para ambos. Muy parecidos 
            ¿verdad? Si buscas por GeoIP se puede ver fácilmente que pertenecen a la 
            misma compañía.
        </p>
        <img src="{{site.url}}/assets/twitter/allot_censorship/censorship-thread6.png" alt="Consulta de DNS para el dominio de sci-hub" title="nslookup sci-hub">
        <img src="{{site.url}}/assets/twitter/allot_censorship/censorship-thread7.png" alt="Información mostrada por geoip para la IP de Sci-hub" title="geoip ip sci-hub">
        <span class="number-marker">5 / 9</span>
    </div>
    <div class="tweet">
        <p>
            Falta la última pregunta, ¿Lo que nos llega es la respuestas del servidor 
            modificada o directamente nos responde a la petición el farsante? Bueno, 
            pues con esto tenemos la suerte de que ambos servidores están en el mismo 
            lugar. Podemos comparar :)
        </p>
        <span class="number-marker">6 / 9</span>
    </div>
    <div class="tweet">
        <p>
            ¿Comparar qué? Pues veréis, nuestro amigo protocolo IP es un chivato sin 
            querer, cada paquete tiene un tiempo de vida, se asigna un número y por cada 
            router que pasa se va restando 1, si llega a 0 se descarta el paquete (se usa
            para que no de vueltas sin fin).
        </p>
        <span class="number-marker">7 / 9</span>
    </div>
    <div class="tweet">
        <p>
            Y por defecto suele ser 64. A lo que voy es que se pueden comparar los saltos
            restantes para un server y para otro (porque están en el mismo lugar físico, 
            Holanda). Miremos pues en los tres casos: el primero en plano, el del 
            certificado falso y el que funciona bien.
        </p>
        <img src="{{site.url}}/assets/twitter/allot_censorship/censorship-thread8.png" alt="Demostración de TTL ruta texto plano" title="TTL captura 1">
        <img src="{{site.url}}/assets/twitter/allot_censorship/censorship-thread9.png" alt="Demostración de TTL ruta certificado cambiado" title="TTL captura 2">
        <img src="{{site.url}}/assets/twitter/allot_censorship/censorship-thread10.png" alt="Demostración de TTL ruta de web sin alterar" title="TTL captura 3">
        <span class="number-marker">8 / 9</span>
    </div>
    <div class="tweet">
        <p>
            Casualmente los paquetes del servidor legítimo aparecen casi todos con 52-53 
            saltos restantes, es decir, vienen de más lejos que los del falso. Tiene 
            sentido siendo si está ubicado en España. :)
        </p>
        <span class="number-marker">9 / 9</span>
    </div>
</div>