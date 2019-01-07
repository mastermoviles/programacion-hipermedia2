# Sesion 2- Rest iOS

## Acceso a servicios REST desde iOS

En iOS podemos acceder a servicios REST utilizando las clases para conectar con URLs vistas en anteriores sesiones. Por ejemplo, para hacer una consulta al _public timeline_ de Twitter podríamos utilizar un código como el siguiente para iniciar la conexión (recordemos que este método de conexión es asíncrono, y los datos los recibirá posteriormente el objeto delegado especificado):

```objectivec
NSURL *url = [NSURL URLWithString:
    @"http://twitter.com/statuses/public_timeline.json"];
NSURLRequest *theRequest = [NSURLRequest requestWithURL: url];
NSURLConnection *theConnection =
   [NSURLConnection connectionWithRequest: theRequest delegate: self];
```

En el caso de Objective-C, si queremos realizar peticiones con métodos distintos a GET, deberemos utilizar la clase `NSMutableURLRequest`, en lugar de `NSURLRequest`, ya que esta última no nos permite modificar los datos de la petición, incluyendo el método HTTP. Podremos de esta forma establecer todos los datos necesarios para la petición al servicio: método HTTP, mensaje a enviar (por ejemplo XML o JSON), y cabeceras (para indicar el tipo de contenido enviado, o los tipos de representaciones que aceptamos). Por ejemplo:

```objectivec

NSURL *url =
    [NSURL URLWithString:@"http://jtech.ua.es/resources/peliculas"];
NSData *datosPelicula = ... // Componer mensaje XML con datos de la
                            // película a crear

NSMutableURLRequest *theRequest =
    [NSMutableURLRequest requestWithURL: url];
[theRequest setHTTPMethod: @"POST"];
[theRequest setHTTPBody: datosPelicula];
[theRequest setValue:@"application/xml" forHTTPHeaderField:@"Accept"];
[theRequest setValue:@"application/xml"
  forHTTPHeaderField:@"Content-Type"];

NSURLConnection *theConnection =
    [NSURLConnection connectionWithRequest: theRequest
                                  delegate: self];
```

Podemos ver que en la petición POST hemos establecido todos los datos necesarios. Por un lado su bloque de contenido, con los datos del recurso que queremos añadir en la representación que consideremos adecuada. En este caso suponemos que utilizamos XML como representación. En tal caso hay que avisar de que el contenido lo enviamos con este formato, mediante la cabecera `Content-Type`, y de que la respuesta también queremos obtenerla en XML, mediante la cabecera `Accept`.


## Parsing de XML en iOS

Para análisis de XML en iOS contamos en el SDK con `NSXMLParser`. Con esta librería el análisis se realiza de forma parecida a los parsers SAX de Java. Este es el parser principal incluido en el SDK y está escrito en Objective-C, aunque también contamos dentro del SDK con libxml2, escrito en C, que incluye tanto un parser SAX como DOM. También encontramos otras librerías que podemos incluir en nuestro proyecto como parsers DOM XML:

<table>
		<tr><th>Parser</th><th>URL</th><th></th><th></th></tr>
		<tr><td>TBXML</td><td colspan="3"><a href="http://www.tbxml.co.uk/">http://www.tbxml.co.uk/</a></td></tr>
		<tr><td>TouchXML</td><td colspan="3"><a href="https://github.com/TouchCode/TouchXML">https://github.com/TouchCode/TouchXML</a></td></tr>
		<tr><td>KissXML</td><td colspan="3"><a href="http://code.google.com/p/kissxml">http://code.google.com/p/kissxml</a></td></tr>
		<tr><td>TinyXML</td><td colspan="3"><a href="http://www.grinninglizard.com/tinyxml/">http://www.grinninglizard.com/tinyxml/</a></td></tr>
		<tr><td>GDataXML</td><td colspan="3"><a href="http://code.google.com/p/gdata-objectivec-client">http://code.google.com/p/gdata-objectivec-client</a></td></tr>
</table>

Nos vamos a centrar en el estudio de `NSXMLParser`, por ser el parser principal incluido en la API de Cocoa Touch.

Para implementar un parser con esta librería deberemos crear una clase que adopte el protocolo `NSXMLParserDelegate`, el cual define, entre otros, los siguientes métodos:

```objectivec
- (void) parser:(NSXMLParser *)parser
didStartElement:(NSString *)elementName
   namespaceURI:(NSString *)namespaceURI
  qualifiedName:(NSString *)qualifiedName
     attributes:(NSDictionary *)attributeDict;

- (void) parser:(NSXMLParser *)parser
  didEndElement:(NSString *)elementName
   namespaceURI:(NSString *)namespaceURI
  qualifiedName:(NSString *)qName;

- (void) parser:(NSXMLParser *)parser
foundCharacters:(NSString *)string;
```

Podemos observar que nos informa de tres tipos de eventos: `didStartElement`, `foundCharacters`, y `didEndElement`. El análisis del XML será secuencial, el parser irá leyendo el documento y nos irá notificando los elementos que encuentre. Cuando se abra una etiqueta, llamará al método `didStartElement` de nuestro parser, cuando encuentre texto llamará a `foundCharacters`, y cuando se cierra la etiqueta llamará a `didEndElement`. Será responsabilidad nuestra implementar de forma correcta estos tres eventos, y guardar la información de estado que necesitemos durante el análisis.

Por ejemplo, imaginemos un documento XML sencillo como el siguiente:

```xml
<![CDATA[<mensajes>
    <mensaje usuario="pepe">Hola, ¿qué tal?</mensaje>
    <mensaje usuario="ana">Fetén</mensaje>
</mensajes>]]>
```

Podemos analizarlo mediante un parser `NSXMLParser` como el siguiente:

```objectivec

- (void)     parser:(NSXMLParser *)parser
    didStartElement:(NSString *)elementName
       namespaceURI:(NSString *)namespaceURI
      qualifiedName:(NSString *)qualifiedName
         attributes:(NSDictionary *)attributeDict {

  if([[elementName lowercaseString] isEqualToString:@"mensajes"]) {
    self.listaMensajes = [NSMutableArray arrayWithCapacity: 100];
  } else if([[elementName lowercaseString]
                                     isEqualToString:@"mensaje"]) {
    self.currentMensaje = [UAMensaje mensaje];
    self.currentMensaje.usuario =
        [attributeDict objectForKey:@"usuario"];
  } else {
    // No puede haber etiquetas distintas a mensajes o mensaje
    [parser abortParsing];
  }
}

- (void)   parser:(NSXMLParser *)parser
    didEndElement:(NSString *)elementName
     namespaceURI:(NSString *)namespaceURI
    qualifiedName:(NSString *)qName {

  if([[elementName lowercaseString] isEqualToString:@"mensaje"]) {
    [self.listaMensajes addObject: self.currentMensaje];
    self.currentMensaje = nil;
  }
}

- (void)     parser:(NSXMLParser *)parser
    foundCharacters:(NSString *)string {

	NSString* value = [string stringByTrimmingCharactersInSet:
	             [NSCharacterSet whitespaceAndNewlineCharacterSet]];

	if([value length]!=0 && self.currentMensaje!=nil) {
        self.currentMensaje.texto = value;
	}
}
```

Podemos observar que cada vez que encuentra una etiqueta de apertura, podemos obtener tanto la etiqueta como sus atributos. Cada vez que se abre un nuevo mensaje, se crea un objeto de tipo `UAMensaje` en una variable de instancia, y se van introduciendo en él los datos que se encuentran en el XML, hasta encontrar la etiqueta de cierre (en nuestro caso el texto, aunque podríamos tener etiquetas anidadas).

Para que se ejecute el _parser_ que hemos implementado mediante el delegado, deberemos crear un objeto `NSXMLParser` y proporcionarle dicho delegado (en el siguiente ejemplo suponemos que nuestro objeto `self` hace de delegado). El parser se debe inicializar proporcionando el contenido XML a analizar (encapsulado en un objeto `NSData`):


```objectivec
NSXMLParser *parser = [[NSXMLParser alloc] initWithData: self.content];
parser.delegate = self;
BOOL result = [parser parse];
```

Tras inicializar el _parser_, lo ejecutamos llamando el método `parse`, que realizará el análisis de forma síncrona, y nos devolverá `YES` si todo ha ido bien, o `NO` si ha habido algún error al procesar la información (se producirá un error si en el delegado durante el _parsing_ llamamos a `[parser abortParsing]`).

### Parsing de JSON en iOS

El parsing de JSON no se incorporó al SDK de iOS hasta la versión 5.0. Anteriormente contábamos con diferentes librerías que podíamos incluir para realizar esta tarea, como *JSONKit* (<a href="https://github.com/johnezang/JSONKit">`https://github.com/johnezang/JSONKit`</a>) o *json-framework* (<a href="https://github.com/stig/json-framework/">`https://github.com/stig/json-framework/`</a>). Sin embargo, ahora podemos trabajar con JSON directamente con las clases de Cocoa Touch, sin necesidad de incluir ninguna librería adicional. Vamos a centrarnos en esta forma de trabajar.

Simplemente necesitaremos la clase `NSJSONSerialization`. A partir de ella obtendremos el contenido del JSON en una jerarquía de objetos `NSArray` y `NSDictionary`

```objectivec
NSError *error = nil;
NSData *data = ... // Contenido JSON obtenido de la red
NSArray *mensajes = [NSJSONSerialization JSONObjectWithData: data
                                                    options:0
                                                      error:&error];

if(error==nil) {
    for(NSDictionary *mensaje in mensajes) {
        NSString *texto = [mensaje valueForKey:@"texto"];
        NSString *usuario = [mensaje valueForKey:@"usuario"];

        ...
    }
}
```

El método `JSONObjectWithData:options:error:` de la clase `NSJSONSerialization` nos devolverá un `NSDictionary` o `NSArray` según si el elemento principal del JSON es un objeto o una lista, respectivamente.

Al igual que en el caso de Android, el objeto `NSJSONSerialization` también nos permite realizar la transformación en el sentido inverso, permitiendo transformar una jerarquía de objetos `NSArray` y `NSDictionary` en una representación JSON. Para eso contaremos con el método `dataWithJSONObject`:

```objectivec
NSDictionary *dict = [NSDictionary dictionaryWithObjectsAndKeys:
                          @"Hola!", @"texto",
                          @"Pepe",  @"usuario", nil];

NSData *json = [NSJSONSerialization dataWithJSONObject: dict
                                               options:0
                                                 error:&error];
```

