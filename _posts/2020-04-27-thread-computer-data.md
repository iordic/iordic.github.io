---
title:  "[Twitter backup] Tratamiendo de información por un ordenador"
description: "Hilo de Twitter para explicar el tratamiento de la información que realiza un ordenador"
date: 2020-04-27 17:30  
categories: twitter-thread torrent
twitter-thread: true
---

<div class="thread">
    <div class="tweet">
        <p>Empiezo hilo explicando como guarda el pc la info :).</p>
        <p>
            Lo primero que tienes que entender es que todo está basado en el 
            funcionamiento del famoso transistor. ¿Qué es? Pues se podría hacer un hilo 
            solo para este y te quedarías corto pero voy a explicar solo lo que nos 
            interesa. 👇
        </p>
        <span class="number-marker">1 / 38</span>
    </div>
    <div class="tweet">
        <p>
            Es un componente electrónico que deja pasar más corriente o menos, entre dos 
            zonas, según la corriente que "inyectemos" en una tercera (hay mucha mierda 
            de química y física por detrás interesante :O ). Os pongo un gif con su 
            representación y os lo explico abajo.
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread1.gif" alt="Funcionamiento transistor" title="GIF transistor">
        <span class="number-marker">2 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Bueno veréis esas 3 "zonas" como "colector", "base" y "emisor". Básicamente 
            si recibe corriente por la base deja que fluya corriente desde el colector 
            al emisor (dirección de la flechita). ¿Parece estúpido verdad? ¿Qué ganamos 
            con esto? ¿Por qué no meter esa corriente a mano?
        </p>
        <span class="number-marker">3 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Pues una característica que tiene es que se puede ajustar para que solo 
            funcione en 2 estados: corte o saturación (es decir, o no pasa nada o pasa 
            todo) y a la entrada se le pueden meter componentes que añaden retrasos de 
            tiempo o cambian periódicamente. Por lo que le da vidilla
        </p>
        <span class="number-marker">4 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Como seres humanos necesitamos representar ese todo o nada sobre el papel, lo
            escribimos como 0 o 1 y ¡Boom! lógica digital. Ahí tenemos el motivo de 
            porque todo está en binario, no tenemos otra forma de guardar valores en 
            estos componentes y poder recuperarlos.
        </p>
        <span class="number-marker">5 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Ahora tenemos una forma de guardar 2 valores distintos, ¿Pero para que nos 
            sirve eso? Recuerda que nuestros números solo tienen  10 valores distintos 
            0-9 (sistema decimal), así que muy distinto no es, hacemos igual, dar 
            importancia según la posición. :D
        </p>
        <span class="number-marker">6 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Ahora se vienen las mates, voy a poneros este ejemplo. Tenemos un número con 
            varios dígitos y según la posición que ocupen tendrán un valor u otro.
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread2.jpeg" alt="Descomposición de entero en binario" title="Entero a binario">
        <span class="number-marker">7 / 38</span>
    </div>
        <div class="tweet">
        <p>
            No voy a explicar como pasar de un sistema a otro en profundidad, es 
            básicamente ir dividiendo entre 2 y guardando los restos hasta que el último 
            cociente sea 0 o 1, un coñazo. Ya tenemos números en nuestros transistores, 
            ahora esta todo rodado ¿verdad? :D
        </p>
        <span class="number-marker">8 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Mas o menos, pero guardar dígito a dígito es un rollo, mejor agruparlos, 
            además habrá que entenderse con el vecino. Las máquinas tienen que trabajar 
            con un número fijo de bits, sus componentes no son infinitos, aquí es donde 
            llega nuestro amigo el nipple (agrupaciones de 4 bits).
        </p>
        <span class="number-marker">9 / 38</span>
    </div>
        <div class="tweet">
        <p>
            ¿Pensabas que iba a decir byte verdad? Paciencia. Al agruparse 4 bits se 
            pueden representar 16 valores distintos, desde 0000 hasta 1111 (combinatoria 
            yay!), ahí es donde se inventó el sistema de representación "hexadecimal" 
            cuyos valores van desde 0 a 9 y siguen de A a F.
        </p>
        <span class="number-marker">10 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Ahora toca el byte, el byte son 2 nibbles (8 bits agrupados). ¿Por qué un 
            byte? Pues resulta que los primeros procesadores de intel trabajaban con 8 
            bits simultáneos, no hay una lógica. ¿Por qué no representar cada valor del 
            byte? Porque un byte puede tener 256 valores distintos.
        </p>
        <span class="number-marker">11 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Y luego sigo que me toca hacer un rato de práctica, esto va a ser tremenda 
            turra... Tengo que explicar como se guardan números enteros, negativos, 
            decimales, texto, ficheros... F.
        </p>
        <span class="number-marker">12 / 38</span>
    </div>
        <div class="tweet">
        <p>
            ¿Qué significa que trabajaba con 8 bits? Pues que el procesador hace cálculos
            matemáticos con 8 bits simultáneos (parecen pocos verdad, el número más 
            grande es 255), si necesitan más solo tienen que extender al siguiente byte 
            (por así decirlo).
        </p>
        <span class="number-marker">13 / 38</span>
    </div>
        <div class="tweet">
        <p>
            La memoria del ordenador (ram o disco) guarda la info en bytes. Habrás oído 
            eso de los 32 o 64 bits, es porque los procesadores modernos hacen 
            operaciones con esa cantidad (4 u 8 bytes, a esto se le llama "palabra").
        </p>
        <p>¿Vale, ahora que pasa con los números negativos? 👇</p>
        <span class="number-marker">14 / 38</span>
    </div>
        <div class="tweet">
        <p>
            La primera opción es usar el bit más a la izquierda y ponerlo a 0 si es 
            positivo o a 1 si es negativo (pues no xD). Estamos perdiendo bastante 
            capacidad con esto, lo que se hace es el famoso "complemento a 2", es cambiar
            todos los bits a de 0 a 1 o de 1 a 0 y sumarle 1 a todo.
        </p>
        <span class="number-marker">15 / 38</span>
    </div>
        <div class="tweet">
        <p>
            ¿Por qué? Bueno, puedes buscar información, pero básicamente es una operación
            que permite guardar números positivos y negativos fácilmente sin perder rango.
            Una demostración de esto en C, podemos ver que se usan 32 bits para los nº, 
            los "enteros" en C usan este tamaño de palabra.
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread3.png" alt="Demostración en C número entero negativo" title="Formato entero negativo en C">
        <span class="number-marker">16 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Bueno, no es preciso entender el código, sólo ver el resultado de abajo. Con 
            esto ya quedaría explicado el tema de enteros, ahora vendría explicar los 
            números decimales (o de "coma flotante" que es como se conoce en informática 
            :D). Para ello se usa un estándar que se lama IEE754
        </p>
        <span class="number-marker">17 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Así es un número representado de esta forma, aquí si se guarda el bit de 
            signo :). Y es una movida, no es difícil de entender pero tiene más chicha. 
            Aquí tenéis un enlace para jugar con la explicación: 
            <a href="https://h-schmidt.net/FloatConverter">https://h-schmidt.net/FloatConverter</a>
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread4.jpeg" alt="Diagrama de bloques IEEE 754" title="Formato IEEE 754">
        <span class="number-marker">18 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Lo resumiré en que el exponente son potencias del estilo 2^n-127 y en la 
            mantisa 2^1/(pos*2) si no me equivoco, pero vamos, es un poco lío. Existen 
            números flotantes de precisión simple (32 bits) o precisión doble (64 bits). 
            En programación se suelen conocer como float y double.
        </p>
        <span class="number-marker">19 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Después de tanto lío de matemáticas de las que habrás pasado llega la hora 
            del texto, te presento ASCII. Es una codificación que se creo para asignar 
            números a caracteres y se usaban 7 bits en total. Con esto puedes obtener 128
            caracteres diferentes.
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread5.jpeg" alt="Tabla de valores ASCII" title="Tabla ASCII">
        <span class="number-marker">20 / 38</span>
    </div>
        <div class="tweet">
        <p>
            ¿Por qué 7 bits y no 8 que son un byte? Bueno, realmente eso vino después 
            pero sí, se han sacado otras que lo amplían: 
            <a href="https://es.wikipedia.org/wiki/Codificaci%C3%B3n_de_caracteres">https://es.wikipedia.org/wiki/Codificaci%C3%B3n_de_caracteres</a>.
        </p>
        <p>
            Como introducción a los ficheros, el más simple que existe se llama fichero 
            de texto plano y no es más que un conjunto de bytes ASCII
        </p>
        <span class="number-marker">21 / 38</span>
    </div>
        <div class="tweet">
        <p>
            En linux tenemos un comando que sirve para mostrar los bytes en hexadecimal 
            de cualquier fichero (xxd), aquí os pongo un ejemplo del contenido de un 
            fichero de texto plano. Podéis ver que cada 2 dígitos hexadecimales son una 
            letra, el valor que podéis comparar en la tabla ASCII.
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread6.png" alt="Ejecución del comando xxd sobre un fichero de texto plano" title="xxd ascii">
        <span class="number-marker">22 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Bueno, ¿significa eso que todos los ficheros están formados por carácteres en
            ASCII? Pues no, existen ficheros que tienen valores binarios puro en formatos
            muy distintos, hay un comando en linux para decirte de que tipo de fichero se
            trata (file), y es bastante útil.
        </p>
        <span class="number-marker">23 / 38</span>
    </div>
        <div class="tweet">
        <p>
            En windows según la extensión se asocia a un tipo u otro, a linux la 
            extensión le da igual, mirad dos ejemplos, el de un fichero de texto normal y
            una imagen quitándole la extensión para ver qué pasa:
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread7.png" alt="Comando file sobre texto plano" title="File sobre texto plano">
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread8.png" alt="Comando file sobre fichero sin extensión reconociendo el formato" title="File sobre png sin extensión">
        <span class="number-marker">24 / 38</span>
    </div>
        <div class="tweet">
        <p>
            ¿Cómo lo sabe? Pues porque los ficheros suelen tener una  una cosa llamada 
            "número mágico", es un valor de 2-4 bytes que se pone al principio de cada 
            fichero, que identifica de forma única el tipo de fichero, ¡a veces incluso 
            son carácteres legibles! Os lo demuestro:
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread9.png" alt="mostrando magic number de fichero con xxd" title="Magic number de png">
        <span class="number-marker">25 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Seguro que leéis algo familiar 😏. ¿Y como es capaz de representarse con todo 
            eso una imagen?, pues cada tipo de fichero se tiene que tratar de una forma, 
            existen desde las estructuras más simples a las más complejas. Aquí hay 
            diagramas muy chulos :D: 
            <a href="https://github.com/corkami/pics/tree/master/binary">https://github.com/corkami/pics/tree/master/binary</a>
        </p>
        <span class="number-marker">26 / 38</span>
    </div>
        <div class="tweet">
        <p>
            ¿Queréis que lo demuestre? Pues mirad, existe otro comando en linux para 
            editar binarios puros "hexedit", vamos a quitarle el número mágico a ver qué 
            pasa. Incapaz de decir otra cosa que no sea "data" 😎
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread10.png" alt="Alterando el magic number de un fichero con hexedit" title="comando hexedit sobre fichero png">
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread11.png" alt="Resultado de ejecutar file sobre fichero png con el magic number alterado" title="comando file sobre fichero con magic number modificado">
        <span class="number-marker">27 / 38</span>
    </div>
        <div class="tweet">
        <p>
            De hecho, técnicas como esta de borrar el número mágico hay gente que las 
            hace para borrar el encabezado de particiones de disco cifradas y que la 
            policía no sepa que tipo de cifrado están usando, pero eso es tema aparte 😇.
        </p>
        <span class="number-marker">28 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Por cierto, los ficheros en texto plano son el tipo de fichero más simple que
            existe porque ni siquiera tienen número mágico, si has vuelto a atrás para 
            comprobarlo no te asustes, es normal. Con esto no me refiero a los "word", 
            esos si tienen estructura compleja.
        </p>
        <span class="number-marker">29 / 38</span>
    </div>
        <div class="tweet">
        <p>
            En fin, supongo que llega la hora de hablar de ejecutables, en linux se 
            llaman ELF y tienen número mágico también. Aprovecho para presentaros otro 
            comando útil de linux "strings", te saca todas las frases legibles de un 
            fichero independientemente del formato.
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread12.png" alt="Introducción a fichero ELF" title="fichero elf">
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread13.png" alt="Comando strings sobre fichero elf" title="Comando strings">
        <span class="number-marker">30 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Vale, ¿y cómo es una máquina capaz de ejecutar un programa? Lo intentaré 
            simplificar pero realmente la estructura de un ejecutable es bastante 
            compleja, me centraré en las instrucciones, independientemente del lenguaje 
            de programación, necesitas que el pc entienda lo que le digas
        </p>
        <span class="number-marker">31 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Para eso tienes que "transformar" tu lenguaje en el suyo a través de un 
            proceso que se llama compilación y el programa que lo hace compilador, no es 
            más que un traductor a grandes rasgos. El número de operaciones que entiende 
            una máquina es bastante limitado.
        </p>
        <span class="number-marker">32 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Del ejemplo anterior podemos ver el código en C y el código despues de 
            compilar. Un churro inmenso de bytes incapaces de leer por una persona, 
            el compilador coge tu código (independiente de la máquina que tengas) y lo 
            convierte para que lo reconozca tu máquina.
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread14.png" alt="Código fuente básico en C" title="Código C básico">
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread15.png" alt="Comando xxd sobre fichero compilado ELF" title="xxd sobre ELF">
        <span class="number-marker">33 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Las operaciones que puede ejecutar un procesador se representan mediante 
            bytes puros y se llaman opcodes, el lenguaje más cercano a estos se llama 
            "ensamblador" y cada tipo de máquina tiene su propio lenguaje ensamblador. Y 
            casi cada instrucción de este tiene un opcode asociado.
        </p>
        <span class="number-marker">34 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Eso quiere decir que entre otras muchas cosas un ejecutable tiene las 
            instrucciones (muy ligadas a ensamblador) dentro de este fichero, se puede 
            hacer una operación inversa y convertir a ensamblador, a esto se le llama 
            desensamblado y es útil para realizar ingeniería inversa.
        </p>
        <span class="number-marker">35 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Es decir, cuando queremos ver el funcionamiento de un programa sin tener 
            acceso al código fuente, podemos intentar ver su código empaquetado. Aquí 
            muestro un desensamblado del código anterior una vez compilado (con el 
            programa radare2):
        </p>
        <img src="{{site.url}}/assets/images/twitter/pc_info/pc-info-thread16.png" alt="decompilado de función main con radare" title="radare sobre método main">
        <span class="number-marker">36 / 38</span>
    </div>
        <div class="tweet">
        <p>
            Y creo que lo dejaré ahí, podría extenderme más pero pff, es demasiada info. 
            Cada 2 tuits se podrían ramificar en otro hilo la verdad, no se yo si se 
            pillarán las cosas pero bueno. Aquí lo dejo hasta que me apetezca hacer otro 
            hilillo. ¡Adiós! 🙃
        </p>
        <span class="number-marker">37 / 38</span>
    </div>
        <div class="tweet">
        <p>Estaría mejor un video jugando con la terminal :)</p>
        <span class="number-marker">38 / 38</span>
    </div>
</div>