# 🍃 Port Swigger

https://portswigger.net/web-security/learning-paths/api-testing

## Documentación de la API

### API recon

_El primer paso es enumerar el punto de raiz de la API para poder empezar a enumerarla._

```
GET /api/books HTTP/1.1
Host: example.com
```

**¿Que debemos de enumerar?**

1. Que inputs procesa y si acepta algún parametro.
2. Métodos HTTP que acepta.
3. Límites de intentos e posibilidades de abuso de estos.

### Documentación de la API

_Esta documentación nos puede guiar en las rutas que hay y en la información de estas. Nos podemos encontrar dos tipos, legible para **humanos** o para **máquinas**._

**Legible para Humanos**

* Esta tendrá documentación acompañada de ejemplos y casos prácticos del uso de esta misma.

**Legible para máquinas**

* Esta esta diseñada con una cierta complejidad superior la cual esta diseñada para usarse directamente con máquinas.

### Descubrir en documentación de la API

_Con la herramienta Burp Scanner podemos encontrar endpoints de API's por ejemplo..._

* `/api`
* `/swagger/index.html`
* `/openapi.json`

_Si hemos identificado el endpoint de un recurso podemos seguir investigando la API, por ejemplo el recurso es: `/api/swagger/v1/users/123`_

* `/api/swagger/v1`
* `/api/swagger`
* `/api`

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (118).png" alt=""><figcaption></figcaption></figure>

&#x20;_Tenemos la función de cambiar el email..._

<figure><img src="../.gitbook/assets/imagen (119).png" alt=""><figcaption></figcaption></figure>

_Probamos a cambiar el método HTTP a DELETE y apuntamos a Carlos y vemos que se ha eliminado el usuario..._&#x20;

**VIDEO:**



{% embed url="https://youtu.be/AxzpOVS23o8" %}

### Documentación legible por la máquina

_Para esta enumeración podemos utilizar infinidad de herramientas, entre ellas Burp Scanner. También puedes utilizar PostMan_

## Identificar Puntos de partida de la API

_Esto es el paso principal, tendremos que fijarnos en las solicitudes que se tramitan y como se hacen, podemos también encontrar referéncias en archivos JS. Una herramienta ideal para enumerar esto es CrawlPaths_&#x20;

<figure><img src="../.gitbook/assets/imagen (120).png" alt=""><figcaption></figcaption></figure>

### Interactuar con Puntos de Partida

_Para interactuar con esto lo podemos tramitar desde el Repeater, debemos de analizar las solicitudes y probar a cambiar parametros, rutas, métodos y reconstruir solicitudes validas_

### Identificar métodos HTTP soportados

_Los métodos HTTP determinan la acción sobre un recurso_

* `GET` --> Recoge información sobre un recurso.
* `PATCH` --> Aplica cambios sobre un recurso.

_Los puntos de partida de las API's soportan diferentes métodos HTTP. Por ejemplo el punto de partida: `/api/tasks` soporta estos métodos:_

* `GET /api/tasks` --> Recoge la lista de tareas.
* `POST /api/tasks` --> Crea una nueva tarea.
* `DELETE /api/tasks/1` --> Elimina una tarea.

_Pudes automatizar el proceso de enumeración de estas a trevés de BurpSuite_&#x20;

<figure><img src="../.gitbook/assets/imagen (121).png" alt=""><figcaption></figcaption></figure>

### Identificar diferentes Content Types

_Los puntos de partida de las API's esperan información en un formato en concreto y cambia según el Content-Type y cambiando este puedes obtener:_

* Fugas de información útiles según un error.
* Bypass de las defensas de la Web.
* Aprovecharte de fallos lógicos por ejemplo una API puede ser segura trabajando en JSON pero si le cambiar la estructura a XML puede ser susceptible a inyecciones.

_Para cambiar este valor unicamente tienes que cambiar el Content-Type y utilizando BurpSuite BApp te cambia automáticamente la estrucura de los datos tanto XML como JSON._

#### EJEMPLO PRÁCTICO

_Identificamos este valor de la API_

<figure><img src="../.gitbook/assets/imagen (122).png" alt=""><figcaption></figcaption></figure>

_Por GET podemos ver esta información._

<figure><img src="../.gitbook/assets/imagen (123).png" alt=""><figcaption></figcaption></figure>

_Por POST podemos ver esta._

<figure><img src="../.gitbook/assets/imagen (124).png" alt=""><figcaption></figcaption></figure>

_Por PATCH podemos ver que podemos modificar el valor del precio_

<figure><img src="../.gitbook/assets/imagen (125).png" alt=""><figcaption></figcaption></figure>

_Agregamos el Content-Type y creamos un estructura JSON y podemos alterar el valor del precio._

<figure><img src="../.gitbook/assets/imagen (126).png" alt=""><figcaption></figcaption></figure>

_Vemos que el valor del precio ha cambiado._

<figure><img src="../.gitbook/assets/imagen (127).png" alt=""><figcaption></figcaption></figure>

### Utilizar Brute Force para encontrar otros puntos de partida

_Pudes hacer un brute force para enumerar otros puntos de partida._

## Parametros ocultos

_Dentro del reconocimiento podemos toparnos con parametros ocultos lo cuales podemos enumerar y nos pueden llevar a descrubir alguna vulnerabilidad._

* Burp Intruder
* Param miner BApp
* Fuzz

## Vulnerabilidades de mass assigment

_Algunos frameworks pueden intentar ahorrar trabajo a los desarrolladores asignando automáticamente estos datos a los atributos correspondientes de un objeto en el servidor. Esto puede incluir datos que el desarrollador no esperaba ni quería procesar._

### Identificación de parametros ocultos

_Hemos dicho que podiamos añadir campos a requests por ejemplo `PATCH /api/users/` nos permite cambiar el correo y el nombre de la cuenta._

```json
{
    "username": "wiener",
    "email": "wiener@example.com",
}
```

_Esta misma solicitud por `GET` nos devuelve lo siguiente._

```json
{
    "id": 123,
    "name": "John Doe",
    "email": "john@example.com",
    "isAdmin": "false"
}
```

_Nos infica algun valor extra que no habiamos visto anteriormente._

### Abusar del mass assigment

_Anteriormente nos devolvia lo siguente por `GET`._

```json
{
    "id": 123,
    "name": "John Doe",
    "email": "john@example.com",
    "isAdmin": "false"
}
```

_Ahora por la solicitud por `PATCH` que habiamos visto anteriormente vamos a cambiar el valor de Administrador._

```json
{
    "username": "wiener",
    "email": "wiener@example.com",
    "isAdmin": true,
}
```

_Ahora deberiamos de verificar si tenemos permisos de administración._

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (129).png" alt=""><figcaption></figcaption></figure>

_Vemos que en el Checkout se tramita una petición por POST_

<figure><img src="../.gitbook/assets/imagen (130).png" alt=""><figcaption></figcaption></figure>

_Al tramitarla por get podemos ver un valor que habla sobre un descuento._

<figure><img src="../.gitbook/assets/imagen (132).png" alt=""><figcaption></figcaption></figure>

_Modificamos el valor del descuento desde la petición por POST_

## Server-side parameter pollution

_Algunos sistemas contienen API internas a las que no se puede acceder directamente desde Internet.Server-side parameter pollution se produce cuando un sitio web incorpora la entrada del usuario en una solicitud del lado del servidor a una API interna sin la codificación adecuada._ _Pudes probar cualquier campo para ver si es vulnerabile por ejemplo parametros de busquera, formularios o rutas URL._

## Atacando Server-side parameter pollution

_Para atacar esto tenemos que hacernos con las syntaxis básica que es: `#`, `&`, `=` y podremos ver las respuestas por parte de la aplicación._ _Vamos a suponer una aplicación vulnerable que te permite buscar a usuarios y luego te retorna a una página._

```
GET /userSearch?name=peter&back=/home
```

_El servidor para recoger esta información hace esta petición interna._

```
GET /users/search?name=peter&publicProfile=true
```

#### Cambiando valores de la consulta

_Puedes utilizar el carácter `#` codificado para intentar alterar el comportamiento de la consulta._ _Por ejemplo podrías modificar la consulta a la siguiente._

```
GET /userSearch?name=peter%23foo&back=/home
```

_El front-end interpretaría esta URL_

```
GET /users/search?name=peter#foo&publicProfile=true
```

_Revise la respuesta para obtener pistas sobre si la consulta ha sido truncada. Por ejemplo, si la respuesta devuelve el petro del usuario, la consulta del lado del servidor puede haber sido truncada. Si se devuelve un mensaje de error de nombre inválido, la aplicación puede haber tratado a Foo como parte del nombre de usuario. Esto sugiere que la solicitud del lado del servidor puede no haber sido truncada._

### Inyectar parámetros validos

_Si te permite modificar la consulta puedes intentar agregar un segundo parametro valido._ _Si por ejemplo has identificado el campo del correo electrónico lo puedes agregar así:_

```
GET /userSearch?name=peter%26email=foo&back=/home
```

_Esto resultaría en la API interna._

```
GET /users/search?name=peter&email=foo&publicProfile=true
```

### Confirmar la vulnerabilidad

_Para confirmar que la aplicación es vulnerable a esto podemos intentar inyectar el segundo parametro con el mismo nombre._ _Por ejemplo podemos modificar esta..._

```
GET /userSearch?name=peter%26name=carlos&back=/home
```

_API interna_

```
GET /users/search?name=peter&name=carlos&publicProfile=true
```

_Esto debería de interpretarlo la aplicación de diferentes maneras._

* **PHP** -> Este cogería el segundo parámetro.
* **ASP.NET** -> Este lo intentariía combinar y devolvería un mensage de error.
* **Node.js / express** -> Este nos daría peter.

> Idea _Si puede anular el parámetro original, es posible que pueda realizar una explotación. Por ejemplo, puede agregar name=administrator a la solicitud. Esto puede permitirle iniciar sesión como usuario administrador._

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (111).png" alt=""><figcaption></figcaption></figure>

_Vemos que tenemos una función con la cual podemos restablecer una contraseña y vemos que podemos agregar un parametro._&#x20;

<figure><img src="../.gitbook/assets/imagen (112).png" alt=""><figcaption></figcaption></figure>

_Vemos que tenemos esta solicitud con el parametro username_

<figure><img src="../.gitbook/assets/imagen (113).png" alt=""><figcaption></figcaption></figure>

_Ahora vemos que nos dice que está esperando un `field`_&#x20;

<figure><img src="../.gitbook/assets/imagen (114).png" alt=""><figcaption></figcaption></figure>

_Ahora vemos que nos dice que es invalido. Si no nos funciona podemos variar con `#` y `&`_&#x20;

<figure><img src="../.gitbook/assets/imagen (115).png" alt=""><figcaption></figcaption></figure>

_Podemos ver que `email` y `username` son validos._

<figure><img src="../.gitbook/assets/imagen (116).png" alt=""><figcaption></figcaption></figure>

_También probamos con el `reset_token` y podemos ver que nos devuelve un Token..._

<figure><img src="../.gitbook/assets/imagen (117).png" alt=""><figcaption></figcaption></figure>

_Ahora en la web nos aprovechamos de esta función._

## Server-side parameter pollution en REST

_Por ejemplo tenemos una aplicación que nos permite editar perfiles en base a su nombre de usuario y de nuestro lado podemos tramitar esta solicitud:_

```markup
GET /edit_profile.php?name=peter
```

_Internamente la API rest procesaría lo siguiente:_

```
GET /api/private/users/peter
```

_Nosotros como atacante podriamos atacar la ruta de la url para explotar al API, para explotar esto vamos ha hacer un Path Traversal pero a la sequéncia._

_Usted podría enviar el `peter/../admin` como el valor del parámetro `name`:_

```
GET /edit_profile.php?name=peter%2f..%2fadmin
```

_Internamente se procesaria esta solicitud:_

```
GET /api/private/users/peter/../admin
```

_La API finalmente nos resolvería a la siguiente ruta_

```
/api/private/users/admin
```

## Server-side parameter pollution en estructura de datos

_Un atacante puede ser capaz de manipular parámetros para explotar vulnerabilidades en el procesamiento del servidor de otros formatos de datos estructurados, como un JSON o XML. Para probar para ello, inyecte datos estructurados inesperados en las entradas del usuario y vea cómo responde el servidor._

_Considere una aplicación que permita a los usuarios editar su perfil, luego aplica sus cambios con una solicitud a una API del lado del servidor. Al editar su nombre, su navegador hace la siguiente petición:_

```json
POST /myaccount
name=peter
```

_Del lado del servidor_

```json
PATCH /users/7312/update
{"name":"peter"}
```

_Ahora nosotros podemos intenar añadir `access_level`_

```json
POST /myaccount
name=peter","access_level":"administrator
```

_Si esto resulta funcionar y podemos añadirlo el servidor lo interpertaría así_

```json
PATCH /users/7312/update
{name="peter","access_level":"administrator"}
```

_Con esto peter consigue acceso a Administrador_

### Estructura JSON

_Ahora vamos a suponer que el navegador se comunica con estructuras JSON, nuestro navegador hace esta solicitud._

```json
POST /myaccount
{"name": "peter"}
```

_El servidor lo interpretaría de esta manera._

```json
PATCH /users/7312/update
{"name":"peter"}
```

_Ahora vamos a intenar agragar el `access_level`_

```json
POST /myaccount
{"name": "peter\",\"access_level\":\"administrator"}
```

_Ahora el servidor debería de interpretarlo de la siguiente manera:_

```json
PATCH /users/7312/update
{"name":"peter","access_level":"administrator"}
```

_Ahora peter recibe privilegios de Administrador._

## Server-side parameter pollution automatizado

_Burp incluye herramientas automatizadas que pueden ayudarle a detectar vulnerabilidades de contaminación de parámetros del lado del servidor._

_Burp Scanner detecta automáticamente transformaciones sospechosas de entrada al realizar una auditoría. Estos ocurren cuando una aplicación recibe la entrada del usuario, la transforma de alguna manera, luego realiza un procesamiento adicional sobre el resultado. Este comportamiento no necesariamente constituye una vulnerabilidad, por lo que tendrá que hacer más pruebas usando las técnicas manuales descritas anteriormente. Para más información, vea la definición de problema de transformación de entradas sospechosas._

_También puede utilizar el escáner de energía de reacción BApp para identificar vulnerabilidades de inyección del lado del servidor. El escáner clasifica los insumos como aburridos, interesantes o vulnerables. Usted tendrá que investigar entradas interesantes utilizando las técnicas manuales descritas anteriormente. Para obtener más información, consulte el Rescaneo de Funcional de Retroves: cazando clases de vulnerabilidad desconocidas papel blanco._
