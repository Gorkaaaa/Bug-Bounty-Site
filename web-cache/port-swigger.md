# 💆‍♂️ Port Swigger

https://portswigger.net/web-security/learning-paths/web-cache-deception

## Web Caché

#### **¿Qué es el Web Cache Deception?**

Es un ataque que explota las políticas de almacenamiento en caché de un servidor o un proxy inverso. Los atacantes logran que información confidencial (que normalmente no se debería almacenar en caché) sea guardada en el caché público, permitiendo a otros usuarios acceder a esa información.

#### **¿Cómo funciona?**

Los servidores de caché como Cloudflare, Akamai, o proxies inversos, almacenan respuestas a solicitudes web para reducir la carga en el servidor y mejorar el rendimiento. Sin embargo, si el servidor de caché está mal configurado, puede almacenar contenido sensible que debería ser dinámico y exclusivo para cada usuario.

Un atacante puede intentar engañar al servidor de caché para que:

1. Trate una respuesta privada (como un panel de usuario o un archivo sensible) como si fuera una respuesta pública.
2. Almacene esa respuesta en la caché pública.
3. Otros usuarios puedan acceder a esa información "cacheada".

#### **Ejemplo de ataque de Web Cache Deception**

1. **Escenario inicial:**
   * El atacante encuentra una URL en una aplicación web que devuelve contenido privado, como `/dashboard`.
   * Normalmente, esta URL no debería ser cacheada, ya que el contenido es específico de cada usuario.
2. **Engaño al caché:**
   * El atacante manipula la URL agregando una extensión que suele ser cacheada, como:

#### **Pasos para Detectar Web Cache Deception en Bug Bounty**

1. **Identificar el objetivo adecuado:**
   * Busca aplicaciones que usen servidores de caché como:
     * **CDNs (Content Delivery Networks):** Cloudflare, Akamai, Fastly, etc.
     * **Proxies inversos:** Nginx, Varnish, etc.
   * Revisa si el objetivo tiene rutas dinámicas que presenten contenido sensible o información específica del usuario.

***

2.  **Analizar la respuesta de la caché:**

    * Encuentra rutas protegidas o privadas, como:
      * Paneles de usuario (`/dashboard`)
      * Historial de compras (`/orders`)
      * Información personal (`/profile`)
    * Manipula estas rutas añadiendo extensiones comúnmente cacheadas, por ejemplo:
      * `/dashboard/profile.css`
      * `/orders/details.jpg`
      * `/profile.js`

    Observa si el servidor sigue devolviendo el contenido sensible a pesar del cambio en la URL.

***

3. **Observar encabezados HTTP:**
   * Usa herramientas como **Burp Suite**, **Postman**, o el navegador para revisar los encabezados de respuesta.
   * Fíjate en:
     * **Cache-Control:** Si permite `public` o no tiene restricciones.
     * **Expires:** Si indica una fecha futura que habilita el almacenamiento en caché.
   * Si el servidor incluye encabezados como `Cache-Control: public` en respuestas privadas, podría ser vulnerable.

***

4.  **Explotar el comportamiento de la caché:**

    * Realiza una solicitud con la URL manipulada y verifica si:
      * El contenido se almacena en caché.
      * Otros usuarios pueden acceder a esa URL y ver la misma respuesta.

    Puedes usar un navegador en modo incógnito o enviar la URL manipulada a un amigo para comprobar si la caché está expuesta.

***

5. **Ejemplo Práctico:**
   * Supongamos que la URL `/dashboard` muestra información del usuario.
   * Pruebas con `/dashboard/sensitive-info.css`.
   * La respuesta sigue mostrando la información sensible pero ahora incluye encabezados de caché, como:

_Cuando se hace una solicitud de el mismo recurso estático en el futuro, la caché sirve la copia almacenada de la respuesta directamente al usuario (conocido como un golpe en caché)._

<figure><img src="../.gitbook/assets/imagen (133).png" alt=""><figcaption></figcaption></figure>

_El caching se ha convertido en un aspecto común y crucial de la entrega de contenido web, particularmente con el uso generalizado de Content Delivery Networks (CDNs), que utilizan caché para almacenar copias de contenido en servidores distribuidos en todo el mundo. Los CDN aceleran la entrega sirviéndo contenido desde el servidor más cercano al usuario, reduciendo los tiempos de carga minimizando los viajes de datos de distancia._

### Caché Keys

_Cuando la caché recibe una solicitud HTTP, debe decidir si hay una respuesta en caché que puede servir directamente, o si tiene que reenviar la solicitud al servidor de origen. La caché toma esta decisión generando una 'teclaca' de elementos de la solicitud HTTP. Normalmente, esto incluye la ruta URL y los parámetros de consulta, pero también puede incluir una variedad de otros elementos como encabezados y tipo de contenido._

_Si la tecla de caché de la solicitud entrante coincide con la de una solicitud anterior, la caché considera que son equivalentes y sirve una copia de la respuesta en caché._

### Reglas de caché

_Las reglas de la caché determinan qué se puede almacenar en el caché y por cuánto tiempo. Las reglas de la caché a menudo se establecen para almacenar recursos estáticos, que generalmente no cambian con frecuencia y se reutilizan en varias páginas. El contenido dinámico no está en caché, ya que es más probable que contenga información sensible, asegurando que los usuarios obtendrán los últimos datos directamente del servidor._ _Los ataques de engaño con caché en la Web explotan cómo se aplican las reglas de la caché, por lo que es importante saber sobre algunos tipos diferentes de reglas, particularmente aquellas basadas en cadenas definidas en la ruta URL de la solicitud. Por ejemplo:_

* _Reglas de extensión de archivos estáticas - Estas reglas coinciden con la extensión de archivo del recurso solicitado, por ejemplo `.css`para hojas de estilo o `.js`para archivos de JavaScript._
* _Reglas de directorio estático - Estas reglas coinciden con todas las rutas URL que comienzan con un prefijo específico. Estos se utilizan a menudo para dirigirse a directorios específicos que contienen sólo recursos estáticos, por ejemplo `/static`o o `/assets`._
* _Reglas de nombre del archivo - Estas reglas coinciden con nombres de archivos específicos para los archivos de destino que son universalmente requeridos para operaciones web y cambian rara vez, tales como `robots.txt`y `favicon.ico`._ _Los Caches también pueden implementar reglas personalizadas basadas en otros criterios, como parámetros de URL o análisis dinámico._

### Construir un ataque de engaño en caché web

_En términos generales, la construcción de un ataque básico de engaño con caché en la web implica los siguientes pasos:_

1. _Identificar una variable objetivo que devuelve una respuesta dinámica que contenga información sensible. Reseña las respuestas en Burp, ya que alguna información sensible puede no ser visible en la página renderizado. Céntese en los endpoints que apoyan la `GET`, `HEAD`, o `OPTIONS`Los métodos como solicitudes que alteran el estado del servidor de origen generalmente no se cachan._
2. _Identificar una discrepancia en cómo el servidor de caché y origen analizan la ruta URL. Esto podría ser una discrepancia en cómo:_
   1. _Mapa URLs a recursos._
   2. _Personajes delimitadores de procesos._
   3. _Normalizar caminos._
3. _Elabora una URL maliciosa que utiliza la discrepancia para engañar al caché en el almacenamiento de una respuesta dinámica. Cuando la víctima accede a la URL, su respuesta se almacena en el caché. Utilizando Burp, puedes enviar una solicitud a la misma URL para buscar la respuesta en caché que contiene los datos de la víctima. Evite hacer esto directamente en el navegador ya que algunas aplicaciones redirten a los usuarios sin una sesión o invalidar datos locales, que podrían ocultar una vulnerabilidad._

### Usando Caché Buster

_**Al tiempo que prueba para discrepancias y la elaboración de un engaño en caché web exploit, asegúrese de que cada solicitud que envíe tiene una clave de caché diferente. De lo contrario, se le pueden dar respuestas en caché, lo que afectará a los resultados de su prueba.**_ _Como la ruta de URL y cualquier parámetro de consulta se incluyen típicamente en la tecla caché, puede cambiar la tecla añadiendo una cadena de consulta a la ruta y cambiándola cada vez que envíe una solicitud. Automatizar este proceso con la extensión Param Miner. Para hacer esto, una vez que haya instalado la extensión, haga clic en el menú **minero de nivel** superior **de Param y Configuración**, luego **seleccione Agregar cachebuster dinámico**. Burp ahora añade una cadena de consulta única a cada petición que hagas. Puedes ver las cuerdas de consulta añadidas en la pestaña **Logger**._

## Detectar respuestas en caché

_Durante las pruebas, es crucial que seas capaz de identificar las respuestas en caché. Para ello, mire las cabeceras de respuesta y los tiempos de respuesta._ _Varias cabeceras de respuesta pueden indicar que está en caché. Por ejemplo:_

* _El `X-Cache` La cabecera proporciona información sobre si se dio una respuesta desde el caché. Los valores típicos incluyen:_
  * _`X-Cache: hit`- La respuesta fue atendida desde el cahché._
  * _`X-Cache: miss`- El caché no contenía una respuesta para la clave de la solicitud, por lo que fue liciada desde el servidor de origen. En la mayoría de los casos, la respuesta se cach es entonces en caché. Para confirmarlo, envíe la solicitud de nuevo para ver si las actualizaciones de valor a golpear._
  * _`X-Cache: dynamic`- El servidor de origen generó dinámicamente el contenido. Generalmente esto significa que la respuesta no es adecuada para caché._
  * _`X-Cache: refresh`- El contenido en caché estaba desactualizado y necesitaba ser refrescado o revalidado._
* _El `Cache-Control`cabecera puede incluir una directiva que indica el caché, como `public`con un `max-age`superior al `0`. Tenga en cuenta que esto sólo sugiere que el recurso es caché. No siempre es indicativo de la caché, ya que el caché a veces puede anular esta cabecera._

_Si usted nota una gran diferencia en el tiempo de respuesta para la misma solicitud, esto también puede indicar que la respuesta más rápida se sirve desde la caché._

### Abastir las reglas de la caché de extensión estática

_Las reglas de caché a menudo apuntan a recursos estáticos al emparendo extensiones de archivos comunes como `.css`o o `.js`. Este es el comportamiento predeterminado en la mayoría de los CDN._ _Si hay discrepancias en cómo el servidor de caché y origen mapea la ruta URL a los recursos o utiliza delimitadores, un atacante puede ser capaz de crear una solicitud de un recurso dinámico con una extensión estática que es ignorada por el servidor de origen pero vista por la caché._

### Discrepa discrepancias en el mapeo

_La asignación de rutas de URL es el proceso de asociar rutas URL con recursos en un servidor, como archivos, scripts o ejecuciones de comandos. Hay una gama de diferentes estilos de mapeo utilizados por diferentes marcos y tecnologías. Dos estilos comunes son la asignación de URL tradicional y la asignación de URL RESTful._

_La asignación tradicional de URL representa una ruta directa a un recurso ubicado en el sistema de archivos. He aquí un ejemplo típico:_ _`http://example.com/path/in/filesystem/resource.html`_

* _`http://example.com`apunta al servidor._
* _`/path/in/filesystem/`representa la ruta de directorio en el sistema de archivos del servidor._
* _`resource.html`es el archivo específico a la que se accede._

_En contraste, las URLs al estilo REST no coinciden directamente con la estructura de archivos físicos. Absúan las rutas de archivo en partes lógicas de la API:_

_`http://example.com/path/resource/param1/param2`_

* _`http://example.com`apunta al servidor._
* _`/path/resource/`es un punto final que representa un recurso._
* _`param1`y `param2`son parámetros de ruta utilizados por el servidor para procesar la solicitud._

_Dispancias en cómo el servidor de caché y origen mapear la ruta URL a los recursos puede resultar en vulnerabilidades de engaño en caché web. Considere el siguiente ejemplo:_ _`http://example.com/user/123/profile/wcd.css`_

* _Un servidor de origen que utiliza la asignación de URL al estilo REST puede interpretar esto como una solicitud para el `/user/123/profile`endpoint y devuelve la información del perfil para el usuario `123`, ignorando `wcd.css`como parámetro no significativo._
* _Una caché que utiliza la asignación de URL tradicional puede ver esto como una solicitud para un archivo nombrado `wcd.css`situado en el `/profile`directorio bajo `/user/123`. Interpreta la ruta URL como `/user/123/profile/wcd.css`. Si la caché se configura para almacenar respuestas para las solicitudes donde el camino termina en `.css`, caché y serviría la información del perfil como si fuera un archivo CSS._

### Explotando las discrepancias de mapeo de rutas

_Para probar cómo el servidor de origen mapea la ruta URL a los recursos, agregue un segmento de ruta arbitraria a la URL de su punto final de destino. Si la respuesta todavía contiene los mismos datos sensibles que la respuesta de base, indica que el servidor de origen abstrae la ruta URL e ignora el segmento añadido. Por ejemplo, este es el caso si se modifica `/api/orders/123`a `/api/orders/123/foo`todavía devuelve la información del pedido._ _Para probar cómo la caché mapea la ruta URL a los recursos, tendrás que modificar la ruta para intentar que coincido con una regla de caché añadiendo una extensión estática. Por ejemplo, actualización `/api/orders/123/foo`a `/api/orders/123/foo.js`. Si la respuesta está en caché, esto indica:_

* _Que la caché interpreta la ruta URL completa con la extensión estática._
* _Que hay una regla de caché para almacenar respuestas para las solicitudes que terminan en `.js`._ _Los cachés pueden tener reglas basadas en extensiones estáticas específicas. Prueba una gama de extensiones, incluyendo `.css`, `.ico`, y `.exe`._ _A continuación, puede elaborar una URL que devuelve una respuesta dinámica que se almacena en la caché. Tenga en cuenta que este ataque se limita a la variable específica que probó, ya que el servidor de origen a menudo tiene reglas de abstracción diferentes para diferentes endpoints._

_Burp Scanner detecta automáticamente vulnerabilidades de engaño en caché web que son causadas por discrepancias de mapeo de rutas durante las auditorías. También puede utilizar el escáner de engaño de caché web BApp para detectar alijos web malfigurados._

#### EJEMPLO PRÁCTICO

**Objetivo**

* Nuestro objetivo es acceder al panel de el usuario carlos abusanco del caché.

<figure><img src="../.gitbook/assets/imagen (134).png" alt=""><figcaption></figcaption></figure>

_Tenemos un panel nuestro en el que podemos ver información nuestra._

<figure><img src="../.gitbook/assets/imagen (135).png" alt=""><figcaption></figcaption></figure>

_Alteramos la Url y vemos que tenemos exactamente el mismo contenido... Vamos a verlo a través de BurpSuite._

<figure><img src="../.gitbook/assets/imagen (136).png" alt=""><figcaption></figcaption></figure>

_Podemos ver en la respuesta información `miss` respecto al caché, esto podemos ver que ha funcionado correctamente._

<figure><img src="../.gitbook/assets/imagen (137).png" alt=""><figcaption></figcaption></figure>

_Ahora le enviaremos este script al usuario el cual queramos cachear su session y veremos que pasa caundo lo abra..._

<figure><img src="../.gitbook/assets/imagen (138).png" alt=""><figcaption></figcaption></figure>

_Ahora vemos que estamos en la session de `Carlos`_

## Discrepancias de Delimitadores en URLs

### ¿Qué son los delimitadores?

* Separan diferentes partes de una URL.
* Ejemplo:
  * `?` separa la ruta de los parámetros de consulta.
  * `;` puede indicar parámetros adicionales.

### Problema

* Los estándares como el **RFC 3986** no siempre se siguen estrictamente.
* Diferentes tecnologías interpretan los delimitadores de formas distintas, causando inconsistencias entre:
  * **Servidor de origen** (procesa la solicitud).
  * **Sistema de caché** (almacena respuestas para agilizar solicitudes futuras).

### Ejemplo de Vulnerabilidad

1. **Java Spring Framework:**
   * Interpreta `;` como delimitador para _variables de matriz_.
   * `/profile;foo.css` se interpreta como `/profile` con un parámetro adicional `foo.css`.
2. **Sistemas de caché no compatibles:**
   * Interpretan `/profile;foo.css` como un recurso completo.
   * Si almacenan respuestas basadas en extensiones (`.css`), podrían guardar y entregar contenido sensible (como información de perfil) erróneamente.

### Riesgo

* Vulnerabilidad de **engaño en caché** (_cache poisoning_), donde:
  * Información sensible es almacenada y entregada incorrectamente a otros usuarios.

## Discrepancias de Delimitadores - Continuación

### Uso Inconsistente de Otros Delimitadores

Las discrepancias también ocurren con otros caracteres interpretados de forma diferente entre tecnologías. Por ejemplo, **Ruby on Rails** usa `.` como delimitador para determinar el formato de la respuesta.

#### Ejemplo en Ruby on Rails

1. **Solicitud Válida**: `/profile`
   * Procesada por el **formateador HTML predeterminado**.
   * Devuelve la información del perfil del usuario.
2. **Extensión No Reconocida**: `/profile.css`
   * Tratada como una **solicitud de archivo CSS**.
   * Rails no puede procesarla y devuelve un **error**.
3. **Fallback al Formateador Predeterminado**: `/profile.ico`
   * La extensión `.ico` no es reconocida.
   * Rails usa el **formateador HTML predeterminado** y devuelve la información del perfil.
   * **Problema:** Si el sistema de caché está configurado para almacenar respuestas que terminan en `.ico`, podría **guardar y servir información del perfil como si fuera un archivo estático**.

### Riesgo

* Las respuestas pueden ser malinterpretadas y almacenadas de forma incorrecta en el caché, exponiendo información sensible.

## Discrepancias de Delimitadores - Continuación

### Uso de Caracteres Codificados como Delimitadores

Los caracteres codificados también pueden actuar como delimitadores, generando inconsistencias. Por ejemplo, la solicitud `/profile%00foo.js`:

#### Ejemplo de Diferentes Interpretaciones

1. **Servidor OpenLiteSpeed:**
   * Interpreta `%00` (carácter nulo codificado) como un delimitador.
   * La ruta se interpreta como `/profile`.
2. **Otros Marcos:**
   * La mayoría rechazan URLs con `%00` y devuelven un **error**.
3. **Sistemas de Caché (Akamai o Fastly):**
   * Interpretan `%00` y todo lo que sigue como parte de la ruta completa.
   * **Problema:** Si el caché está configurado para almacenar respuestas basadas en extensiones (`.js`), podría guardar contenido incorrecto asociado a la URL `/profile%00foo.js`.

### Riesgo

* Puede llevar a la **almacenación incorrecta de respuestas** en el caché, permitiendo la exposición o distribución de información sensible.

## Explotación de Discrepancias de Delimitadores

### Objetivo

Las discrepancias en los delimitadores pueden ser explotadas para **agregar una extensión estática a la ruta** vista por el caché, pero no por el servidor de origen. El proceso consiste en identificar un carácter que sea utilizado como delimitador por el servidor de origen, pero no por el sistema de caché.

#### Pasos para Explotar Discrepancias de Delimitadores

1.  **Identificar caracteres como delimitadores del servidor de origen:**

    * Comienza agregando una cadena arbitraria a la URL del punto de destino, por ejemplo, cambia `/settings/users/list` a `/settings/users/listaaa`.
    * La respuesta de esta solicitud será tu referencia cuando empieces a probar los caracteres delimitadores.

    **Nota:** Si la respuesta es idéntica a la respuesta original, significa que la solicitud está siendo **redirigida**. En este caso, deberás elegir otro punto de destino para probar.
2. **Agregar un posible delimitador:**
   * Añade un delimitador entre la ruta original y la cadena arbitraria, por ejemplo, `/settings/users/list;aaa`.
   * **Si la respuesta es idéntica a la respuesta base**, esto indica que el carácter `;` está siendo utilizado como delimitador y el servidor de origen interpreta la ruta como `/settings/users/list`.
   * **Si la respuesta coincide con la de la ruta con la cadena arbitraria**, esto indica que el carácter `;` **no se usa como delimitador**, y el servidor de origen interpreta la ruta como `/settings/users/list;aaa`.

### Riesgo

* Al explotar estas discrepancias, un atacante podría manipular cómo los sistemas de caché almacenan y sirven contenido, exponiendo información sensible de manera inapropiada.

## Explotación de Discrepancias de Delimitadores - Continuación

### Comprobación de Delimitadores en el Caché

Una vez que hayas identificado los delimitadores utilizados por el servidor de origen, es necesario comprobar si también son utilizados por el sistema de caché. Para ello, agrega una **extensión estática** al final de la ruta y observa la respuesta.

#### Pasos para Verificar el Comportamiento del Caché

1. **Agregar una extensión estática**:
   * Modifica la URL de la solicitud agregando una extensión estática al final, por ejemplo, `/settings/users/list.js`.
2. **Si la respuesta se almacena en caché**, esto indica lo siguiente:
   * El **caché no utiliza el delimitador** y considera toda la URL con la extensión estática como una ruta válida.
   * Existe una **regla de caché** que almacena respuestas para solicitudes que terminan en `.js`.

#### Probar Diferentes Caracteres y Extensiones

* Asegúrate de probar **todos los caracteres ASCII** y una variedad de extensiones comunes, como `.css`, `.ico` y `.exe`.
* Usa la **lista de caracteres delimitadores** proporcionada en los laboratorios (como la lista de delimitadores en el laboratorio de engaño de caché web) para comenzar las pruebas.

#### Uso de Burp Intruder

* Utiliza **Burp Intruder** para probar rápidamente estos caracteres.
* Para evitar que Burp Intruder codifique automáticamente los caracteres delimitadores, **desactiva la codificación de caracteres automatizada** en la sección "Payload encoding" del panel de Payloads.

### Riesgo

* Este tipo de pruebas puede ser explotado para manipular cómo el caché almacena contenido, creando vulnerabilidades de **engaño en caché** y exponiendo información sensible a usuarios no autorizados.

## Explotación de Discrepancias de Delimitadores - Continuación

### Construcción de un Explot de Discrepancias de Delimitadores

Una vez que hayas identificado un delimitador que se usa en el servidor de origen pero no en el caché, puedes crear un **exploit** que active la regla de caché para una extensión estática.

#### Ejemplo de Payload

Considera el siguiente payload: `/settings/users/list;aaa.js`

1. **Comportamiento del Caché**:
   * El caché interpreta la ruta completa como: `/settings/users/list;aaa.js`
2. **Comportamiento del Servidor de Origen**:
   * El servidor de origen interpreta la ruta como: `/settings/users/list`
   * Devuelve la **información dinámica del perfil**.
3. **Vulnerabilidad**:
   * La respuesta dinámica del perfil se almacena en el caché como si fuera un archivo estático (`.js`).
   * Cuando otros usuarios solicitan `/settings/users/list;aaa.js`, el caché les servirá la **información del perfil**.

### Notas Importantes

* **Consistencia en los delimitadores**: Los delimitadores se usan generalmente de manera coherente dentro de cada servidor, lo que hace que este ataque sea efectivo en muchos puntos finales.
* **Limitaciones del Navegador**:
  * Algunos delimitadores pueden ser procesados por el **navegador** antes de que se reenvíe la solicitud al caché, lo que impide su uso en un exploit. Por ejemplo, los navegadores:
    * **Codifican caracteres** como `{`, `}`, `<`, `>` y usan `#` para truncar la ruta.
  * **Posibilidad de Explotar Caracteres Codificados**: Si el caché o el servidor de origen decodifica estos caracteres, podría ser posible usar una **versión codificada** en el exploit para sortear estas restricciones.

### Riesgo

* Este tipo de explotación puede comprometer la seguridad de la aplicación, ya que el caché puede entregar **información sensible** de forma inapropiada.

#### EJEMPLO PRÁCTICO

LISTA:https://pastebin.com/6ft4VvAy

<figure><img src="../.gitbook/assets/imagen (139).png" alt=""><figcaption></figcaption></figure>

_Si alteramos la URL podemos ver que nos finroma de que el recurso no ha sido encontrado._

<figure><img src="../.gitbook/assets/imagen (140).png" alt=""><figcaption></figcaption></figure>

_Buscamos la solicitud en el Burp-Suite_

<figure><img src="../.gitbook/assets/imagen (141).png" alt=""><figcaption></figcaption></figure>

_Agreagmos en esa posición las llaves._

<figure><img src="../.gitbook/assets/imagen (142).png" alt=""><figcaption></figcaption></figure>

_Descativamos esta casilla._

<figure><img src="../.gitbook/assets/imagen (143).png" alt=""><figcaption></figcaption></figure>

_Podemos ver que ha habido algunos con los que no han habido problemas: `;|?`._&#x20;

<figure><img src="../.gitbook/assets/imagen (144).png" alt=""><figcaption></figcaption></figure>

_Ahora agregamos una extension de archivos y comprobamos que realmente funciona._

<figure><img src="../.gitbook/assets/imagen (146).png" alt=""><figcaption></figcaption></figure>

_Enviamos el payload que debe de abrir la víctima._

<figure><img src="../.gitbook/assets/imagen (147).png" alt=""><figcaption></figcaption></figure>

_Hemos conseguido guardar en Cache su sesion._

## Discrepancias en la Decodificación de Delimitadores

### Contexto

A veces, los sitios web necesitan enviar datos en la URL que contienen caracteres con significados especiales, como los delimitadores. Para asegurarse de que estos caracteres sean interpretados como datos, generalmente se codifican. Sin embargo, algunos parsers decodifican ciertos caracteres antes de procesar la URL. Si un delimitador es decodificado, puede ser tratado como un delimitador, truncando la ruta de la URL.

### Discrepancias en la Decodificación entre el Caché y el Servidor de Origen

Cuando un delimitador es decodificado de manera diferente entre el servidor de origen y el caché, pueden surgir discrepancias en la interpretación de la ruta de la URL, incluso si ambos utilizan los mismos caracteres como delimitadores.

#### Ejemplo de Discrepancia

Considera la URL codificada: `/profile%23wcd.css`, donde `%23` es la versión codificada del carácter `#`.

1. **Comportamiento del Servidor de Origen:**
   * El servidor de origen decodifica `%23` a `#`.
   * Usa `#` como delimitador, por lo que interpreta la ruta como `/profile`.
   * Devuelve la **información del perfil**.
2. **Comportamiento del Caché:**
   * El caché también usa `#` como delimitador, pero **no decodifica `%23`**.
   * El caché interpreta la ruta como `/profile%23wcd.css`.
   * Si hay una **regla de caché** para la extensión `.css`, la respuesta será **almacenada**.

### Riesgo

* Este tipo de discrepancia puede causar que **información sensible** sea almacenada y entregada por el caché como si fuera un archivo estático, exponiéndola a usuarios no autorizados.

## Discrepancias en la Decodificación de Delimitadores - Continuación

### Diferentes Comportamientos de Decodificación en Servidores de Caché

Algunos servidores de caché decodifican la URL antes de aplicar las reglas de caché, mientras que otros aplican primero las reglas basadas en la URL codificada y luego decodifican la URL. Estos comportamientos pueden generar discrepancias en la forma en que el caché y el servidor de origen interpretan la ruta de la URL.

#### Ejemplo de Discrepancia

Considera la URL `/myaccount%3fwcd.css`, donde `%3f` es la versión codificada del carácter `?`.

1. **Comportamiento del Servidor de Caché:**
   * El servidor de caché **aplica las reglas de caché** basadas en la URL codificada `/myaccount%3fwcd.css`.
   * Como existe una **regla de caché** para la extensión `.css`, el servidor almacena la respuesta.
   * Luego **decodifica** `%3f` a `?` y reenvía la solicitud modificada al servidor de origen.
2. **Comportamiento del Servidor de Origen:**
   * El servidor de origen recibe la solicitud como `/myaccount?wcd.css`.
   * Utiliza el carácter `?` como delimitador, lo que hace que interprete la ruta como `/myaccount`.
   * Devuelve la **información asociada al perfil** del usuario.

### Riesgo

* Este comportamiento puede provocar que la **información dinámica** se almacene incorrectamente en el caché y se sirva como si fuera un archivo estático (`.css`), lo que podría permitir el acceso no autorizado a información sensible.

## Explotación de Discrepancias en la Decodificación de Delimitadores

### Objetivo

Puedes aprovechar una discrepancia en la decodificación utilizando un **delimitador codificado** para agregar una extensión estática a la ruta que es vista por el caché, pero no por el servidor de origen.

### Metodología de Prueba

Utiliza la misma metodología que se usa para identificar y explotar discrepancias de delimitadores, pero esta vez probando una **gama de caracteres codificados**.

#### Pasos para Explotar la Discrepancia en la Decodificación

1. **Prueba con caracteres codificados**:
   * Añade delimitadores codificados como `%3f` (para `?`), `%23` (para `#`), o caracteres no imprimibles como `%00`, `%0A`, y `%09`.
2. **Prueba con caracteres no imprimibles**:
   * Los caracteres como `%00` (nulo), `%0A` (salto de línea), y `%09` (tabulación) pueden **truncar la ruta de la URL** si son decodificados, y pueden ser utilizados para **explotar la discrepancia**.
3. **Resultado esperado**:
   * Si el caché interpreta la URL codificada de manera diferente que el servidor de origen, es posible que **almacene la respuesta incorrecta** en caché, sirviendo contenido sensible como si fuera un archivo estático.

### Riesgo

* Al explotar estas discrepancias, podrías forzar al caché a almacenar contenido dinámico como estático, lo que permitiría **acceder a información sensible** o modificar el comportamiento esperado de la aplicación.

## Explotación de Reglas de Caché para Directorios Estáticos

### Contexto

Es una práctica común que los servidores web almacenen recursos estáticos en directorios específicos. Las reglas de caché suelen dirigirse a estos directorios mediante coincidencias con **prefijos de rutas de URL** como `/static`, `/assets`, `/scripts` o `/images`.

#### Riesgo de Explotación

Estas reglas de caché pueden ser **vulnerables al engaño en caché web**. Si se aprovechan correctamente, los atacantes pueden forzar al caché a almacenar y entregar información dinámica (como perfiles de usuario) como si fuera un archivo estático.

### Requisitos para Explotar Reglas de Caché de Directorios Estáticos

Para explotar las reglas de caché de directorios estáticos, es necesario entender los **conceptos básicos de los ataques de travesía de ruta**. Estos ataques permiten acceder a ubicaciones fuera del directorio permitido, lo que podría hacer que una solicitud maliciosa sea interpretada como parte de un directorio estático y, por lo tanto, almacenada incorrectamente en el caché.

#### Información Adicional

Para obtener más información sobre cómo realizar ataques de travesía de ruta, consulta el tema **Academia de Travesía de Ruta**.

## Discrepancias de Normalización

### Contexto

La **normalización** consiste en convertir diferentes representaciones de rutas de URL en un formato estándar. Esto a menudo implica decodificar caracteres codificados y resolver segmentos de puntos (`..`), pero esto varía significativamente entre los distintos parsers.

#### Discrepancias en la Normalización entre el Caché y el Servidor de Origen

Las discrepancias en cómo el caché y el servidor de origen normalizan la URL pueden permitir que un atacante construya un **payload de travesía de ruta** que sea interpretado de manera diferente por cada parser, lo que genera una vulnerabilidad de engaño en caché.

#### Ejemplo de Discrepancia

Considera la siguiente URL: `/static/..%2fprofile`, donde `%2f` es la versión codificada del carácter `/`.

1. **Comportamiento del Servidor de Origen:**
   * El servidor de origen **decodifica** los caracteres de barra (`/`) y **resuelve los segmentos de puntos** (`..`), normalizando la ruta a `/profile`.
   * Devuelve la **información del perfil**.
2. **Comportamiento del Caché:**
   * El caché **no resuelve los segmentos de puntos** ni **decodifica las barras**.
   * Interpreta la ruta como `/static/..%2fprofile`.
   * Si existe una **regla de caché** para solicitudes con el prefijo `/static`, almacena y sirve la **información del perfil**.

### Requisitos para Exploit

Para que una discrepancia de normalización sea explotable, **el caché o el servidor de origen deben decodificar caracteres** en la secuencia de travesía de ruta y resolver los segmentos de puntos de manera diferente.

#### Importante

* Cada segmento de puntos en la secuencia de travesía de ruta debe estar **codificado**, ya que de lo contrario, el navegador de la víctima resolverá los segmentos antes de reenviar la solicitud al caché.

### Riesgo

Este tipo de vulnerabilidad puede permitir que un atacante obtenga acceso a **información sensible** a través del caché, al aprovechar las diferencias en la normalización entre el servidor de origen y el caché.

## Detectando la Normalización por parte del Servidor de Origen

### Objetivo

Para comprobar cómo el servidor de origen normaliza la ruta de la URL, envía una solicitud a un recurso no almacenable en caché con una secuencia de travesía de ruta y un directorio arbitrario al principio de la ruta. Para elegir un recurso no almacenable, busca un método no idempotente como **POST**.

#### Ejemplo de Prueba

Modifica la ruta `/profile` a `/aaa/..%2fprofile` (donde `%2f` es el carácter codificado de la barra `/`).

1. **Si la respuesta coincide con la respuesta base** y devuelve la información del perfil, esto indica que la ruta ha sido interpretada como `/profile`. El servidor de origen **decodifica** la barra (`/`) y **resuelve** el segmento de puntos (`..`).
2. **Si la respuesta no coincide con la base** (por ejemplo, devuelve un error 404), esto indica que la ruta ha sido interpretada como `/aaa/..%2fprofile`. El servidor de origen **no decodifica** la barra (`/`) o **no resuelve** el segmento de puntos.

#### Notas Importantes

* **Codificar solo la segunda barra** en el segmento de puntos: Esto es importante porque algunos CDNs pueden hacer coincidir la barra después del prefijo del directorio estático, lo que puede afectar la normalización de la ruta.
* También puedes probar **codificar la secuencia completa de travesía de ruta** o **codificar un punto** en lugar de la barra. Esto puede influir en si el parser decodifica la secuencia correctamente.

### Riesgo

Al comprender cómo se normaliza la ruta, puedes identificar posibles **vulnerabilidades de engaño en caché**, aprovechando las discrepancias en la normalización entre el servidor de origen y el caché.

## Detectando la Normalización por parte del Servidor de Caché

### Objetivo

Para verificar cómo el servidor de caché normaliza la ruta, puedes usar diferentes métodos. El primer paso es **identificar directorios estáticos potenciales**.

#### Pasos para la Prueba

1. **Revisa el historial HTTP** en la herramienta de proxy.
   * Filtra las solicitudes para mostrar solo aquellas que contienen respuestas 2xx y los tipos MIME de **scripts**, **imágenes** y **CSS**.
   * Busca respuestas **almacenadas en caché** con **prefijos de directorios estáticos comunes** como `/assets`, `/static`, etc.
2. **Envío de solicitud modificada**:
   * Escoge una solicitud que tenga una respuesta almacenada en caché.
   * Vuelve a enviar la solicitud con una **secuencia de travesía de ruta** y un **directorio arbitrario** al principio de la ruta estática. Por ejemplo: `/aaa/..%2fassets/js/stockCheck.js`.

#### Ejemplo de Prueba

* **Si la respuesta ya no está en caché**, esto indica que el caché **no está normalizando** la ruta antes de mapearla al punto final. También sugiere que hay una regla de caché basada en el prefijo `/assets`.
* **Si la respuesta sigue estando en caché**, esto puede indicar que el caché **ha normalizado** la ruta a `/assets/js/stockCheck.js`, y está manejando la normalización antes de almacenar la respuesta.

### Riesgo

* Si el caché no normaliza correctamente la URL, puede almacenar contenido dinámico como si fuera un recurso estático, lo que puede resultar en **engaños de caché** y filtración de información sensible.

## Detectando la Normalización por parte del Servidor de Caché - Continuación

### Método Adicional de Prueba

También puedes agregar una **secuencia de travesía de ruta** después del prefijo del directorio. Por ejemplo, modifica la ruta `/assets/js/stockCheck.js` a `/assets/..%2fjs/stockCheck.js`.

#### Ejemplo de Prueba

1. **Si la respuesta ya no está en caché**, esto indica que el caché **decodifica** la barra (`/`) y **resuelve** el segmento de puntos (`..`) durante la normalización, interpretando la ruta como `/js/stockCheck.js`. Esto sugiere que existe una **regla de caché** basada en el prefijo `/assets`.
2. **Si la respuesta sigue estando en caché**, esto puede indicar que el caché **no ha decodificado** la barra (`/`) ni ha resuelto el segmento de puntos (`..`), interpretando la ruta como `/assets/..%2fjs/stockCheck.js`.

### Riesgo

Si el caché no maneja adecuadamente la normalización de la URL, podría **almacenar contenido dinámico** de manera incorrecta, permitiendo vulnerabilidades como **engaños de caché**.

## Detectando la Normalización por parte del Servidor de Caché - Continuación

### Confirmación de la Regla de Caché

En ambos casos anteriores, la respuesta puede estar almacenada en caché debido a **otra regla de caché**, como una basada en la **extensión de archivo**. Para confirmar que la regla de caché está basada en el directorio estático, reemplaza la ruta después del prefijo del directorio con una **cadena arbitraria**. Por ejemplo, modifica `/assets/js/stockCheck.js` a `/assets/aaa`.

#### Ejemplo de Prueba

* **Si la respuesta sigue estando en caché**, esto confirma que la regla de caché está basada en el prefijo `/assets`.
* **Si la respuesta no aparece almacenada en caché**, esto no necesariamente descarta una regla de caché para directorios estáticos, ya que a veces **las respuestas 404 no se almacenan en caché**.

#### Nota Importante

Es posible que no puedas determinar de manera definitiva si el caché **decodifica los segmentos de puntos** o **decodifica la ruta de la URL** sin intentar un **exploit**. Por lo tanto, una evaluación práctica o intento de explotación podría ser necesaria para confirmar este comportamiento.

### Riesgo

* Dependiendo de cómo el caché maneje la normalización, puede haber riesgos de **engaño de caché** si el servidor no normaliza las rutas de manera coherente con el origen.

## Explotando la Normalización por parte del Servidor de Origen

Si el servidor de origen resuelve los **segmentos de puntos codificados**, pero el caché no lo hace, puedes intentar explotar la discrepancia construyendo una **carga útil** según la siguiente estructura:

* `/<static-directory-prefix>/..%2f<dynamic-path>`

#### Ejemplo de Explotación

Considera la carga útil `/assets/..%2fprofile`:

* **El caché interpreta** la ruta como: `/assets/..%2fprofile` (sin normalizar).
* **El servidor de origen interpreta** la ruta como: `/profile` (resolviendo el segmento de puntos).

En este caso, el servidor de origen devolvería **información dinámica del perfil**, la cual sería **almacenada en el caché**.

### Riesgo

* Esta discrepancia de normalización puede ser explotada para que el **contenido dinámico** se almacene en el caché como si fuera estático, lo que puede resultar en **engaños de caché** y exposición de información sensible.

#### EJEMPLO PRÁCTICO

LISTA:https://pastebin.com/6ft4VvAy

<figure><img src="../.gitbook/assets/imagen (148).png" alt=""><figcaption></figcaption></figure>

_Navegamos por la web en busca de recursos y hacer un buen sitemap..._

<figure><img src="../.gitbook/assets/imagen (149).png" alt=""><figcaption></figcaption></figure>

_Hemos encontrado la ruta de resources en la cual sospechamos de lo que se almacena ahí puede estar en caché._

<figure><img src="../.gitbook/assets/imagen (150).png" alt=""><figcaption></figcaption></figure>

_Realizamos este Path-Traversal para comprobar que no tenemos ningun problema para movernos entre directorios._

<figure><img src="../.gitbook/assets/imagen (151).png" alt=""><figcaption></figcaption></figure>

_Mandamos la solicitud al intruder de esta manera._

<figure><img src="../.gitbook/assets/imagen (152).png" alt=""><figcaption></figcaption></figure>

_Debemos de desactivar esta opcion._

<figure><img src="../.gitbook/assets/imagen (153).png" alt=""><figcaption></figcaption></figure>

_Podemos encontrar el limitador que almacena en caché._

<figure><img src="../.gitbook/assets/imagen (154).png" alt=""><figcaption></figcaption></figure>

_Enviamos este exploit a la víctima._

## Explotando la Normalización por parte del Servidor de Caché

Si el servidor de caché resuelve los **segmentos de puntos codificados**, pero el servidor de origen no lo hace, puedes intentar explotar la discrepancia construyendo una **carga útil** según la siguiente estructura:

* `/<dynamic-path>%2f%2e%2e%2f<static-directory-prefix>`

#### Nota Importante

* Al explotar la normalización por parte del servidor de caché, **codifica todos los caracteres** en la secuencia de travesía de ruta. Usar caracteres codificados ayuda a evitar comportamientos inesperados al usar delimitadores.
* No es necesario tener una barra (`/`) sin codificar después del prefijo del directorio estático, ya que el **servidor de caché** se encargará de la decodificación.

#### Ejemplo de Explotación

Considera la carga útil `/profile%2f%2e%2e%2fstatic`:

* **El caché interpreta** la ruta como: `/static`.
* **El servidor de origen interpreta** la ruta como: `/profile%2f%2e%2e%2fstatic`.

En este caso, el **servidor de origen** probablemente devolverá un **mensaje de error** en lugar de la información del perfil.

#### Riesgo

* La discrepancia en la normalización puede impedir que la explotación funcione si el servidor de origen no maneja correctamente los segmentos de puntos codificados, lo que podría resultar en respuestas de error inesperadas.

## Explotando la Normalización por parte del Servidor de Caché - Continuación

Para explotar esta discrepancia, también necesitarás identificar un **delimitador** que sea utilizado por el servidor de origen, pero no por el caché. Puedes probar delimitadores posibles añadiéndolos a la carga útil después de la ruta dinámica.

#### Cómo Probar los Delimitadores

* **Si el servidor de origen usa un delimitador**, este truncará la ruta de la URL y devolverá la información dinámica.
* **Si el caché no usa el delimitador**, este resolverá la ruta y almacenará la respuesta.

#### Ejemplo de Explotación

Considera la carga útil `/profile;%2f%2e%2e%2fstatic`. El servidor de origen usa `;` como delimitador:

* **El caché interpreta** la ruta como: `/static`.
* **El servidor de origen interpreta** la ruta como: `/profile`.

En este caso, el servidor de origen devolvería la **información dinámica del perfil**, la cual sería **almacenada en el caché**.

#### Riesgo

* Al explotar una discrepancia en los delimitadores, puedes lograr que el contenido dinámico se almacene como estático, lo que puede resultar en un **engaño de caché** y la **exposición no deseada** de información.

#### EJEMPLO PRÁCTICO

LISTA:https://pastebin.com/6ft4VvAy

<figure><img src="../.gitbook/assets/imagen (155).png" alt=""><figcaption></figcaption></figure>

_Navegamos un poco por la página para poder enumerar sitemap..._

<figure><img src="../.gitbook/assets/imagen (156).png" alt=""><figcaption></figcaption></figure>

_Vemos este recurso en el sitemap que tiene la etiqueta miss en el Cache_

<figure><img src="../.gitbook/assets/imagen (157).png" alt=""><figcaption></figcaption></figure>

_Mandamos al repeater la página la cual nos interesa probar (en este caso la infromación del usuario)._

<figure><img src="../.gitbook/assets/imagen (158).png" alt=""><figcaption></figcaption></figure>

_El path traversal no está funcionando pero vamos a investigar delimitadores..._

<figure><img src="../.gitbook/assets/imagen (159).png" alt=""><figcaption></figcaption></figure>

_Lo mandamos al intruder y agregamos las llaves._

<figure><img src="../.gitbook/assets/imagen (160).png" alt=""><figcaption></figcaption></figure>

_Desmarcamos esta casilla_

<figure><img src="../.gitbook/assets/imagen (161).png" alt=""><figcaption></figcaption></figure>

_Ahora hemos identificado el delimitador que debemos de usuar._

<figure><img src="../.gitbook/assets/imagen (162).png" alt=""><figcaption></figcaption></figure>

_Le enviamos el exploit_

## Explotando Reglas de Caché Basadas en el Nombre de Archivo

Algunos archivos como `robots.txt`, `index.html` y `favicon.ico` son comunes en los servidores web. Debido a que estos archivos rara vez cambian, suelen ser almacenados en caché. Las reglas de caché a menudo están diseñadas para hacer coincidir exactamente el **nombre del archivo**.

#### Cómo Detectar una Regla de Caché Basada en el Nombre de Archivo

1. **Envía una solicitud GET** para un archivo posible (por ejemplo, `robots.txt`, `index.html`, `favicon.ico`).
2. **Verifica si la respuesta está almacenada en caché**.

#### Ejemplo de Prueba

* Realiza una solicitud GET para `robots.txt`.
* Si la respuesta está en caché, esto indica que existe una **regla de caché** para este archivo, y el servidor almacena las respuestas asociadas.

#### Riesgo

* Los archivos comunes como `robots.txt` y `index.html` pueden contener información sensible si son mal gestionados. Un atacante podría intentar engañar al servidor de caché para almacenar información dinámica o sensible bajo el nombre de estos archivos.

## Detectando Discrepancias de Normalización

#### Probar la Normalización en el Servidor de Origen

Para probar cómo el **servidor de origen** normaliza la ruta de la URL, utiliza el mismo método que se usa para detectar reglas de caché basadas en directorios estáticos. Más detalles sobre esto en [Detectando normalización por parte del servidor de origen](broken-reference).

#### Probar la Normalización en el Servidor de Caché

Para probar cómo el **servidor de caché** normaliza la ruta de la URL, envía una solicitud con una secuencia de travesía de ruta y un directorio arbitrario antes del nombre del archivo. Por ejemplo:

* `/aaa%2f%2e%2e%2findex.html`

#### Posibles Resultados

* **Si la respuesta está en caché**, esto indica que el caché normaliza la ruta a `/index.html`.
* **Si la respuesta no está en caché**, esto indica que el caché no decodifica la barra (`/`) ni resuelve el segmento de puntos (`..`), interpretando la ruta como `/profile%2f%2e%2e%2findex.html`.

#### Riesgo

Si el caché no maneja correctamente la normalización, puede almacenar información incorrecta o exponerla a través de rutas mal gestionadas.

## Explotando Discrepancias de Normalización

Las respuestas solo se almacenan en caché si la solicitud coincide exactamente con el nombre del archivo. Por lo tanto, se puede explotar una discrepancia **cuando el servidor de caché resuelve los segmentos de puntos codificados**, pero el servidor de origen no lo hace.

#### Método de Explotación

Usa el mismo método que para las **reglas de caché basadas en directorios estáticos**. Simplemente reemplaza el prefijo de directorio estático con el nombre del archivo.

#### Ejemplo de Explotación

Si el servidor de caché resuelve correctamente los segmentos de puntos, pero el servidor de origen no lo hace, se puede construir una carga útil para explotar esta diferencia en la normalización.

#### Riesgo

Al aprovechar las discrepancias de normalización, puedes engañar al servidor de caché para que almacene contenido dinámico como si fuera un archivo estático, lo que podría resultar en la exposición de información confidencial o mal almacenada.
