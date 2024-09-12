---
title:  "[Twitter backup] Usando una cámara analógica con Orange Pi"
description: "Jugando con la cámara analógica de un timbre"
date: 2020-06-05 22:58  
categories: twitter-thread torrent
twitter-thread: true
---
Este hilo me salió un poco trucho porque en realidad son tres hilos diferentes, ya que
hice tres pruebas distintas en días separados. Los voy a mantener separados aquí con una
descripción breve antes de cada uno.

Contexto: se me rompió un timbre cutre que tenía videoportero y lo destripé. Dentro traía
camára y pantalla analógica. Así que probé a enchufar la primera a la TV y luego estuve
probando con una Orange Pi y una capturadora de video. La pantalla llegué a usarla en
algún momento pero creo que la perdí y en estos hilos no hablo de ella... Estava chula 
porque podías usarla para pincharle cualquier cosa que tuviese video analógico 😺.

### Primer hilo
En el primer hilo simplemente probé a conectar a la camára destripada a una TV.

**Fecha:** *5 de junio de 2020 a las 22:58*
<div class="thread">
    <div class="tweet">
        <p>
            A ver, digamos que un telefonillo se ha roto y desmonté la parte de afuera 
            para ver que llevaba y era más simple que el mecanismo de un botijo. No voy a
            ser técnico porque tampoco hay mucho que rascar, tenía dos placas y una de 
            ellas era la que tenía la cámara:
        </p>
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread1.jpeg" alt="Primer plano de la cámara sin conectar" title="PCB camára analógica">
        <span class="number-marker">1 / 10</span>
    </div>
    <div class="tweet">
        <p>
            En la parte de abajo tenía un conector que desoldé, había 3 pines soldados y 
            uno que no. Si lo miramos desde atrás se pueden ver claramente para qué son 
            (viene indicado), voy a dibujar por encima con el paint lo que hay que saber:
        </p>
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread2.jpeg" alt="Identificación de los pines a usar por la parte delantera" title="Pines de la PCB por delante">
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread3.jpeg" alt="Identificación de los pines a usar por la parte trasera" title="Pines de la PCB por detrás">
        <span class="number-marker">2 / 10</span>
    </div>
    <div class="tweet">
        <p>¿Por qué leds infrarrojos? Obvio, ¡visión nocturna! 🤣.</p>
        <p>
            Y claro un pin para el video significa que eso es más analógico que la dinamo
            de la bici de mi abuelo, así que una tele debería reconocerlo (quizás?), 
            pues probemos :D
        </p>
        <span class="number-marker">3 / 10</span>
    </div>
    <div class="tweet">
        <p>
            Vaya espera, aquí tengo un problema, mi tele no tiene pin de video compuesto
            para entrada :(((. Solo un SCART, y si algo enseñan en la ingeniería es la 
            premisa: un ingeniero resuelve problemas con las herramientas de las que 
            dispone, o lo que es lo mismo: ¡hora de ñapas!
        </p>
        <span class="number-marker">4 / 10</span>
    </div>
    <div class="tweet">
        <p>
            Buscamos los pines de scart en google y tiene dos para entrada de video :D, 
            así que teóricamente se podría con este esquema:
        </p>
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread4.png" alt="Esquema de conexión de la pcb a SCART" title="Esquema conexión">
        <span class="number-marker">5 / 10</span>
    </div>
    <div class="tweet">
        <p>
            Pillo un cable cutre y hago una ñapa que avergüence a todos los profesores 
            que algún día decidieron enseñarme electrónica :D
        </p>
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread5.jpeg" alt="Cable que usé para conectar la camára" title="Cable a usar">
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread6.jpeg" alt="Cable soldado a la placa de la camára" title="Cable soldado a la placa">
        <span class="number-marker">6 / 10</span>
    </div>
    <div class="tweet">
        <p>
            Lo alimento con un transformador de 12v que tengo por ahí tirado y a ver qué 
            pasa, pero ¿cómo sabemos que funciona antes de pincharlo a la tele? 
            ¿Recuerdas los leds IR? Nosotros no podemos verlos pero el teléfono con su 
            cámara si 😏
        </p>
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread7.jpeg" alt="Vista de los leds IR funcionando desde el teléfono" title="Camára encendida">
        <span class="number-marker">7 / 10</span>
    </div>
    <div class="tweet">
        <p>¡Y voila! Hasta a oscuras :D</p>
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread8.jpeg" alt="Camára funcionando y mostrando la imagen en la televisión" title="Camára funcionando">
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread9.jpeg" alt="Camára siendo sujetada por un soporte de soldadura" title="Pseudosoporte de camára">
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread10.jpeg" alt="Camára funcionando y mostrando la imagen a oscuras" title="Camára funcionando a oscuras">
        <span class="number-marker">8 / 10</span>
    </div>
    <div class="tweet">
        <p>Y esta es la parte que no queréis ver jajaja</p>
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread1/camera-thread11.jpeg" alt="Mostrando la conexión de la camára al conector scart de la televisión" title="Conexión de camára a SCART">
        <span class="number-marker">9 / 10</span>
    </div>
    <div class="tweet">
        <p>
            La calidad no es la mejor pero oye... ¿La putada? Que molaría tenerlo en una
            rpi pero es analógica, creo que la solución es pincharla a una capturadora de
            video. Tengo una en Valencia, en verano podría probar, si lo hago ya lo 
            enseñaré pero no sé hasta que punto compensa. FIN.
        </p>
        <span class="number-marker">10 / 10</span>
    </div>
</div>

### Segundo hilo
En este hilo, si se puede llamar así porque tiene cuatro tweets, estuve probando a
conectar la camára a una capturadora de video que a su vez estaba conectada a la
Orange Pi Zero (la 1).

**Fecha:** *10 de julio de 2020 a las 14:15*
<div class="thread">
    <div class="tweet">
        <p>
            Recordáis que dije que probaría la cámara del timbre con una capturadora 
            conectada a la rpi, pues funciona, pero esto es lo que hay que liar para 
            hacerlo jajaj
        </p>
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread2/camera2-thread1.jpeg" alt="Foto mostrando el montaje de la camára con la capturadora" title="Montaje del prototipo">
        <span class="number-marker">1 / 4</span>
    </div>
    <div class="tweet">
        <p>y funciona:</p>
        <span class="number-marker">2 / 4</span>
        <img src="{{site.url}}/assets/images/twitter/analog_camera/thread2/camera2-thread2.png" alt="Captura mostrando el resultado de ejecutar un comando para sacar fotografía con la camára" title="Resultado">
    </div>
    <div class="tweet">
        <p>En verdad todo era una excusa para beberme una cerveza, a vuestra salud :)</p>
        <span class="number-marker">3 / 4</span>
    </div>
    <div class="tweet">
        <p>
            What's next: hacer una web-gui sencillita que devuelva feedback en tiempo 
            real, haciendo una captura cada segundo ? No se 🤔
        </p>
        <span class="number-marker">4 / 4</span>
    </div>
</div>

### Tercer hilo
Al final realicé una pequeña y cutre aplicación web para tener una especie de "IPCam".
Lo programé usando Flask, que os recomiendo para proyectos así porque facilita mucho las 
cosas, y metí un servomotor para poder girar la camára.

El código no es muy fino pero dejo abajo el enlace al repositorio por si queréis echarle
un ojo. Es un fork de otro que encontré, adaptado a la Opi y metiendo la parte del servo.


**Fecha:** *27 de Julio de 2020 a las 21:43*
<div class="thread">
    <div class="tweet">
        <p>keep it cutre</p>
         <img src="{{site.url}}/assets/images/twitter/analog_camera/thread3/camera3-thread1.jpeg" alt="" title="">
         <img src="{{site.url}}/assets/images/twitter/analog_camera/thread3/camera3-thread2.png" alt="" title="">
        <span class="number-marker">1 / 3</span>
    </div>
    <div class="tweet">
        <p></p>
        <video width="640" height="360" controls>
          <source src="{{site.url}}/assets/videos/twitter/analog_camera/thread3/camera3-thread3.mp4" type="video/mp4">
            Parece que tu navegador no soporta la etiqueta de video :(
        </video>        
        <span class="number-marker">2 / 3</span>
    </div>
    <div class="tweet">
        <p>
            El código para streamear la cámara lo he copiado vilmente, no me escondo (es 
            usar opencv y ya). Haré fork al repo que he copiado añadiendo lo del servo 
            jaja
        </p>
        <span class="number-marker">3 / 3</span>
    </div>
</div>

### Links de interés
* [Código del miniproyecto](https://github.com/iordic/OPiZ-Camera)