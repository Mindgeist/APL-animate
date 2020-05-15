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
    *[Alojando su propio paquete APL]()

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

*Abra la consola.
*En la barra de navegación a la izquierda haga click en **Display**.
 *La herramienta de diseño se abrirá en una página nueva.
*Haga click en **Create Template**.
*Elija **Start from scratch*. así creará un documento en blanco.
*En la barra de navegación a la izquierda haga click en **APL** para abrir el editor de JSON.
*Borre todo el contenido y copie el código de este ejemplo.

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




## Ejemplos
### Llamadas de atención
#### Bounce
![Image  of  bounce](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/Bounce.gif?raw=true)

```json
{
    "type": "bounce",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Flash
![Image  of  flash](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/Flash.gif?raw=true)

```json
{
    "type": "flash",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Pulse
![Image  of  pulse](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/Pulse.gif?raw=true)

```json
{
    "type": "pulse",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Rubberband
![Image  of  rubberBand](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/RubberBand.gif?raw=true)

```json
{
    "type": "rubberBand",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Shake
![Image  of  shake](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/Shake.gif?raw=true)

```json
{
    "type": "shake",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Tada
![Image  of  tada](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/Tada.gif?raw=true)

```json
{
    "type": "tada",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Wobble
![Image  of  wobble](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/Wobble.gif?raw=true)

```json
{
    "type": "wobble",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Jack In Thebox
![Image  of  jackInTheBox](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/JackInTheBox.gif?raw=true)

```json
{
    "type": "jackInTheBox",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


### Entradas Fundido

#### Fade In 
![Image  of  fadeIn](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/FadeIn.gif?raw=true)

```json
{
    "type": "fadeIn",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Fade In Down
![Image  of  fadeInDown](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/fadeInDown.gif?raw=true)

```json
{
    "type": "fadeInDown",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Fade In Left
![Image  of  fadeInLeft](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/FadeInLeft.gif?raw=true)

```json
{
    "type": "fadeInLeft",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Fade In Right
![Image  of  fadeInRight](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/fadeInRight.gif?raw=true)

```json
{
    "type": "fadeInRight",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Fade In Up
![Image  of  fadeInUp](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/FadeInUp.gif?raw=true)

```json
{
    "type": "fadeInUp",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


### Salidas Fundido

#### Fade Out 
![Image  of  fadeOut](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/FadeOut.gif?raw=true)

```json
{
    "type": "fadeOut",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Fade Out Down
![Image  of  fadeOutDown](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/FadeOutDown.gif?raw=true)

```json
{
    "type": "fadeOutDown",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Fade Out Left
![Image  of  fadeOutLeft](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/FadeOutLeft.gif?raw=true)

```json
{
    "type": "fadeOutLeft",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Fade Out Right
![Image  of  fadeOutRight](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/FadeOutRight.gif?raw=true)

```json
{
    "type": "fadeOutRight",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Fade Out Up
![Image  of  fadeOutUp](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/FadeOutUp.gif?raw=true)

```json
{
    "type": "fadeOutUp",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


### Entradas Rebotando

#### Bounce In 
![Image  of  bounceIn](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/BounceIn.gif?raw=true)

```json
{
    "type": "bounceIn",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Bounce In Down
![Image  of  bounceInDown](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/BounceInDown.gif?raw=true)

```json
{
    "type": "bounceInDown",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Bounce In Left
![Image  of  bounceInLeft](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/BounceInLeft.gif?raw=true)

```json
{
    "type": "bounceInLeft",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Bounce In Right
![Image  of  bounceInRight](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/BounceInRight.gif?raw=true)

```json
{
    "type": "bounceInRight",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Bounce In Up
![Image  of  bounceInUp](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/BounceInUp.gif?raw=true)

```json
{
    "type": "bounceInUp",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


### Salidas Rebotando

#### Bounce Out 
![Image  of  bounceOut](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/BounceOut.gif?raw=true)

```json
{
    "type": "bounceOut",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Bounce Out Down
![Image  of  bounceOutDown](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/BounceOutDown.gif?raw=true)

```json
{
    "type": "bounceOutDown",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Bounce Out Left
![Image  of  bounceOutLeft](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/BounceOutLeft.gif?raw=true)

```json
{
    "type": "bounceOutLeft",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Bounce Out Right
![Image  of  bounceOutRight](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/BounceOutRight.gif?raw=true)

```json
{
    "type": "bounceOutRight",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Bounce Out Up
![Image  of  bounceOutUp](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/BounceOutUp.gif?raw=true)

```json
{
    "type": "bounceOutUp",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```



### Lightspeed 

#### Lightspeed In 
![Image  of  lightSpeedIn](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/LightspeedIn.gif?raw=true)

```json
{
    "type": "lightSpeedIn",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


#### Lightspeed Out 
![Image  of  lightSpeedOut](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/LightspeedOut.gif?raw=true)

```json
{
    "type": "lightSpeedOut",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


### Roll

#### Roll In 
![Image  of  rollIn](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/RollIn.gif?raw=true)

```json
{
    "type": "rollIn",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Roll Out 
![Image  of  rollOut](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/RollOut.gif?raw=true)

```json
{
    "type": "rollOut",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


### Rotate

#### Rotate In 
![Image  of  rotateIn](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/RotateIn.gif?raw=true)

```json
{
    "type": "rotateIn",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Rotate Out 
![Image  of  rotateOut](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/RotateOut.gif?raw=true)

```json
{
    "type": "rotateOut",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


### Entrada Deslizando

#### Slide In Down
![Image  of  slideInDown](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/SlideInDown.gif?raw=true)

```json
{
    "type": "slideInDown",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Slide In Left
![Image  of  slideInLeft](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/SlideInLeft.gif?raw=true)

```json
{
    "type": "slideInLeft",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Slide In Right
![Image  of  slideInRight](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/SlideInRight.gif?raw=true)

```json
{
    "type": "slideInRight",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Slide In Up
![Image  of  slideInUp](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/SlideInUp.gif?raw=true)

```json
{
    "type": "slideInUp",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


### Salida Deslizando

#### Slide Out Down
![Image  of  slideOutDown](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/SlideOutDown.gif?raw=true)

```json
{
    "type": "slideOutDown",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Slide Out Left
![Image  of  slideOutLeft](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/SlideOutLeft.gif?raw=true)

```json
{
    "type": "slideOutLeft",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Slide Out Right
![Image  of  slideOutRight](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/SlideOutRight.gif?raw=true)

```json
{
    "type": "slideOutRight",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Slide Out Up
![Image  of  slideOutUp](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/SlideOutUp.gif?raw=true)

```json
{
    "type": "slideOutUp",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


#### Entrada Zoom

#### Zoom In 
![Image  of  zoomIn](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/ZoomIn.gif?raw=true)

```json
{
    "type": "zoomIn",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Zoom In Down
![Image  of  zoomInDown](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/ZoomInDown.gif?raw=true)

```json
{
    "type": "zoomInDown",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Zoom In Left
![Image  of  zoomInLeft](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/ZoomInLeft.gif?raw=true)

```json
{
    "type": "zoomInLeft",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Zoom In Right
![Image  of  zoomInRight](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/ZoomInRight.gif?raw=true)

```json
{
    "type": "zoomInRight",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Zoom In Up
![Image  of  zoomInUp](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/ZoomInUp.gif?raw=true)

```json
{
    "type": "zoomInUp",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```


### Salida Zoom

#### Zoom Out 
![Image  of  zoomOut](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/ZoomOut.gif?raw=true)

```json
{
    "type": "zoomOut",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Zoom Out Down
![Image  of  zoomOutDown](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/ZoomOutDown.gif?raw=true)

```json
{
    "type": "zoomOutDown",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Zoom Out Left
![Image  of  zoomOutLeft](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/ZoomOutLeft.gif?raw=true)

```json
{
    "type": "zoomOutLeft",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Zoom Out Right
![Image  of  zoomOutRight](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/ZoomOutRight.gif?raw=true)

```json
{
    "type": "zoomOutRight",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```

#### Zoom Out Up
![Image  of  zoomOutUp](https://github.com/Mindgeist/APL-animate/blob/master/thumbnails/zoomOutUp.gif?raw=true)

```json
{
    "type": "zoomOutUp",
    "delay": 1000,
    "duration": 1000,
    "distance": "100"
}    
```
