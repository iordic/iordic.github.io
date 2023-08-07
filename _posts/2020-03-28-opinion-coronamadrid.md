---
title:  "Opinión personal sobre la app coronamadrid"
description: "Una simple opinión y algún aspecto técnico con todo el revuelo de la aplicación"
date:   2020-03-29 22:00
categories: opinion
---
Este post lo dedicaré a dar mi opinión sobre la aplicación web que sacó la Comunidad de
Madrid para realizar un test desde casa y saber si estás contagiado de coronavirus. La
aplicación en cuestion se llama *Coronamadrid* y la puedes encontrar en:
[www.coronamadrid.com](https://www.coronamadrid.com/).


Primero que todo aclarar que lo que voy a decir aquí se trata de mi propia opinión sobre
la aplicación, si estás leyendo esto no te lo tomes como una verdad absoluta, sé lo mismo
que tú, lo que pone en la propia página de la aplicación oficial.

Yo probé la aplicación con mis datos, guardé un trazado de red en un archivo *.har* para
analizarlo un poco y ver qué se podía indagar.

## ¿Por qué tanto revuelo con esta nueva "app"?

Lo primero es entrar en contexto. Esta aplicación se supone que se creo para ayudar a que
la gente pueda consultar si está contagiada sin saturar la línea telefónica que tenía la
misma finalidad. En teoría se creó por gente voluntaria y se supone que el gobierno no ha
invertido dinero en ella, no encuentro ningún tipo de información sobre ello. En la
propia página de la aplicación no pone nada claro, así que no puedo decir mucho más.

El problema está en que pide datos de carácter privado que no son pocos, podéis acceder
al apartado de [privacidad](https://www.coronamadrid.com/proteccion-de-datos) de la página
y echarle un ojo. Los datos son:

* Nombre completo
* Nº de teléfono
* DNI / NIE
* Fecha de nacimiento
* Dirección completa
* Género
* Geolocalización (esto es opcional)
* y por último: las respuestas que marques en el propio test

La crítica vino porque en la primera versión de protección de datos no dejaban claro para
que fines se usarían esos datos y a quién se cederían. Ahora han dejado la cosa un poco
más clara, supongo que por temas legales. La desconfianza vino porque la aplicación se ha
creado en colaboración con grandes compañías que podrían tener acceso a esos datos.

## ¿Cómo podrían tener acceso a tus datos privados las empresas?

Bueno, para esto hay que hablar un poco sobre sus aspectos técnicos. Primero hay que 
mirar en su página de [preguntas frecuentes](https://www.coronamadrid.com/preguntas-frecuentes)
donde pone:

> Esta aplicación de la Comunidad de Madrid ha sido realizada por un esfuerzo conjunto de
> la Administración y de muchos profesionales de las compañías involucradas en su desarrollo.
> Entre ellas, Carto, ForceManager y Mendesaltaren, con el apoyo y colaboración de las 
> corporaciones Telefónica, Ferrovial, Goggo network y Google, que han cedido sus equipos y 
> capacidades para desarrollar esta aplicación en un tiempo récord.

¿A qué se refiere con ceder sus equipos y capacidades? Pues imagino que a los *50 hackers*
voluntarios que mencionó cierta persona de gorrito de lana y a infraestructuras como
servidores, etc...

En la página de privacidad actual pone:

> Nuestros proveedores y colaboradores, así como a las empresas que estos subcontraten, 
> los cuales nos ayudan en calidad de encargado del tratamiento al desarrollo, mantenimiento, 
> evolución y operación de la Aplicación y nos permiten prestarte los servicios a través de 
> ella. Ten en cuenta que estas empresas sólo eventualmente podrán tener acceso a tus datos 
> para llevar a cabo dichos servicios en nombre y por cuenta de la CSCM y siguiendo siempre 
> nuestras instrucciones, y por lo tanto, en ningún momento los podrán utilizar para fines
> propios y/o finalidades no autorizadas.

Por lo tanto, las empresas privadas no deberían poder usar tus datos privados para sus
propios fines. Seguramente se refiera a que tus datos privados son almacenados en un
servidor de una de estas compañías, de hecho así lo hace, se guardan en servidores de 
Google (firebase) y ahí tenemos el primer problema: dependencia de la infraestructura de
una de las compañías. De ahí el aviso de privacidad, supongo que las empresas privadas
solo tienen acceso a tus datos privados porque tienen que realizar tratamiento sobre estos
para almacenarlos y anonimizarlos.

## Empresas involucradas, ¿altruísmo u oportunismo?

Aquí viene la parte más subjetiva del post, ya que esto es totalmente opinión personal.
¿Por qué iban a meterse las empresas en esto si no obtienen ningún beneficio? Yo soy de
los que opinan que se trata de un movimiento totalmente oportunista. Y antes de explicarlo
repito que es solo mi opinión, pero en una parte del documento de privacidad pone que los
datos anonimizados se conservarán para finalidades estadísticas, investigaciones, etc...
por lo tanto creo que las grandes compañías tecnológicas que se han ofrecido voluntarias
podrían estar interesadas en almacenar todos esos datos anónimos y hacer negocios con
ellos, pero al fin y al cabo es algo que nadie puede saber.

## Aspectos técnicos

Si me sigues en twitter puede que hayas visto todo el cachondeo general que había sobre
si la aplicación consistía en hacer una suma, esto es una verdad a medias, dejando todo
el cachondeo aparte voy a tratar de explicar el por qué de esta coña :D.

Siendo sinceros, no es que la aplicación lo único que haga es sumar valores y ya está,
pero la parte que sirve para ayudar al usuario si que es básicamente eso. De hecho, 
puedes ir a este [repositorio](https://github.com/celiavelmar/open-covid19-test) donde
te explica el funcionamiento del test o puedes bajar al final del post donde pondré una
demostración simple del test que usa el mismo algoritmo que la aplicación original para
calcular si estás infectado o no.

**¿A qué me refiero con "la parte que sirve para ayudar al usuario"?**

Pues que la aplicación (como la mayoría) consta de dos partes: está el propio test para
calcular si podrías estar infectado con el virus, y luego está lo que no podemos ver, que
tiene que ver con lo mencionado anteriormente: existe toda una parte "trasera" que tiene
que ver con la recopilación de datos masivos, el tratamiento de estos, el anonimizado y
a saber si incluso sistemas de alerta, que comprenderán el grueso de la aplicación y donde
realmente se habrá puesto la mayor parte del tiempo/esfuerzo.

Por lo tanto la aplicación en sí no podemos decir que es el test, lo interesante está en
todo el conglomerado que habrá detrás de eso respecto al tratamiento de datos. El test es
simplemente la punta del iceberg si somos objetivos.

### Opinión: ¿deberías usar esta aplicación o las alternativas que están saliendo?

Mi opinión sincera: yo que se, realmente es una situación difícil, por un lado al usar
las alternativas estás evitando dar tus datos y a lo mejor, si mis especulaciones pueden
ser ciertas, evitando que las grandes empresas hagan negocio con esos datos. Esto como ya
he dicho creo que no vamos a tener forma de saberlo.

Por otro lado, si nadie la usa, es posible que sea contraproducente, ya que esos datos 
anónimos si podrían servir realmente para ayudar a la comunidad científica y aportar
beneficios para todos. De hecho lo de verificar el teléfono es una buena forma de que los
datos sean más realistas pero a mi no me hace mucha gracia dar mi número de teléfono.

Así que en tus manos queda decidir sobre el tema. No puedo decir mas que eso. Lo que si
puedo decir es que se podría haber gestionado esto de forma mucho más transparente, como
por ejemplo públicando el código fuente de toda la aplicación, dando más explicaciones
desde un primer momento, etc...

Además el envío de la información privada y muchos de los
scripts que usa el navegador están ofuscados o encriptados, y eso a mi me genera cierta
desconfianza. Supongo que podrían excusarse con el tema de la seguridad: lo del código
fuente diciendo que así podrían buscar fallos y lo de la información cifrada/ofuscada por
si tenemos algún programa que acceda a los datos del navegador o sufrimos algún tipo de
ataque MITM que acceda a las peticiones web, pero aún así creo que si buscas comprensión
lo primero es transparencia o generas desconfianza.

#### Extra: demo de test simple con JavaScript

Dejando aparte el tema político, si queréis trastear o jugar con el código aquí dejo una
pequeña demostración del test para que lo probéis sin tener que meter vuestros datos.

Como véis es tan simple que incluso lo puedo insertar en este post con Jekyll que solo
soporta contenido estático (*serverless*) y funciona perfectamente:
<form>
    <style>

#result {
    display: none;
    padding: 5px;
    font-weight: bold;
    color: darkblue;
}
legend {
    font-size: 1.5em;
    text-decoration: underline overline;
}
form {
    color: black;
    text-align: center;
    background-color: CadetBlue;
    border-radius: 10px;
    padding-top; 1em;
    padding-bottom: 1em;
}
form > input {
    margin-top: 1em;
    margin-bottom: 1em;
}
</style>
<script>
function getScore() {

    var scores = {
        'falta_aire':60,
        'fiebre':15,
        'tos':15,
        'contacto_positivo':29,
        'mucosidad':0,
        'dolor_muscular':0,
        'gastrointestinal':0,
        'mas_20_dias':-15
    };

    var total = 0;
    var radios;

    // Recorremos cada punto y para cada punto todos sus radiobuttons
    for (var i in scores) {
        radios = document.getElementsByName(i);
        for (var j = 0; j < radios.length; j++) {
            if (radios[j].checked) {
                total += scores[i] * radios[j].value;
                //console.log(i + ": " + scores[i] * radios[j].value);
                break;
            }
        }
    }

    var result_field = document.getElementById("result");
    result_field.style.display = "block";

    if (total >= 30) {
        result_field.innerHTML = "Tus respuestas sugieren la posibilidad de tener sintomatología compatible con COVID-19";  // 'con-sintomas'
    } else {
        result_field.innerHTML = "Tus respuestas sugieren que no tienes síntomas o no son suficientes para determinar un contagio de COVID-19";   // 'sin-sintomas';
    }
}
</script>
    <legend>Test de autodiagnóstico</legend>
    <br>
    <label id="result">Null</label>
    <br>
    <br>
    <label>¿Tienes sensación de falta de aire de inicio brusco (en ausencia de cualquier otra patología que justifique este síntoma)?</label>
    <br>
    <input type="radio" name="falta_aire" value="1">
    <label>Sí</label>
    <input type="radio" name="falta_aire" value="0" checked>
    <label>No</label>
    <br>
    <label>¿Tienes fiebre? (+37.7ºC)</label>
    <br>
    <input type="radio" name="fiebre" value="1">
    <label>Sí</label>
    <input type="radio" name="fiebre" value="0" checked>
    <label>No</label>
    <br>
    <label>¿Tienes tos seca y persistente?</label>
    <br>
    <input type="radio" name="tos" value="1">
    <label>Sí</label>
    <input type="radio" name="tos" value="0" checked>
    <label>No</label>
    <br>
    <label>¿Has tenido contacto estrecho con algún paciente positivo confirmado?</label>
    <br>
    <input type="radio" name="contacto_positivo" value="1">
    <label>Sí</label>
    <input type="radio" name="contacto_positivo" value="0" checked>
    <label>No</label>
    <br>
    <label>¿Tienes mucosidad en la nariz?</label>
    <br>
    <input type="radio" name="mucosidad" value="1">
    <label>Sí</label>
    <input type="radio" name="mucosidad" value="0" checked>
    <label>No</label>
    <br>
    <label>¿Tienes dolor muscular?</label>
    <br>
    <input type="radio" name="dolor_muscular" value="1">
    <label>Sí</label>
    <input type="radio" name="dolor_muscular" value="0" checked>
    <label>No</label>
    <br>
    <label>¿Tienes sintomatología gastrointestinal?</label>
    <br>
    <input type="radio" name="gastrointestinal" value="1">
    <label>Sí</label>
    <input type="radio" name="gastrointestinal" value="0" checked>
    <label>No</label>
    <br>
    <label>¿Llevas más de 20 días con estos síntomas?</label>
    <br>
    <input type="radio" name="mas_20_dias" value="1">
    <label>Sí</label>
    <input type="radio" name="mas_20_dias" value="0" checked>
    <label>No</label>
    <br><br>
    <input type="button" value="Obtener resultados" onclick="getScore()">
</form>
<br>
El código original de este formulario ocupa poco más de 100 líneas de código incluyendo el
html, css y javascript. Si le queréis echar un ojo lo podéis encontrar 
[aquí](https://gist.github.com/iordic/409c174a7428e65d802c1302d70ed8e8).



### Links de interés
 * [Repositorio de la persona que descubrió el código del test](https://github.com/celiavelmar/open-covid19-test)
 * [Url directa al código original del test](https://coronamadrid.comunidad.madrid/protocols_metadata.json)
 * [Coronamadrid](https://www.coronamadrid.com/)
 * [Archive de maldita.es sobre la versión 1 de privacidad](http://archive.is/k5x1L)
 * [La noticia actualizada de maldita.es](https://maldita.es/malditatecnologia/2020/03/24/aplicacion-madrid-coronavirus-oficial-comparte-datos-empresas/)
