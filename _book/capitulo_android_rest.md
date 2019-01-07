
# Clientes de servicios REST

## Invocación de servicios RESTful desde una clase Java

Vamos a ver como crear un cliente RESTful utilizando una sencilla clase Java, lo cual es aplicable a Android. Para ello vamos a utilizar el API de mensajes proporcionado por Twitter (<a href="http://www.twitter.com">http://www.twitter.com</a>). No va a ser necesario disponer de una cuenta de Twitter ni conocer con detalle qué es Twitter para seguir el ejemplo.


> Nota: Twitter es una plataforma de _micro-blogging_ que permite que múltiples usuarios actualicen su estado utilizando 140 caracteres como máximo cada vez. Además, los usuarios pueden "seguirse" unos a otros formando redes de "amigos". Twitter almacena estas actualizaciones en sus servidores, y por defecto, están disponibles públicamente. Es por ésto por lo que utilizaremos Twitter para crear nuestros ejemplos de clientes REST.

```java
try {
      URL twitter = new
        URL("http://twitter.com/statuses/public_timeline.xml");

      // Abrimos la conexión
      URLConnection tc = twitter.openConnection();

      // Obtenemos la respuesta del servidor
      BufferedReader in = new BufferedReader(new
                InputStreamReader( tc.getInputStream()));
      String line;

      // Leemos la respuesta del servidor y la imprimimos
      while ((line = in.readLine()) != null) {
           tvResult.append(line);
      }
      in.close();
    } catch (MalformedURLException e) {
           e.printStackTrace();
    } catch (IOException e) {
           e.printStackTrace();
}
```

Podemos ver que hemos utilizado el paquete estándar `java.net`. La URI del servicio web es: _http://twitter.com/statuses/public_timeline.xml_. Ésta será la URI de nuestro recurso y apuntará a las últimas 20 actualizaciones públicas.


Para conectar con el servicio web, primero tenemos que instanciar el objeto URL con la URI del servicio. A continuación, "abriremos" un objeto _URLConnection_ para la instancia de Twitter. La llamada al método _twitter.openConnection()_ ejecuta una petición HTTP GET.


Una vez que tenemos establecida la conexión, el servidor devuelve la respuesta HTTP. Dicha respuesta contiene una representación XML de las actualizaciones. Por simplicidad, volcaremos en la salida estandar la respuesta del servidor. Para ello, primero leemos el _stream_ de respuesta en un objeto _BufferedReader_, y a continuación realizamos un bucle para cada línea del _stream_, asignándola a un objeto _String_. Finalmente hemos incluido nuestro código en una sentencia _try/catch_, y enviamos cualquier mensaje de excepción a la salida estándar.


Éste es el estado público de la última actualización de Twitter de la estructura XML obtenida (sólo mostramos parte de uno de los _tweets_).

```xml
<?xml version="1.0" encoding="UTF-8"?> <statuses type="array">
  ...
  <status>
    <created_at>Tue Feb 22 11:43:25 +0000 2011</created_at>
    <id>40013788233216000</id>
    <text>Haar doen voor de cam. #ahahah</text>
    <source>web
    <truncated>false</truncated>
    <favorited>false</favorited>
    <in_reply_to_status_id></in_reply_to_status_id>
    <in_reply_to_user_id></in_reply_to_user_id>
    <in_reply_to_screen_name></in_reply_to_screen_name>
    <retweet_count>0</retweet_count>
    <retweeted>false</retweeted>
    <user>
      <id>250090010</id>
      <name>Dani&#235;l van der wal</name>
      <screen_name>DanielvdWall</screen_name>
      <location>Nederland,   Hoogezand</location>
      <description></description>
      <profile_image_url>
      http://a0.twimg.com/profile_images/1240171940/Picture0003_normal.JPG
      </profile_image_url>
      <url>http://daniel694.hyves.nl/</url>
      <protected>false</protected>
       <followers_count>50</followers_count>
       ...
      <friends_count>74</friends_count>
      ...
      <following>false</following>
      <statuses_count>288</statuses_count>
      <lang>en</lang>
      <contributors_enabled>false</contributors_enabled>
      <follow_request_sent>false</follow_request_sent>
      <listed_count>0</listed_count>
      <show_all_inline_media>false</show_all_inline_media>
      <is_translator>false</is_translator>
    </user>
    <geo/>
    <coordinates/>
    <place/>
    <contributors/>
  </status>
...
</statuses>
```


No entraremos en los detallos de la estructura XML, podemos encontrar la documentación del API en <a href="http://dev.twitter.com/">http://dev.twitter.com/</a>


La documentación del API nos dice que si cambiamos la extensión _.xml_ obtendremos diferentes representaciones del recurso. Por ejemplo, podemos cambiar _.xml_, por _.json_, _.rss_ o _.atom_. Así, por ejemplo, si quisiéramos recibir la respuesta en formato JSON (JavaScript Object Notation), el único cambio que tendríamos que hacer es en la siguiente línea:


```java
URL twitter =
      new URL("http://twitter.com/statuses/public_timeline.json");
```

En este caso, obtendríamos algo como esto:

```bash
[{"in_reply_to_status_id_str":null,"text":"THAT GAME SUCKED ASS.",
"contributors":null,"retweeted":false,"in_reply_to_user_id_str"
:null,"retweet_count":0,"in_reply_to_user_id":null,"source":"web",
"created_at":"Tue Feb 22 11:55:17 +0000 2011","place":null,
"truncated":false,"id_str":"40016776221696000","geo":null,
"favorited":false,"user":{"listed_count":0,"following":null,
"favourites_count":0,"url":"http:\/\/www.youtube.com\/user\
/haezelnut","profile_use_background_image":true,...
```


Los detalles sobre JSON se encuentran en la documentación del API.

> Nota: Aunque Twitter referencia este servicio como servicio RESTful, esta API en particular no es completamente RESTful, debido a una elección de diseño. Analizando la documentación del API vemos que el tipo de representación de la petición forma parte de la URI y no de la cabecera _accept_ de HTTP. El API devuelve una representación que solamente depende de la propia URI: _http://twitter.com/statuses/public_timeline.FORMATO_, en donde FORMATO puede ser _.xml_, _.json_, _.rss_, o _.atom_. Sin embargo, esta cuestión no cambia la utilidad del API para utilizarlo como ejemplo para nuestro cliente REST.


Otra posibilidad de implementación de nuestro cliente Java para Android es utilizar la librería _Http Client_. Dicha librería ofrece una mayor facilidad para controlar y utilizar objetos de conexión HTTP.


El código de nuestra clase cliente utilizando la librería quedaría así:

```java
HttpClient client = new DefaultHttpClient();
HttpGet request = new HttpGet(
                    "http://twitter.com/statuses/public_timeline.xml");
try {
    ResponseHandler<String> handler = new BasicResponseHandler();
    String contenido = client.execute(request, handler);

    tvResponse.setText(contenido);

} catch (ClientProtocolException e) {
} catch (IOException e) {
} finally {
    client.getConnectionManager().shutdown();
}
```

Observamos que primero instanciamos el cliente HTTP y procedemos a crear un objeto que representa el método HTTP GET. Con el cliente y el método instanciado, necesitamos ejecutar la petición con el método `execute`. Si queremos tener un mayor control sobre la respuesta, en lugar de utilizar un `BasicResponseHandler` podríamos ejecutar directamente la petición sobre el cliente, y obtener así la respuesta completa, tal como vimos en la sesión anterior.


En este caso, hemos visto cómo realizar una petición GET, tal como se vio en sesiones anteriores, para acceder a servicios REST. Sin embargo, para determinadas operaciones hemos visto que REST utiliza métodos HTTP distintos, como POST, PUT o DELETE. Para cambiar el método simplemente tendremos que cambiar el objeto `HttpGet` por el del método que corresponda. Cada tipo incorporará los métodos necesarios para el tipo de petición HTTP que represente. Por ejemplo, a continuación vemos un ejemplo de petición POST. Creamos un objeto `HttpPost` al que le deberemos pasar una entidad que represente el bloque de contenido a enviar (en una petición POST ya no sólo tenemos un bloque de contenido en la respuesta, sino que también lo tenemos en la petición). Podemos crear diferentes tipos de entidades, que serán clases que hereden de `HttpEntity`. La más habitual para los servicios que estamos utilizando será `StringEntity`, que nos facilitará incluir en la petición contenido XML o JSON como una cadena de texto. Además, deberemos especificar el tipo MIME de la entidad de la petición mediante `setContentType` (en el siguiente ejemplo consideramos que es XML). Por otro lado, también debemos especificar el tipo de representación que queremos obtener como respuesta, y como hemos visto anteriormente, esto debe hacerse mediante la cabecera `Accept`. Esta cabecera la deberemos establecer en el objeto que representa la petición POST (`HttpPost`).

```java
HttpClient client = new DefaultHttpClient();

HttpPost post = new HttpPost("http://jtech.ua.es/recursos/peliculas");
StringEntity s = new StringEntity("[contenido xml]");
s.setContentType("application/xml");
post.setEntity(s);
post.setHeader("Accept", "application/xml");

ResponseHandler<String> handler = new BasicResponseHandler();
String respuesta = client.execute(post, handler);
```

Como podemos ver en el ejemplo anterior, una vez configurada la petición POST la forma de ejecutar la petición es la misma que la vista anteriormente para peticiones GET. Para el resto de métodos HTTP el funcionamiento será similar, simplemente cambiando el tipo del objeto de la petición por el que corresponda (por ejemplo `HttpPut` o `HttpDelete`).



## Parsing de estructuras XML


En las comunicaciones por red es muy común transmitir información en formato XML, el ejemplo más conocido, depués del HTML, son las noticias RSS. En este último caso, al delimitar cada campo de la noticia por tags de XML se permite a los diferentes clientes lectores de RSS obtener sólo aquellos campos que les interese mostrar.


### Parsing de XML en Android


Android nos ofrece dos maneras de trocear o "parsear" XML. El `SAXParser` y el `XmlPullParser`. El parser SAX requiere la implementación de manejadores que reaccionan a eventos tales como encontrar la apertura o cierre de una etiqueta, o encontrar atributos. Menos implementación requiere el uso del parser Pull que consiste en iterar sobre el árbol de XML (sin tenerlo completo en memoria) conforme el código lo va requiriendo, indicándole al parser que tome la siguiente etiqueta (método `next()`) o texto (método nextText()).


A continuación mostramos un ejemplo sencillo de uso del `XmlPullParser`. Préstese atención a las sentencias y constantes resaltadas, para observar cómo se identifican los distintos tipos de etiqueta, y si son de apertura o cierre. También se puede ver cómo encontrar atributos y cómo obtener su valor.

```java
try {
  URL text = new URL("http://www.ua.es");

  XmlPullParserFactory parserCreator = XmlPullParserFactory.newInstance();
  XmlPullParser parser = parserCreator.newPullParser();
  parser.setInput(text.openStream(), null);
  int parserEvent = parser.getEventType();
  while (parserEvent != XmlPullParser.END_DOCUMENT) {

    switch (parserEvent) {
    case XmlPullParser.START_DOCUMENT:
      break;
    case XmlPullParser.END_DOCUMENT:
      break;
    case XmlPullParser.START_TAG:
      String tag = parser.getName();
      if (tag.equalsIgnoreCase("title")) {
        Log.i("XML","El titulo es: "+ parser.nextText());
      } else if(tag.equalsIgnoreCase("meta")) {
        String name = parser.getAttributeValue(null, "name");
        if(name.equalsIgnoreCase("description")) {
          Log.i("XML","La descripción es:" +
                      parser.getAttributeValue(null,"content"));
        }
      }
      break;
    case XmlPullParser.END_TAG:
      break;
    }

    parserEvent = parser.next();
  }
} catch (Exception e) {
  Log.e("Net", "Error en la conexion de red", e);
}
```


El ejemplo anterior serviría para imprimir en el LogCat el título del siguiente fragmento de página web, que en este caso sería "Universidad de Alicante", y para encontrar el `meta` cuyo atributo `name` sea "Description" y mostrar el valor de su atributo `content`:


```html
<html xmlns="http://www.w3.org/1999/xhtml" lang="es" xml:lang="es">
<head>
<title>Universidad de Alicante*</title>
<meta name="Description" content="Informacion Universidad Alicante.
  Estudios, masteres, diplomaturas, ingenierias, facultades, escuelas"/>
<![CDATA[<meta http-equiv="pragma" content="no-cache" />
<meta name="Author" content="Universidad de Alicante" />
<meta name="Copyright" content="&copy; Universidad de Alicante" />
<meta name="robots" content="index, follow" />
```




## Parsing de estructuras JSON

JSON es una representación muy utilizada para formatear los recursos solicitados a un servicio web RESTful. Se trata de ficheros con texto plano que pueden ser manipulados muy fácilmente utilizando JavaScript.


La gramática de los objetos JSON es simple y requiere la agrupación de la definición de los datos y valores de los mismos. En primer lugar, los elementos están contenidos dentro de llaves `{` y `}`; los valores de los diferentes elementos se organizan en pares, con la estructura: `"nombre":"valor"`, y están separados por comas; y finalmente, las secuencias de elementos están contenidas entre corchetes `[` y `]`. Y esto es todo :)! Para una descripción detallada de la gramática, podéis consultar <a href="http://www.json.org/fatfree.html"> http://www.json.org/fatfree.html</a>


Con la definición de agrupaciones anterior, podemos combinar múltiples conjuntos para crear cualquier tipo de estructura requerido. El siguiente ejemplo muestra una descipción JSON de un objeto con información sobre una lista de mensajes:


<source lang="plain">[ {"texto":"Hola, ¿qué tal?", "usuario":"Pepe" },
  {"texto":"Fetén", "usuario":"Ana" } ]</source>

Antes de visualizar cualquiera de los valores de una respuesta JSON, necesitamos convertirla en una estructura que nos resulte familiar, por ejemplo, sabemos cómo trabajar con las jerarquías de objetos Javascript.


Para convertir una cadena JSON en código "usable" utilizaremos la función Javascript nativa _eval()_. En el caso de un _stream_ JSON, _eval()_ transforma dicho _stream_ en un objeto junto con propiedades que son accesibles sin necesidad de manipular ninguna cadenas de caracteres.


>Nota: Los _streams_ JSON son fragmentos de código Javascript y deben evaluarse utilizando la función _eval()_ antes de poder utilizarse como objetos en tiempo de ejecución. En general, ejecutar Javascript a través de _eval()_ desde fuentes no confiables introducen riesgos de seguridad debido al valor de los parámetros de las funciones ejecutadas como Javascript. Sin embargo, para nuestra aplicación de ejemplo, confiamos en que el JavaScript enviado desde Twitter es una estructura JSON segura.

Debido a que un objeto JSON evaluado es similar a un objeto DOM, podremos atravesar el árbol del objeto utilizando el carácter "punto". Por ejemplo, un elemento raíz denominado `Root` con un sub-elemento denominado `Element` puede ser accedido mediante `Root.Element`


> Nota: Si estamos ante una cadena JSON sin ninguna documentación del API correspondiente, tendremos que buscar llaves de apertura y cierre (`{` y `}`). Esto nos llevará de forma inmediata a la definición del objeto. A continuación buscaremos pares de de nombre/valor entre llaves.

El análisis de JSON en Android e iOS es algo más complejo que en Javascript, pero en ambas plataformas encontramos tanto librerías integradas en el SDK, como librerías proporcionadas por terceros.




### Parsing de JSON en Android

Dentro de la API de Android encontramos una serie de clases que nos permiten analizar y componer mensajes JSON. Las dos clases fundamentales son `JSONArray` y `JSONObject`. La primera de ellas representa una lista de elementos, mientras que la segunda representa un objeto con una serie de propiedades. Podemos combinar estos dos tipos de objetos para crear cualquier estructura JSON. Cuando en el JSON encontremos una lista de elementos (`[ ... ]`) se representará mediante `JSONArray`, mientras que cuando encontremos un conjunto de propiedades _clave-valor_ encerrado entre llaves (`{ ... }`) se representará con `JSONObject`. Encontraremos estos dos tipos de elementos anidados según la estructura JSON que estemos leyendo.

```java
JSONArray mensajes = new JSONArray(contenido);

for(int i=0;i<mensajes.length();i++) {
    JSONObject mensaje = mensajes.getJSONObject(i);

    String texto = mensaje.getString("texto");
    String usuario = mensaje.getString("usuario");

    ...
}
```

El objeto `JSONArray` nos permite conocer el número de elementos que contiene (`length`), y obtenerlos a partir de su índice, con una serie de métodos `get-`. Los elementos pueden ser de tipos básicos (`boolean`, `double`, `int`, `long`, `String`), o bien ser objetos o listas de objetos (`JSONObject`, `JSONArray`). En el caso del ejemplo anterior cada elemento de la lista es un objeto _mensaje_.

Los objetos (`JSONObject`) tienen una serie de campos, a los que también se accede mediante una serie de métodos `get-` que pueden ser de los mismos tipos que en el caso de las listas. En el ejemplo anterior son cadenas (_texto_, y _usuario_), pero podrían ser listas u objetos anidados.

Esta librería no sólo nos permite analizar JSON, sino que también podemos componer mensajes con este formato. Los objetos `JSONObject` y `JSONArray` tienen para cada método `get-`, un método `put-` asociado que nos permite añadir campos o elementos. Una vez añadida la información necesaria, podemos obtener el texto JSON mediante el método `toString()` de los objetos anteriores.

Esta librería es sencilla y fácil de utilizar, pero puede generar demasiado código para parsear estructuras de complejidad media. Existen otras librerías que podemos utilizar como *GSON* (<a href="http://sites.google.com/site/gson/gson-user-guide">`http://sites.google.com/site/gson/gson-user-guide`</a>) o *Jackson* (<a href="http://wiki.fasterxml.com/JacksonInFiveMinutes">`http://wiki.fasterxml.com/JacksonInFiveMinutes`</a>) que nos facilitarán notablemente el trabajo, ya que nos permiten mapear el JSON directamente con nuestros objetos Java, con lo que podremos acceder al contenido JSON de forma similar a como se hace en Javascript.


