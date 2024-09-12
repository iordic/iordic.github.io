---
title:  "Ingeniería inversa sobre software de FNIRSI"
description: "Un caso práctico de reversing sobre software en .NET"
date:   2022-01-26 20:00
categories: reversing
---
¿Qué es eso te preguntarás? Hace un par de meses compré un cacharro de AliExpress con la 
idea de usarlo para hacer una fuente de alimentación regulable barata. Vi que existía el 
cacharro este para ello y además... ¡se podía controlar desde el pc por usb! :D

Bueno pues FNIRSI es el supuesto fabricante y el dispositivo que te vende lleva el nombre
DC-580, como podría ser cualquier otra cosa. Y está AliExpress lleno de esos cacharros.
De hecho creo que he leído algunos posts o papers de dispositivos similares que usan los
mismos tipos de microcontroladores (el STM32 si no me equivoco) y no voy a afirmar nada
pero seguramente hayan copiado de algunos proyectos ya hechos.

Así que procedí a comprarlo y mientras llegaba me puse a ver el software que te 
proporciona el vendedor mediante un enlace de Mediafire (esto es solo la punta del 
iceberg de la cutrez, dejo el enlace debajo del artículo). Debo decir que era tarea tan 
fácil que antes de recibir el cacharro ya sabía cómo funcionaba e incluso implementé el 
código en Java para engañar al software haciéndole pensar que estaba interactuando con un
dispositivo real.

![dispositivo dc580]({{site.url}}/assets/images/dc580/dc580.jpg)

En la imagen vemos el cacharrín en si, la pcb que se ve a la izquierda contiene el chip
para comunicarse al PC por USB.

## Reversing del software
Uno de los motivos que me mueven a hacer esto es el software roñoso que da el fabricante,
un programa exclusivamente para Windows de código cerrado (empezamos mal) y con cosas
innecesarias (un poco bloatware pero no del todo):

* Una gráfica que va representando los valores actuales, ¿WTF? es una fuente de 
alimentación, no voy a estar modificando el valor mientras la use jajaja
* Un botón para guardar los valores de la gráfica esa a un Excel jajajaj

Aquí se puede ver una captura del software:

![software proporcionado por FNIRSI]({{site.url}}/assets/images/dc580/fnirsi_software.png)

Me descargué el zip proporcionado por el fabricante y antes de instalarlo siquiera, al
entrar a mirar el contenido del zip ya se pueden sacar un par de conclusiones útiles:

![Ficheros dentro del zip de instalación]({{site.url}}/assets/images/dc580/ficheros_instalador.png)

Uno de los ficheros es el instalador de una versión concreta del framework .NET, eso
significa que el programa está desarrollado en .NET y que decompilarlos va a ser tarea 
fácil (suelen serlo). El otro fichero interesante es el del driver, que se llama 
*CH341SER.EXE*, si tienes un mínimo de experiencia con placas de microcontroladores 
chinas es muy probable que te suene el nombre, se suele usar como interfaz de puerto 
serie a usb para programarlos, pero sino una búsqueda rápida en Google y ya. Lo de "SER"
ya va dando a entender que es para ello. Por lo tanto ya sabemos que el software se va a
comunicar mediante UART con el dispositivo, solo nos falta saber a que *baudrate* y cómo
se codifica la información. Así que... ¡a destripar el software! :D

Un software que me gusta mucho para decompilado de .NET es *dotPeek* (lo sé, lo sé, no es
FOSS... shame on me. Pero es que es una maravilla).

Pues bueno, le doy a decompilar y veo que es bastante legible (C# se parece bastante a 
Java tbh):

![Ficheros dentro del zip de instalación]({{site.url}}/assets/images/dc580/dotpeek1.png)

Básicamente todo el código que nos interesa está en el mismo sitio, es un único 
formulario con todo el código a piñón y a correr (si hija si...).

El código es bastante largo pero bueno, omito todo y dejo lo que interesa:

```cs
// ...
private SerialPort serialPort1;
// ...
this.serialPort1.Encoding = Encoding.GetEncoding("gb2312");
// ...
this.serialPort1.BaudRate = 115200;
```

Con eso afirmamos que se trata de un puerto serie con baudrate de 11520 baudios y que la
comunicación se codifica con la codificación de caracteres de China (Google -> *gb2312*).

Ahora solo falta ver que se manda y que se recibe, al final el software tiene que hacer
un tratamiento de lo que le llegue, por lo tanto podemos suponerlo. Hay que buscar la 
función que hace el tratamiento de lo recibido y cómo lo trata.

Aquí tenemos la función del botón de "Conectar" con partes omitidas:
```cs
private void btn_Connect_device_Click(object sender, EventArgs e)
    {
      this.serialPort1.ReadTimeout = 1500;
      try
      {
        if (this.Connection_device_Flag == (byte) 0)
        {
          this.btn_Connect_device.Text = "Connection";
          this.serialPort1.Write("Q\r\n");
          this.serialPort1.DiscardInBuffer();
          string a = this.serialPort1.ReadTo("B");
          if (string.Equals(a, "K") || string.Equals(a, "\r\nK"))
          {
            this.Connection_device_Flag = (byte) 1;
            this.btn_Connect_device.Text = "Disconnect";
            //...
            this.timer2.Start();
            //...
            this.tbx_show.AppendText("Connected successfully\r\n");
            this.mode = (byte) 1;
            this.label17.Text = "Model：DC-6006L";
            this.label18.Text = "Manufacturer：FEI NI RUI SI TECHNOLOGYCO.,LTD";
          }
          else
          {
            if (!string.Equals(a, "M") && !string.Equals(a, "\r\nM"))
              return;
            this.Connection_device_Flag = (byte) 1;
            this.btn_Connect_device.Text = "Disconnect";
            //...
            this.timer2.Start();
            //...
            this.tbx_show.AppendText("Connected successfully\r\n");
            this.mode = (byte) 2;
            this.label17.Text = "Model：DC-580";
            this.label18.Text = "Manufacturer：FEI NI RUI SI TECHNOLOGYCO.,LTD";
          }
        }
        else {
          //...
        }
      }
      //...
    }
    //...
    this.timer2.Tick += new EventHandler(this.timer2_Tick);
```

Se ve en el código que la información se escribe y lee en ¡TEXTO PLANO! Eso es bueno
para el reversing y malo para la eficiencia jajaja Otra cosa que se ve es un supuesto
*timer* que se empieza a ejecutar. Es decir, una función asíncrona que se ejecuta cada
X tiempo, eso significa que es la función que se ejecuta a la espera de que le lleguen
datos. Vamos a ella y la destripamos también. Esta función es una locura, la más larga
diría (dejo sólo una pequeña parte):

```cs
private void timer2_Tick(object sender, EventArgs e) {
      try {
        //...
        string[] strArray = this.serialPort1.ReadExisting().Split('A');
        int length = strArray.Length;
        this.serialPort1.DiscardInBuffer();
        switch (length) {
          case 9:
            double yValue1 = Convert.ToDouble(strArray[0]) / 100.0;
            this.textBox6.Text = yValue1.ToString("00.00") + "V";
            double yValue2 = Convert.ToDouble(strArray[1]) / 1000.0;
            this.textBox7.Text = yValue2.ToString("0.000") + "A";
            double num1 = Convert.ToDouble(strArray[2]) / 100.0;
            //...
            break;
          case 11:
            //...
            break;
          case 16:
            //...
            break;
          case 18:
            //...
            break;
        }
      }
      //...
    }
```
En resumen: ¡está recibiendo todos los valores de la fuente en texto plano, concatenados
y separados por una *A*! CSV?? More like *ASV*! jajajaj Además puede recibir diferentes
longitudes de cadena, y al hacer split de la *A* comprueba la longitud del array creado.

Bueno pues después de hacer la cuenta de la vieja y ver que valores cogía según la 
posición conseguí entender los valores que se mandaban y se recibían al completo. Al 
final he conseguido un programa hecho en Java con una interfaz similar y he publicado el
repositorio, ahora es FOSS y multiplataforma (?) :D. Los detalles al completo del 
protocolo los he escupido en la wiki de este y lo dejaré como primer enlace debajo en la
sección del enlace.

## Simulación del dispositivo
Cuando conseguí entenderlo todo decidí ver si conseguía comunicarme con el software sin
tener el dispositivo. Pero claro, no iba a programar esa locura en un dispositivo físico.
Así que opté por descargarme algún software de virtualización y encontre uno sencillito
que me sirvió: *com0com*. Con él creas puertos serie virtuales y los conectas entre ellos:

![Ficheros dentro del zip de instalación]({{site.url}}/assets/images/dc580/com0com.jpg)

Implementé en Java un programilla que se conectara en un extremo (puerto creado *COM4*) y
cada X tiempo mandara los supuestos valores de la fuente. En el otro extremo (*COM3*)
tenía el software original. El "simulador" básicamente guarda los valores que le manda el
programa de *FNIRSI* y los guarda en una clase con todos los valores actuales, luego los
replica periódicamente (no simula más que eso). Aquí enseño una captura:

![Captura de Eclipse lanzando el simulador]({{site.url}}/assets/images/dc580/dc580_simulador.png)

¡Funciona! El código del "simulador" no lo he subido a GitHub porque está un poco guarro
(más aún que el de la interfaz sí!), porque era más una prueba y porque está pensado para
que funcione en una condición muy concreta: Windows con el *com0com*. Y la verdad que
ahora mismo no me apetece intentar portarlo a GNU/Linux aposta. Pero si alguien lo quiere
ver lo puedo subir o yo qué sé.

En fin, gracias por leer si es que lo lee alguien y paciencia la tuya :)

<hr>

**Escena poscréditos**: Por lo visto alguien más tiene cosas en GitHub, lo descubrí
mientras escribía este artículo, lo dejo en la sección de enlaces también.

## Enlaces
* [Parte de mi wiki con los detalles del protocolo](https://github.com/iordic/jdcx/wiki/Serial-communication)
* [Software de PC proporcionado por FNIRSI (Mediafire)](https://www.mediafire.com/file/81wp3r44mxh8yqx/FNIRSI_DC580_PC_Software.zip)
* [Otro repo de ingenería inversa al DC-580](https://github.com/jcheger/fnirsi-dc580-protocol)
* [com0com](http://com0com.sourceforge.net/)
* [dotPeek](https://www.jetbrains.com/es-es/decompiler/)