# APL-animate
## Alexa APL Animation Library
Ésta es una librería con una colección de animaciones que pueden aplicarse a objetos de Alexa APL. Implementa con las transformaciones disponibles con AnimateItem algunas de las animaciones implementadas en [Animate CSS](https://animate.style/), la adaptación original es de https://github.com/arjun-g/apl-transitions 

## Tabla de contenidos

* [Alexa APLS](#qu%C3%A9-es-alexa-apl)
* [Documentación](#documentaci%C3%B3n) 
  * [Documento APL](#documento-apl)
  * [Paquetes APL](#paquetes-apl)
  * [Comandos APL](#comandos-apl)
  * [Cómo utilizar este paquete](#c%C3%B3mo-utilizarlo)
   * [Alojando su propio paquete APL]()

* [Ejemplos](#Ejemplos)


## ¿Qué es Alexa APL?
Con el Alexa Presentation Language se pueden crear experiencias visuales que darán soporte a una skill. Los usuarios pueden interactuar en los dispositivos que lo soportan, como el Echo Show, Fire TV y otros dispositivos con experiencia multimodal que soporten Alexa. 

## Documentación
La información que se presenta aquí corresponde a la de la especificación [1.3 de APL](https://developer.amazon.com/es-ES/docs/alexa/alexa-presentation-language/apl-document.html)

Este documento no tiene el objetivo explicar los detalles del funcionamiento del APL de Alexa más allá de lo necesario para su uso e integración en otros proyectos, puede obtenerse más información en la [guías de diseño de Alexa](https://developer.amazon.com/es-ES/docs/alexa/alexa-design/get-started.html) y en los [foros](https://forums.developer.amazon.com/topics/apl.html).

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

Este ejemplo importa el paquete de [Estilos de del Sistema de Diseño de APL de Alexa](https://developer.amazon.com/es-ES/docs/alexa/alexa-presentation-language/apl-alexa-styles-package.html)

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
`type` | String | OBLIGATORIO | Tipo de comamdo
`description`| String | "" | Documenta el comando (opcional)
`delay`| Integer | 0 | El intervalo en milisegundos antes de que se ejecute el comando. Debe ser un número positivo.
`when`| Boolean | true | Expresión condicional. Si se evalúa  ` false`el comando es ignorado.


#### AnimateItem

Ejecuta una animación de una duración determinada en una o más propiedades de un componente.

`AnimateItem` suma las siguientes propiedades a las propiedades de un comando normal.

Propiedad | Tipo | Valor por defecto | Descripción
------------ | ------------- | ------------ | -------------
`componentId` | String | SELF |El id del componente al que se aplicarán las transformaciones.
`duration` | Integer | REQUIRED | Duración en milisegundos
`easing` | linear, ease-in, … |	linear | Especifica la curva de reproducción.
`repeatCount` | Integer | 0	| Número de veces que se repite.
`repeatMode` | restart, reverse | restart | Cómo se repiten.
`value` | Array of animated properties | REQUIRED | Un `array` de propiedades animadas de un componente.

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
            "version": "https://yourdomain.com/<path>/apl-animate.json"
        }
    ]
    ....
}
````

#### Alojando su propio paquete APL

Puede utilizar un bucket S3 al que debe configurar para acceso público con el encabezado apropiado `Access-Control-Allow-Origin` de **uso compartido de origen cruzado** [CORS](https://developer.amazon.com/es-ES/docs/alexa/alexa-presentation-language/apl-support-for-your-skill.html#support-cors)

Puede alojarlo en un repositorio público de GitHub ya que este soporta CORS en todos los dominios.

#### Animando una imagen en un evento onMount

Puede probar este ejemplo pegando el código en la sección APL en la herramienta de diseño de la [consola de desarrollador](https://developer.amazon.com/alexa/console/ask?).

* Abra la consola.
* En la barra de navegación a la izquierda haga click en **Display**.
 * La herramienta de diseño se abrirá en una página nueva.
* Haga click en **Create Template**.
* Elija **Start from scratch*. así creará un documento en blanco.
* En la barra de navegación a la izquierda haga click en **APL** para abrir el editor de JSON.
* Borre todo el contenido y copie el código de este ejemplo.

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
                                                "componentId": "current"
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
                                "text": "Text",
                                "id": "current"
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




