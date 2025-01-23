# 🏎️ Port Swigger

https://portswigger.net/web-security/learning-paths/race-conditions

## Race Conditions

_El tipo de condición de carrera más conocido le permite superar algún tipo de límite impuesto por la lógica de negocio de la aplicación._

_Por ejemplo, considere una tienda en línea que le permita introducir un código promocional durante el checkout para obtener un descuento de una sola vez en su pedido. Para aplicar este descuento, la solicitud podrá realizar los siguientes pasos de alto nivel:_

1. _Comítelo que no ha usado este código._
2. _Aplicar el descuento al total del pedido._
3. _Actualice el registro en la base de datos para reflejar el hecho de que ahora ha utilizado este código._

_Si más tarde intentas reutilizar este código, las comprobaciones iniciales realizadas al inicio del proceso deberían impedirte hacer esto:_

<figure><img src="../.gitbook/assets/imagen (58).png" alt=""><figcaption></figcaption></figure>

_Ahora considere lo que pasaría si un usuario que nunca haya aplicado este código de descuento antes de intentar aplicarlo dos veces casi exactamente al mismo tiempo:_

<figure><img src="../.gitbook/assets/imagen (59).png" alt=""><figcaption></figcaption></figure>

_Como puedes ver, la aplicación pasa a través de un sub-estado temporal; es decir, un estado en el que entra y luego sale de nuevo antes de que el procesamiento de solicitudes esté completo. En este caso, el sub-estado comienza cuando el servidor comienza a procesar la primera solicitud, y termina cuando actualiza la base de datos para indicar que ya ha utilizado este código. Esto introduce una pequeña ventana de carrera durante la cual puedes reclamar repetidamente el descuento tantas veces como quieras._

_Hay muchas variaciones de este tipo de ataque, incluyendo:_

* _Redentor de una tarjeta de regalo varias veces_
* _Calenando un producto varias veces_
* _Retirar o transferir efectivo en exceso de la balanza de su cuenta_
* _Reutilizar una única solución CAPTCHA_
* _Eludiendo un límite de tipos de fuerza antibruto_

_Los límites son un subtipo de las llamadas fallas de "tiempo de control al tiempo de uso" (TOCTOU). Más tarde en este tema, veremos algunos ejemplos de vulnerabilidades de condiciones de carrera que no caen en ninguna de estas categorías._

## Detectar y explotar condiciones de carreras de sobrecostos con Burp Repeater

_El proceso de detección y explotación de las condiciones de carreras de sobrecostos es relativamente sencillo. En términos de alto nivel, todo lo que necesitas hacer es:_

1. _Identificar una variable de un solo uso o límite de tarifa que tenga algún tipo de impacto de seguridad u otro propósito útil._
2. _Emite múltiples solicitudes a este endpoint en rápida sucesión para ver si puedes sobrepasar este límite._

_El principal reto es la sincronización de las peticiones para que al menos dos ventanas de carreras se alineen, causando una colisión. Esta ventana a menudo es de sólo milisegundos y puede ser aún más corta._

_Incluso si usted envía todas las solicitudes exactamente al mismo tiempo, en la práctica hay varios factores externos incontrolables e impredecibles que afectan cuando el servidor procesa cada solicitud y en qué orden._

<figure><img src="../.gitbook/assets/imagen (60).png" alt=""><figcaption></figcaption></figure>

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (61).png" alt=""><figcaption></figcaption></figure>

_Vemos que en la maquina vulnerable tenemos un cupon del 20%_

<figure><img src="../.gitbook/assets/imagen (62).png" alt=""><figcaption></figcaption></figure>

_Agregamos un producto y vemos que tenemos un input para poner el cupon_

<figure><img src="../.gitbook/assets/imagen (63).png" alt=""><figcaption></figcaption></figure>

_Agregamos el cupon y interceptamos la solicitud con el repeater y creamos un grupo con todas._ !

<figure><img src="../.gitbook/assets/imagen (65).png" alt=""><figcaption></figcaption></figure>

_Aplicamos esta opción_&#x20;

<figure><img src="../.gitbook/assets/imagen (66).png" alt=""><figcaption></figcaption></figure>

_Ahora enviamos todas las solicitudes_

<figure><img src="../.gitbook/assets/imagen (67).png" alt=""><figcaption></figcaption></figure>

_Vemos que hemos conseguido lo que queriamos_

## Detectar y explotar condiciones de carreras de sobrecosto con Turbo Intruder

_Además de proporcionar soporte nativo para el ataque de un solo paquete en Burp Repeater, también hemos mejorado la extensión de Turbo Intruder para apoyar esta técnica. Puede descargar la última versión de la tienda BApp._ **Para usar el ataque de un solo paquete en Turbo Intruder:**

1. _Asegúrese de que el objetivo soporta HTTP/2. El ataque de un solo paquete es incompatible con HTTP/1._
2. _Fisean el `engine=Engine.BURP2`y `concurrentConnections=1`opciones de configuración para el motor de solicitud._
3. _Al hacer cola de sus solicitudes, agruéllas asignándolas a una puerta llamada usando el `gate`argumento para la `engine.queue()`método._
4. _Para enviar todas las solicitudes en un grupo dado, abra la puerta respectiva con la `engine.openGate()`método._

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (68).png" alt=""><figcaption></figcaption></figure>

_Nos descargamos la extension_

<figure><img src="../.gitbook/assets/imagen (69).png" alt=""><figcaption></figcaption></figure>

_Agregamos %s y hacemos esto._

<figure><img src="../.gitbook/assets/imagen (70).png" alt=""><figcaption></figcaption></figure>

_Ponemos este script y lo modificamos con esto._

```python
def queueRequests(target, wordlists):

    # as the target supports HTTP/2, use engine=Engine.BURP2 and concurrentConnections=1 for a single-packet attack
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           engine=Engine.BURP2
                           )
    
    # assign the list of candidate passwords from your clipboard
    passwords = wordlists.clipboard
    
    # queue a login request using each password from the wordlist
    # the 'gate' argument withholds the final part of each request until engine.openGate() is invoked
    for password in passwords:
        engine.queue(target.req, password, gate='1')
    
    # once every request has been queued
    # invoke engine.openGate() to send all requests in the given gate simultaneously
    engine.openGate('1')


def handleResponse(req, interesting):
    table.add(req)
```

_Aplicamos este codigo y pegamos la wordlist en la clipboard_

<figure><img src="../.gitbook/assets/imagen (71).png" alt=""><figcaption></figcaption></figure>

_Vemos que lo hemos conseguido._

## Identificar Race Conditions

<figure><img src="../.gitbook/assets/imagen (73).png" alt=""><figcaption></figcaption></figure>

_Probando cada endpoint es poco práctico. Después de mapear el sitio de destino como es normal, puede reducir el número de puntos de end que necesita probar haciéndole las siguientes preguntas:_

<figure><img src="../.gitbook/assets/imagen (75).png" alt=""><figcaption></figcaption></figure>

_Con el primer ejemplo, es poco probable que la solicitud de reinicias de contraseña paralelas para dos usuarios diferentes cause una colisión, ya que resulta en cambios en dos registros diferentes. Sin embargo, la segunda implementación le permite editar el mismo registro con solicitudes para dos usuarios diferentes._

## Condiciones de carrera multi-punto

_Una variación de esta vulnerabilidad puede ocurrir cuando se realiza la validación del pago y la confirmación del pedido durante el procesamiento de una sola solicitud. La máquina estatal para el estado del pedido podría verse algo como esto:_

<figure><img src="../.gitbook/assets/imagen (76).png" alt=""><figcaption></figcaption></figure>

_En este caso, puedes añadir más elementos a tu cesta durante la ventana de la carrera entre cuando se valida el pago y cuando el pedido se confirma finalmente._

## Alineando ventanas de carrera multi-punto

_Cuando se hace una prueba para las condiciones de carrera multi-punto, puede encontrar problemas tratando de alinear las ventanas de la carrera para cada solicitud, incluso si los envía a todos exactamente al mismo tiempo utilizando la técnica de un solo paquete._

<figure><img src="../.gitbook/assets/imagen (77).png" alt=""><figcaption></figcaption></figure>

_Este problema común es causado principalmente por los dos factores siguientes:_

* _**Retraso introducido por la arquitectura** de **la red -** Por ejemplo, puede haber un retraso cuando el servidor frontal establece una nueva conexión con el back-end. El protocolo utilizado también puede tener un gran impacto._
* _**Retrasados introducidos por procesamiento específico de la variable -** Diferentes variables varían inherentemente en sus tiempos de procesamiento, a veces significativamente, dependiendo de las operaciones que desencadenen._

_Afortunadamente, hay posibles soluciones a ambos temas._

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (78).png" alt=""><figcaption></figcaption></figure>

_Interceptamos la solicitud de añadir al carrito la chaqueta._

<figure><img src="../.gitbook/assets/imagen (79).png" alt=""><figcaption></figcaption></figure>

_Interceptamos el comprar el contenido del carrito_&#x20;

<figure><img src="../.gitbook/assets/imagen (80).png" alt=""><figcaption></figcaption></figure>

_Ahora lo añadimos al grupo y lo mandamos como único paquete, en el carrito tiene que haber un producto que si que podamos comprar y también en el grupo tiene que estar el orden: aádir chaqueta, comprar, añadir chaqueta._

## Condiciones de la carrera de un solo extremo

_Enviar solicitudes paralelas con diferentes valores a una sola variable a veces puede desencadenar poderosas condiciones de carrera._ _Considere un mecanismo de restablecimiento de contraseña que almacena el ID de usuario y resticio de token en la sesión del usuario._ _En este escenario, enviar dos solicitudes paralelas de restablecimiento de contraseña de la misma sesión, pero con dos nombres de usuario diferentes, podría potencialmente causar la siguiente colisión:_&#x20;

<figure><img src="../.gitbook/assets/imagen (81).png" alt=""><figcaption></figcaption></figure>

_Observe el estado final cuando todas las operaciones estén completas:_

* _`session['reset-user'] = victim`_
* _`session['reset-token'] = 1234`_ _Para que este ataque funcione, las diferentes operaciones realizadas por cada proceso deben ocurrir en el orden correcto. Probablemente requeriría múltiples intentos, o un poco de suerte, para lograr el resultado deseado._ _Las confirmaciones de direcciones de correo electrónico, o cualquier operación basada en correo electrónico, son generalmente un buen objetivo para las condiciones de carrera de un solo punto. Los correos electrónicos a menudo se envían en un hilo de fondo después de que el servidor emite la respuesta HTTP al cliente, haciendo que las condiciones de la carrera sean más probables._

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (82).png" alt=""><figcaption></figcaption></figure>

_Tenemos un panel en el cual nos da la posibilidad de cambiar nuestra dirección de correo._

<figure><img src="../.gitbook/assets/imagen (83).png" alt=""><figcaption></figcaption></figure>

_El grupo que vamos a hacer tiene que constar de dos solicitudes, una en la que la persona a la que le llegue el correo somos nosotros y otra en la que tiene que ser el correo de carlos y nos llegará a nosotros el codigo de carlos._

<figure><img src="../.gitbook/assets/imagen (84).png" alt=""><figcaption></figcaption></figure>

_Ahora nos ha llegado el correo de cambiar de Carlos al que le hemos indicado_

## Mecanismos de bloqueo basados en sesiones

_Algunos marcos tratan de prevenir la corrupción accidental de datos utilizando algún tipo de bloqueo de solicitudes. Por ejemplo, el módulo de manejador de sesión nativa de PHP solo procesa una solicitud por sesión a la vez._

_Es extremadamente importante detectar este tipo de comportamiento, ya que de lo contrario puede enmascarar vulnerabilidades triviamente explotables. Si te das cuenta de que todas tus solicitudes están siendo procesadas secuencialmente, intente enviarlas a cada una de ellas usando un símbolo de sesión diferente._

## Ataques sensibles al tiempo

_A veces puede que no encuentres condiciones de carrera, pero las técnicas para entregar solicitudes con un momento preciso todavía pueden revelar la presencia de otras vulnerabilidades._ _Considere una ficha de restablecimiento de contraseña que sólo se aleatoriza usando una marca de tiempo. En este caso, podría ser posible activar dos desconácidos desconácelos para dos usuarios diferentes, que ambos utilizan el mismo token. Todo lo que tienes que hacer es tiempo las peticiones para que generen la misma marca._

#### EJEMPLO PRÁCTICO



{% embed url="https://youtu.be/nalN7XAjYus" %}
