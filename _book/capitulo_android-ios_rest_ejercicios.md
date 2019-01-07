
# Servicios de acceso a servicios REST


> Ayuda: Entre las plantillas de este módulo se incluye la aplicación `RESTfulSwingClient`. Se trata de una aplicación Java de escritorio que nos permite probar servicios web RESTful de forma sencilla. Simplemente tendremos que importarla en Eclipse y ejecutar su clase `restfulswingclient.RESTfulSwingClientApp`. Se abrirá una ventana en la que podremos introducir la URL del servicio a probar y realizar una petición, proporcionando el contenido y cabeceras que especifiquemos. En la misma ventana podremos visualizar la respuesta recibida. Esta aplicación puede resultar de utilidad para entender mejor el funcionamiento de los servicios que utilizaremos en esta sesión.

## Consulta de libros (1.5 puntos)

En primer lugar vamos a implementar una aplicación que consulte el listado de libros disponible en una biblioteca online. Partiremos de la plantilla `ListadoLibros`, donde encontramos una tabla o lista que deberemos rellenar con los datos obtenidos del servicio REST de la biblioteca. Se pide:

_a)_ Realizar desde la aplicación una llamada al servicio, solicitando que nos devuelva la representación XML del recurso lista de libros. La URL a la que debemos acceder para obtener dicho listado es:

<a href="http://server.jtech.ua.es/jbib-rest/resources/libros">http://server.jtech.ua.es/jbib-rest/resources/libros</a>

Recoge el resultado como una cadena, y provisionalmente muéstralo como _log_. Comprueba que hemos obtenido los datos de forma correcta en formato XML (es importante enviar en la petición la cabecera `Accept` para asegurarnos de que se devuelva este formato).

> Ayuda Android: En la actividad `ListadoLibrosActivity`, y dentro de ella en la tarea asíncrona `CargarLibrosTask`, configura la petición para obtener representación `application/xml`.

> Ayuda iOS: En el controlador `UAListadoLibrosViewController`, en `viewWillAppear` configura la petición para que pida representación `application/xml`.


_b)_ Implementa el _parsing_ del XML anterior, y obtén una lista de libros (objetos `Libro` o `UALibro`). Tras esto se deberá actualizar la interfaz para mostrar en ella la lista de libros obtenida.

> Ayuda Android: En `parseLibros` utiliza `XmlPullParser` para obtener la lista de libros a partir del XML.

> Ayuda iOS: Implementa el método del delegado del parser `parser: didStartElement: namespaceURI: qualifiedName: attributes:` para que lea los datos de los libros del XML y los añada a la lista de libros del controlador encapsulados en objetos `UALibro`. En `actualizarLibrosConDatos:` deberemos ejecutar el parser utilizando `self` como delegado.


## Búsqueda de mensajes en Twitter (1.5 puntos)

Vamos a continuar trabajando con el cliente de Twitter que comenzamos en la sesión anterior. Hasta ahora esta aplicación  está obteniendo la lista de _tweets_ de memoria. Vamos ahora a modificar este comportamiento para que obtenga los _tweets_ de los servicios REST publicados por Twitter. Concretamente, accederemos al servicio de de búsqueda público y utilizaremos la representación JSON:

% JODIDO!: http://stackoverflow.com/questions/12684765/twitter-api-returns-error-215-bad-authentication-data

<a href="http://search.twitter.com/search.json?q=universidad%20alicante">http://search.twitter.com/search.json?q=universidad%20alicante</a>

> Ayuda Android: En la actividad `ClienteTwitterActivity`, y dentro de ella en la tarea `CargarTweetsTask`, cambia la inicialización de la lista de _tweets_ predefinida por una llamada al servicio de Twitter anterior, y obtén la representación JSON de la respuesta.

> Ayuda iOS: En el controlador `UAListadoTweetsViewController`, en `viewDidLoad` elimina la llamada a `inicializaTweets`, que es quien carga una lista predefinida de _tweets_ en memoria, y en su lugar realiza una conexión asíncrona al servicio mostrando el indicador de actividad de red cuando sea oportuno. En el método `actualizarTweetsConDatos`, lee el JSON obtenido en forma de `NSDictionary`. Dentro de dicho diccionario hay una propiedad `results`, que contiene un _array_ con los _tweets_ obtenidos. Cada elemento de dicho _array_ es un diccionario que contiene los datos del _tweet_, del que deberás obtener las propiedades `text`, `from_user` y `profile_image_url` y encapsúlalas en objetos `UATweet`.


