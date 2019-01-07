




<!-- ********************************************************************* -->
## API de PhoneGap

En esta sección vamos a ver algunas de las funciones que nos ofrece la API de PhoneGap para el acceso a las características nativas y hardware del dispositivo, como por ejemplo acceso a las notificaciones, información del dispositivo, conexión, acelerómetro, brújula, geolocalización o cámara. Además, al final de esta sección se incluyen indicaciones para obtener más información sobre las opciones que no se tratan.

En el siguiente código de ejemplo se muestran los primeros pasos que tenemos que realizar con PhoneGap. En primer lugar cargamos la API de PhoneGap desde su librería JavaScript. A continuación tenemos que esperar a que se cargue correctamente la librería y los objetos generados que nos dan acceso a las funcionalidades del dispositivo:

```html
<html>
  <head>
    <title>Ejemplo de uso de PhoneGap</title>

    <script type="text/javascript" charset="utf-8" src="cordova-2.2.0.js"></script>
    <script type="text/javascript" charset="utf-8">

    // Esperamos a que cargue
    document.addEventListener("deviceready", onDeviceReady, false);

    // Cordova está listo
    function onDeviceReady() {
        navigator.FUNCION( PARAMETROS );
    }

    // Otras funciones...
    function funcionEjemplo() {
        navigator.FUNCION( PARAMETROS );
    }
    </script>
  </head>
  <body>
    <p><a href="#" onclick="funcionEjemplo(); return false;">Lanzar función</a></p>
  </body>
</html>
```


<!-- ********************************************************************* -->
### Notificaciones

Permite mostrar notificaciones visuales, o mediante sonido o vibración. Para esto disponemos de los siguientes métodos:

* **notification.alert**: muestra un mensaje visual de aviso

```html
navigator.notification.alert(message, alertCallback, [title], [buttonName])
```

donde:

* message: Cadena con el mensaje a mostrar en el cuadro de diálogo.
* alertCallback: Nombre de la función que se llamará cuando se cierre el aviso.
* title: Cadena con el título del cuadro de diálogo (Opcional, por defecto: "Alert")
* buttonName: Cadena con el texto del botón (Opcional, por defecto: "OK")


* **notification.confirm**: muestra un cuadro de diálogo de confirmación.

```html
navigator.notification.confirm(message, confirmCallback, [title], [buttonLabels])
```

donde:

* message: Cadena con el mensaje a mostrar en el cuadro de diálogo.
* confirmCallback: Nombre de la función que se llamará cuando se cierre el aviso. Esta función recibe un parámetro con el índice del botón presionado.
* title: Cadena con el título del cuadro de diálogo (Opcional, por defecto: "Confirm")
* buttonLabels: Lista de cadenas separadas por comas con los textos de los botones (Opcional, por defecto: "OK,Cancel")


* **notification.beep**: Reproduce un sonido tipo "beep" un número de veces especificado.

```html
navigator.notification.beep(times);
```

donde "times" indica el número de veces a repetir el sonido.


* **notification.vibrate**: Realiza una vibración en el dispositivo durante un tiempo especificado.

```html
navigator.notification.vibrate(milliseconds)
```

donde "time" indica el tiempo en milisegundos de la vibración.


A continuación se incluye un ejemplo completo de uso de todas estas funciones:

```html
<html>
  <head>
    <title>Ejemplo de Notificaciones</title>

    <script type="text/javascript" charset="utf-8" src="cordova-2.2.0.js"></script>
    <script type="text/javascript" charset="utf-8">

    // Esperamos a que cargue
    document.addEventListener("deviceready", onDeviceReady, false);

    // Cordova está listo
    function onDeviceReady() {
        //...
    }

    // Callback del diálogo de aviso
    function callbackAlertDismissed() {
        //...
    }

    // Callback del diálogo de confirmación
    function callbackOnConfirm(buttonIndex) {
        alert(&apos;Botón seleccionado &apos; + buttonIndex);
    }

    // Mostrar un diálogo de aviso
    function showAlert() {
        navigator.notification.alert(
            &apos;Eres el ganador!&apos;,     // mensaje
            callbackAlertDismissed, // callback
            &apos;Fin de Juego&apos;,         // título
            &apos;Continuar&apos;             // texto botón
        );
    }

    // Mostrar un diálogo de confirmación
    function showConfirm() {
        navigator.notification.confirm(
            &apos;¿Qué deseas realizar?&apos;, // mensaje
            callbackOnConfirm,       // callback
            &apos;Juego Pausado&apos;,         // título
            &apos;Reiniciar,Continuar&apos;    // texto de los botones
        );
    }

    // Reproduce un sonido beep 3 veces
    function playBeep() {
        navigator.notification.beep(3);
    }

    // Realiza una vibración durante 2 segundos
    function vibrate() {
        navigator.notification.vibrate(2000);
    }

    </script>
  </head>
  <body>
    <p><a href="#" onclick="showAlert(); return false;">Mostrar Alerta</a></p>
    <p><a href="#" onclick="showConfirm(); return false;">Mostrar Confirmación</a></p>
    <p><a href="#" onclick="playBeep(); return false;">Reproducir Beep</a></p>
    <p><a href="#" onclick="vibrate(); return false;">Vibración</a></p>
  </body>
</html>
```

Para más información consultar:
<a href="http://docs.phonegap.com/en/2.2.0/cordova_notification_notification.md.html">http://docs.phonegap.com/en/2.2.0/cordova_notification_notification.md.html</a>



<!-- ********************************************************************* -->
### Información del dispositivo

A través del objeto "device" podemos obtener información sobre el dispositivo, como el modelo del dispositivo, la versión de PhoneGap utilizada, el nombre del sistema operativo utilizado, el identificador único del dispositivo (UUID),
y la versión del sistema operativo. En el código siguiente se incluye un ejemplo de uso:

```html
<html>
  <head>
    <title>Información del dispositivo</title>
    <script type="text/javascript" charset="utf-8" src="cordova-2.2.0.js"></script>
    <script type="text/javascript" charset="utf-8">

    // Wait for Cordova to load
    document.addEventListener("deviceready", onDeviceReady, false);

    // Cordova is ready
    function onDeviceReady() {
        var element = document.getElementById(&apos;deviceProperties&apos;);
        element.innerHTML =
            &apos;Device Model: &apos; + device.name + &apos;<br/>&apos; +
            &apos;Device Cordova: &apos; + device.cordova + &apos;<br/>&apos; +
            &apos;Device Platform: &apos; + device.platform + &apos;<br/>&apos; +
            &apos;Device UUID: &apos; + device.uuid + &apos;<br/>&apos; +
            &apos;Device Version: &apos; + device.version + &apos;<br/>&apos;;
    }
    </script>
  </head>
  <body>
    <p id="deviceProperties">Loading device properties...</p>
  </body>
</html>
```



<!-- ********************************************************************* -->
### Información de Conexión

El objeto "`connection.type`" devuelve una constante indicando el tipo de conexión que está activa en el dispositivo.
En el siguiente código se muestra un ejemplo de uso, en el que se obtiene el tipo de conexión y se muestra en un cuadro de diálogo.
El array "states" del ejemplo contiene todos los tipos de conexión soportados por "connection.type".

```html
function checkConnection() {
    var networkState = navigator.connection.type;

    var states = {};
    states[Connection.UNKNOWN]  = &apos;Unknown connection&apos;;
    states[Connection.ETHERNET] = &apos;Ethernet connection&apos;;
    states[Connection.WIFI]     = &apos;WiFi connection&apos;;
    states[Connection.CELL_2G]  = &apos;Cell 2G connection&apos;;
    states[Connection.CELL_3G]  = &apos;Cell 3G connection&apos;;
    states[Connection.CELL_4G]  = &apos;Cell 4G connection&apos;;
    states[Connection.NONE]     = &apos;No network connection&apos;;

    alert(&apos;Connection type: &apos; + states[networkState]);
}

checkConnection();
```


<!-- ********************************************************************* -->
### Aceleración

Para obtener los valores del sensor de aceleración del dispositivo en los ejes x, y, z, podemos utilizar la función "accelerometer.getCurrentAcceleration".
Esta función recibe dos parámetros: el primero es la función callback que se llamará en caso de éxito con los valores obtenidos y el segundo
el nombre de la función que se llamará en caso de error. A continuación se incluye un ejemplo de uso:


```html
function onSuccess(acceleration) {
    alert(&apos;Acceleration X: &apos; + acceleration.x + &apos;\n&apos; +
          &apos;Acceleration Y: &apos; + acceleration.y + &apos;\n&apos; +
          &apos;Acceleration Z: &apos; + acceleration.z + &apos;\n&apos; +
          &apos;Timestamp: &apos;      + acceleration.timestamp + &apos;\n&apos;);
};

function onError() {
    alert(&apos;onError!&apos;);
};

navigator.accelerometer.getCurrentAcceleration(onSuccess, onError);
```

Para más información puedes consultar la dirección:
<a href="http://docs.phonegap.com/en/2.2.0/cordova_accelerometer_accelerometer.md.html">http://docs.phonegap.com/en/2.2.0/cordova_accelerometer_accelerometer.md.html</a>.



<!-- ********************************************************************* -->
### Brújula

Para obtener la información del sensor de brújula del dispositivo utilizamos la función "compass.getCurrentHeading", la cual devuelve un ángulo (entre 0 y 359.99) con la dirección en la que está apuntando el dispositivo. Esta función recibe dos parámetros: la función callback a la que se le pasarán los datos obtenidos y una función error que será llamada en caso de que no se pueda acceder al sensor. A continuación se incluye un ejemplo de uso:


```html
function onSuccess(heading) {
    alert(&apos;Heading: &apos; + heading.magneticHeading);
};

function onError(error) {
    alert(&apos;CompassError: &apos; + error.code);
};

navigator.compass.getCurrentHeading(onSuccess, onError);
```

Para más información puedes consultar la dirección:
<a href="http://docs.phonegap.com/en/2.2.0/cordova_compass_compass.md.html">http://docs.phonegap.com/en/2.2.0/cordova_compass_compass.md.html</a>.




<!-- ********************************************************************* -->
### Geolocalización

Para acceder a la información del sensor GPS del dispositivo utilizamos
la función "geolocation.getCurrentPosition". Esta función recibe dos parámetros: el método que se llamará si se obtienen correctamente la información del GPS, y el método que se llamará en caso de error. A continuación se incluye un ejemplo de uso:

```javascript
function onSuccess(position) {
    alert(&apos;Latitude: &apos; + position.coords.latitude + &apos;\n&apos; +
          &apos;Longitude: &apos; + position.coords.longitude + &apos;\n&apos; +
          &apos;Altitude: &apos; + position.coords.altitude + &apos;\n&apos; +
          &apos;Accuracy: &apos; + position.coords.accuracy + &apos;\n&apos; +
          &apos;Altitude Accuracy: &apos; + position.coords.altitudeAccuracy + &apos;\n&apos; +
          &apos;Heading: &apos; + position.coords.heading + &apos;\n&apos; +
          &apos;Speed: &apos; + position.coords.speed + &apos;\n&apos; +
          &apos;Timestamp: &apos; + position.timestamp + &apos;\n&apos;);
};

function onError(error) {
    alert(&apos;code: &apos;    + error.code    + &apos;\n&apos; +
          &apos;message: &apos; + error.message + &apos;\n&apos;);
}

navigator.geolocation.getCurrentPosition(onSuccess, onError);
```

Para más información consultar:
<a href="http://docs.phonegap.com/en/2.2.0/cordova_geolocation_geolocation.md.html">http://docs.phonegap.com/en/2.2.0/cordova_geolocation_geolocation.md.html</a>



<!-- ********************************************************************* -->
### Cámara

Podemos utilizar el módulo de cámara para tomar imágenes mediante la llamada a la función "camera.getPicture". Esta función recibe tres parámetros: la función callback que se llamará si se obtiene la imagen correctamente, el segundo parámetro será la función callback de error, y el tercero las opciones de configuración. En el último parámetro podemos indicar la calidad con la que se obtendrá la imagen (en el rango [0,100]), el origen del cual se obtendrá la imagen (cámara del dispositivo [opción por defecto], librería de la cámara, o álbumes de fotos), y el destino o formato en el que se devolverá la imagen (como una cadena codificada en base64 o con la dirección de un fichero).
A continuación se incluye un ejemplo de uso:


```html
navigator.camera.getPicture(onSuccess, onFail,
	{ quality: 50, destinationType: Camera.DestinationType.FILE_URI });

function onSuccess(imageURI) {
    var image = document.getElementById(&apos;myImage&apos;);
    image.src = imageURI;
}

function onFail(message) {
    alert(&apos;Failed because: &apos; + message);
}
```

En este ejemplo la imagen obtenida se muestra en una etiqueta `<img>` que se ha definido en el html como vacía y con identificador `myImage`.

Para más información consultar:
<a href="http://docs.phonegap.com/en/2.2.0/cordova_camera_camera.md.html">http://docs.phonegap.com/en/2.2.0/cordova_camera_camera.md.html</a>



<!-- ********************************************************************* -->
### API completa

Quedan muchas opciones que no hemos tratado en esta introducción a PhoneGap, como:

* Gestión de contactos: crear, obtener, modificar, guardar y borrar contactos.
* Control de eventos:
  * pause, resume: cuando la aplicación pierde o recibe el foco.
  * online, offline: cuando se obtiene o pierde la conexión a Internet.
* Control de botones: atrás, menú, búsqueda, llamada, fin de llamada.
* Control de batería: estado, batería baja, batería crítica.
* Botones de volumen.
* Control del sistema de ficheros: lectura, escritura, navegación.
* Almacenamiento: acceso a las opciones de almacenamiento y control de base de datos del dispositivo.
* Captura de medios: audio, vídeo, imágenes.
* Control multimedia: reproducir y grabar audio y vídeo.
* Mostrar/ocultar una imagen como "_splashscreen_".


En la dirección
<a href="http://docs.phonegap.com/en/2.2.0/index.html">http://docs.phonegap.com/en/2.2.0/index.html</a>
se puede consultar la API completa de PhoneGap.


