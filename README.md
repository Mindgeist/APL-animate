# APL-animate
## Alexa APL Animation Library
Esta es una librería con una colección de animaciones que pueden aplicarse a objetos de Alexa APL. 
Implementa con las transformaciones disponibles con AnimateItem algunas de las animaciones implementadas en [Animate CSS](https://animate.style/), la adaptación original es de https://github.com/arjun-g/apl-transitions 

## Tabla de contenidos

* [Alexa APLS](#qu%C3%A9-es-alexa-apl)
* [Documentación](#documentaci%C3%B3n) 
  * [Documento APL](#documento-apl)
  * [Paquetes APL](#paquetes-apl)
  * [Comandos APL](#comandos-apl)
  * [Cómo utilizar este paquete](#c%C3%B3mo-utilizarlo)
    * [Importando el paquete](#utilizando-import)
    * [Alojando su propio paquete APL](#alojando-su-propio-paquete-apl)
    * [Asignando el comando a un componente](#asignando-el-comando-a-un-componente)
* [Ejemplo Simple](#ejemplo-simple)
    * [Animación simple de un objeto en onMount](#animando-una-imagen-en-un-evento-onmount)
* [Ejemplo Avanzado](#ejemplo-avanzado)
* [Ejemplos de las animaciones](https://github.com/Mindgeist/APL-animate/blob/master/SAMPLES.md)


## ¿Qué es Alexa APL?
Con el Alexa Presentation Language se pueden crear experiencias visuales que darán soporte a una skill. Los usuarios pueden interactuar en los dispositivos que lo soportan, como el Echo Show, Fire TV y otros dispositivos con experiencia multimodal que soporten Alexa. 

## Documentación
La información que se presenta aquí es compatible con la de la especificación [1.3 de APL](https://developer.amazon.com/es-ES/docs/alexa/alexa-presentation-language/apl-document.html)

Este documento no tiene como objetivo explicar los detalles del funcionamiento del APL de Alexa más allá de lo necesario para su uso e integración en este proyecto, puede obtener más información en la [guía de diseño de Alexa](https://developer.amazon.com/es-ES/docs/alexa/alexa-design/get-started.html) y en los [foros](https://forums.developer.amazon.com/topics/apl.html).

### Documento APL

Un *documento APL* es un objeto JSON con una estructura bien definida. Define una plantilla que se presentará en un dispositivo con pantalla. Este documento controla la estructura y diseño.

#### Ejemplo básico de un documento APL
Este ejemplo muestra un documento APL que presenta un solo componente `Text`
```json
{
  "type": "APL",
  "version": "1.3",
  "mainTemplate": {
    "item": {
      "type": "Text",
      "text": "¡Hola mundo!"
    }
  }
}
```
Un APL más completo puede incluir definiciones de recursos, definiciones de estilo, gráficos de vectores, etc.
Puedes encontrar más información en [propiedades de un documento APL.](https://developer.amazon.com/es-ES/docs/alexa/alexa-presentation-language/apl-document.html#document-properties)


### Paquetes APL

Un [paquete de APL](https://developer.amazon.com/es-ES/docs/alexa/alexa-presentation-language/apl-package.html) es un contenedor para `layouts`, `resources`, `styles` que pueden importarse en su documento APL, están conformados como un docmento único JSON que sique la estructura de un documento APL convencional pero en el que se prescinde de `mainTemplate`, aunque su inclusión no invalida el paquete.

Estos paquetes sirven para facilitar el acceso a elementos comunes ente diferentes documentos APL o entre diferentes skills.
El [Sistema de Diseño Alexa para APL](https://developer.amazon.com/es-ES/docs/alexa/alexa-presentation-language/apl-alexa-packages-overview.html) está compuesto por un conjunto de paquetes de estilo y plantillas prediseñadas para incoporar a sus skills.

[APL Animate](https://github.com/Mindgeist/APL-animate/tree/master/src) es un paquete APL de comandos que incluye más de 50 animaciones que pueden aplicarse a los elementos de un documento APL.

#### Importar un paquete APL en su documento

La sección `import` de un documento APL es un array de paquetes a importar en formato JSON.

Este ejemplo importa el paquete de [Estilos del Sistema de Diseño de APL de Alexa](https://developer.amazon.com/es-ES/docs/alexa/alexa-presentation-language/apl-alexa-styles-package.html)

```json
{
  "import": [
    {
      "name": "alexa-styles",
      "version": "1.1.0"
    }
  ]
}
```


### Comandos APL

Los comandos son mensajes que modifican la presentación del contenido visual o de audio en un dispositivo Alexa con pantalla.
Pueden dispararse por diferentes tipos de eventos. Los [comandos de APL](https://developer.amazon.com/es-ES/docs/alexa/alexa-presentation-language/apl-commands.html) se utilizan para navegar contenido, cambiar escenas en el documento, sincronizar la voz con presentaciones visuales y pueden utilizarse de manera creativa para crear experiencias de usuario multimodales (con soporte visual además de sonoro).

Todos los comandos son un objeto JSON que tiene las siguientes propiedades:

Propiedad | Tipo | Valor por defecto | Descripción
------------ | ------------- | ------------ | -------------
type | String | OBLIGATORIO | Tipo de comamdo
description| String | "" | Documenta el comando (opcional)
delay| Integer | 0 | El intervalo en milisegundos antes de que se ejecute el comando. Debe ser un número positivo.
when| Boolean | true | Expresión condicional. Si se evalúa `false`el comando es ignorado.


#### AnimateItem

Ejecuta una animación de una duración determinada en una o más propiedades de un componente.

`AnimateItem` suma las siguientes propiedades a las propiedades de un comando normal.

Propiedad | Tipo | Valor por defecto | Descripción
------------ | ------------- | ------------ | -------------
componentId | String | SELF |El id del componente al que se aplicarán las transformaciones.
duration | Integer | REQUIRED | Duración en milisegundos
easing | linear, ease-in, … |	linear | Especifica la curva de reproducción.
repeatCount | Integer | 0	| Número de veces que se repite.
repeatMode | restart, reverse | restart | Cómo se repiten.
value | Array of animated properties | REQUIRED | Un `array` de propiedades animadas de un componente.

##### Ejemplo de AnimateItem

Esta secuencia de comandos aplica al componente con el `id = "foo"` una variación de 500ms de duración en la opacidad entre transparente y totalmente opaco que empieza después de un intervalo de 500ms desde que se ejecuta el comando y lo repite 5 veces con una duración total de 3000ms. En cada iteracón el valor se invierte, de totalmente transparente a totalmente opaco y viceversa.    

```json
{
    "type": "AnimateItem",
    "componentId": "foo",
    "duration": "500",
    "delay": "500",
    "repeatCount": 5,
    "repeatMode": "reverse",
    "value": [
        {
            "property": "opacity",
            "from": 0,
            "to": 1
        }
    ]
}
```


### Cómo utilizar este paquete

#### Utilizando Import

Copie el documento [apl-animate.json](https://github.com/Mindgeist/APL-animate/blob/master/src/apl-animate.json) a un almacenamiento con acceso publico e importe el paquete en su APL de esta manera:

```json
{
    "type": "APL",
    "version": "1.1",
    "import": [
        {
            "name": "apl-animate",
            "source": "https://yourdomain.com/<path>/apl-animate.json"
        }
    ]
    ....
}
````

#### Alojando su propio paquete APL

Puede utilizar un bucket S3 al que debe configurar para acceso público con el encabezado apropiado `Access-Control-Allow-Origin` de **uso compartido de origen cruzado** [CORS](https://developer.amazon.com/es-ES/docs/alexa/alexa-presentation-language/apl-support-for-your-skill.html#support-cors)

También puede alojarlo en un repositorio público de GitHub ya que este soporta CORS en todos los dominios.

#### Asignando el comando a un componente

Las animaciones pueden agregarse a un componente de un documento APL al que se haya importado el paquete [apl-animate.json](https://github.com/Mindgeist/APL-animate/blob/master/src/apl-animate.json) a partir de un evento.

```json
{
    "type": "Text",
    "text": "APL 1.1 Transitions",
    "onMount: [
        {
            "type": "fadeIn",
            "duration": 1000
        }
    ],
    ....
}
```
## Ejemplos

### Ejemplo simple

#### Animando una imagen en un evento onMount

Puede probar este ejemplo pegando el código en la sección APL en la herramienta de diseño de la [consola de desarrollador](https://developer.amazon.com/alexa/console/ask?).

* Abra la consola.
* En la barra de navegación a la izquierda haga click en **Display**.
 * La herramienta de diseño se abrirá en una página nueva.
* Haga click en **Create Template**.
* Elija **Start from scratch*. Así creará un documento en blanco.
* En la barra de navegación a la izquierda haga click en **APL** para abrir el editor de JSON.
* Borre todo el contenido y copie el código de este ejemplo.

En este ejemplo en el momento en que se despliega el documento se ejecuta una serie de comandos secuenciales, el primero pone el nombre del efecto en el elemento de texto con identificador **"actual"** a continuación con un intervalo inicial de espera de 1000ms aplica el efecto de animación de la librería *slideInLeft* que se desplazará desde una distancia **"distance"** de 100 unidades de ancho (*vw*, aunque puede utilizarse cualquier las unidades que soporta APL como *px* para píxeles) al elemento de imagen con identificador **"mglogo"**

```json
{
    "type": "APL",
    "version": "1.3",
    "import": [
        {
            "name": "Transitions",
            "source": "https://raw.githubusercontent.com/Mindgeist/APL-animate/master/src/apl-animate.json?token=AAS3JMW4BILNEAP4XEX2GQC6XZ3UI",
            "version": "1.1"
        }
    ],
    "mainTemplate": {
        "items": [
            {
                "type": "Container",
                "direction": "row"
                "items": [
                    {
                        "type": "Container",
                        "width": "100vw",
                        "height": "100vh",
                        "paddingTop": "16dp",
                        "paddingLeft": "16dp",
                        "paddingRight": "16dp",
                        "paddingBottom": "16dp",
                        "alignItems": "center",
                        "justifyContent": "center"
                        "item": [
                            {
                                "type": "Image",
                                "id": "mglogo",
                                "width": "70vw",
                                "height": "26vw",
                                "source": "https://github.com/Mindgeist/APL-animate/raw/master/img/mglogo.png",
                                "align": "center",
                                "onMount": [
                                    {
                                        "type": "Sequential",
                                        "commands": [
                                            {
                                                "type": "SetValue",
                                                "property": "text",
                                                "value": "Slide In Left",
                                                "componentId": "actual"
                                            },
                                            {
                                                "type": "slideInLeft",
                                                "delay": "1000",
                                                "duration": 1000,
                                                "distance":"100vw",
                                                "componentId": "mglogo"
                                            }
                                        ]
                                    }
                                ]
                            },
                            {
                                "type": "Text",
                                "height": "10vh",
                                "text": "Texto",
                                "id": "actual"
                            }
                        ]
                    }
                ]
            }
        ]
    },
    "layouts": {}
}

```



#### Ejemplo Avanzado

Como alternativa puede utilizar el archivo [apl_template_samples.json](https://github.com/Mindgeist/APL-animate/blob/master/apl_template_samples.json )

* Abra la [consola de desarrollador](https://developer.amazon.com/alexa/console/ask?).
* En la barra de navegación a la izquierda haga click en **Display**.
 * La herramienta de diseño se abrirá en una página nueva.
* Haga click en **Create Template**.
* Elija **Upload Code** y suba el documento [apl_template_samples.json](https://github.com/Mindgeist/APL-animate/blob/master/apl_template_samples.json )
* Elija el dispositivo que tenga registrado debajo de la simulación de la herramienta.

En el dispositivo verá una lista de efectos que podrá disparar tocando en el nombre.
Este ejemplo utiliza otro evento, **onPress**, para disparar las animaciones.

 ```json

{
    "type": "APL",
    "version": "1.3",
    "import": [
        {
            "name": "Animate",
            "source": "https://ask-apls.s3.eu-west-3.amazonaws.com/apl-animate.json",
            "version": "1.1"
        }
    ],
    "styles": {
        "stylePressable": {
            "values": [
                {
                    "fontWeight": "400",
                    "when": "${state.focused}"
                },
                {
                    "fontWeight": "800",
                    "when": "${state.pressed}"
                },
                {
                    "fontWeight": "100",
                    "when": "${state.disabled}"
                }
            ]
        }
    },
    "mainTemplate": {
        "items": [
            {
                "type": "Container",
                "items": [
                    {
                        "type": "Container",
                        "width": "50vw",
                        "height": "100vh",
                        "paddingTop": "16dp",
                        "paddingLeft": "16dp",
                        "paddingRight": "16dp",
                        "paddingBottom": "16dp",
                        "item": [
                            {
                                "type": "Image",
                                "id": "mglogo",
                                "width": "30vw",
                                "height": "6vw",
                                "source": "https://ask-pics-mindgeist.s3-eu-west-1.amazonaws.com/mglogo.png",
                                "align": "center",
                                "scale": "scale-down"
                            }
                        ],
                        "alignItems": "center",
                        "justifyContent": "center"
                    },
                    {
                        "type": "ScrollView",
                        "height": "90vh",
                        "alignSelf": "center",
                        "items": [
                            {
                                "type": "Container",
                                "width": "45vw",
                                "data": [
                                    "bounce",
                                    "bounceIn",
                                    "bounceInDown",
                                    "bounceInLeft",
                                    "bounceInRight",
                                    "bounceInUp",
                                    "bounceOut",
                                    "bounceOutDown",
                                    "bounceOutLeft",
                                    "bounceOutRight",
                                    "bounceOutUp",
                                    "fadeIn",
                                    "fadeInDown",
                                    "fadeInLeft",
                                    "fadeInRight",
                                    "fadeInUp",
                                    "fadeOut",
                                    "fadeOutDown",
                                    "fadeOutLeft",
                                    "fadeOutRight",
                                    "fadeOutUp",
                                    "flash",
                                    "jackInTheBox",
                                    "jello",
                                    "lightSpeedIn",
                                    "lightSpeedOut",
                                    "pulse",
                                    "rollIn",
                                    "rollOut",
                                    "rotateIn",
                                    "rubberBand",
                                    "shake",
                                    "slideInDown",
                                    "slideInLeft",
                                    "slideInRight",
                                    "slideInUp",
                                    "slideOutDown",
                                    "slideOutLeft",
                                    "slideOutRight",
                                    "slideOutUp",
                                    "tada",
                                    "wobble",
                                    "zoomIn",
                                    "zoomInDown",
                                    "zoomInLeft",
                                    "zoomInRight",
                                    "zoomInUp",
                                    "zoomOut",
                                    "zoomOutDown",
                                    "zoomOutLeft",
                                    "zoomOutRight",
                                    "zoomOutUp"
                                ],
                                "item": {
                                    "type": "TouchWrapper",
                                    "items": {
                                        "type": "Text",
                                        "height": "10vh",
                                        "text": "${data}",
                                        "fontWeight": "800"
                                    },
                                    "style": "stylePressable",
                                    "onPress": [
                                        {
                                            "type": "${data}",
                                            "duration": 1000,
                                            "distance": "50vw",
                                            "componentId": "mglogo"
                                        }
                                    ]
                                }
                            }
                        ]
                    }
                ],
                "direction": "row"
            }
        ]
    },
    "layouts": {}
}

 ```


