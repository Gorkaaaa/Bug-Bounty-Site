# 🦅 What to try?

## Mini-Tutoriales Ofensivos sobre Vulnerabilidades de Web Sockets (Enfocado en Bug Bounty)

### 1. **Falta de Autenticación en la Conexión**

#### Descripción

Intenta conectarte al endpoint de WebSocket sin autenticación y verifica si puedes enviar o recibir datos restringidos.

***

### 2. **Subida de Mensajes Malformados**

#### Descripción

Envía mensajes malformados o con datos inesperados al servidor de WebSocket para provocar errores o filtrar información.

***

### 3. **Exposición de Información Sensible**

#### Descripción

Intercepta mensajes y busca datos sensibles como credenciales, tokens o información personal que pueda estar siendo transmitida en texto plano.

***

### 4. **Inyección de Comandos en Mensajes**

#### Descripción

Manipula los mensajes enviados al servidor para incluir comandos que puedan ser ejecutados en el lado del servidor.

***

### 5. **Persistencia de Sesión Vulnerable**

#### Descripción

Intenta reutilizar un token de sesión o credenciales de una conexión WebSocket en otra instancia para acceder a datos no autorizados.

***

### 6. **Cross-Site WebSocket Hijacking (CSWSH)**

#### Descripción

Crea un script en un dominio externo para intentar interceptar o interactuar con la conexión WebSocket de otro usuario.

***

### 7. **Fuzzing de Mensajes WebSocket**

#### Descripción

Envía mensajes con datos grandes, binarios o inesperados para identificar posibles vulnerabilidades en la validación de entradas del servidor.

***

### 8. **Ejecución de Scripts Maliciosos**

#### Descripción

Prueba si puedes enviar scripts maliciosos que serán ejecutados en el navegador de otro usuario a través de mensajes de WebSocket.

***

### 9. **Inyección SQL/NoSQL a través de WebSockets**

#### Descripción

Envía payloads SQL o NoSQL en los mensajes para probar si el servidor realiza consultas inseguras basadas en el contenido de los mensajes.

***

### 10. **Ataques de Denegación de Servicio (DoS)**

#### Descripción

Abre múltiples conexiones simultáneas al servidor de WebSocket o envía mensajes masivos para sobrecargar el servicio.

***

### 11. **Replay de Mensajes**

#### Descripción

Intercepta mensajes de WebSocket y reenvíalos al servidor para verificar si puedes realizar acciones repetidas no autorizadas.

***

### 12. **Manipulación de Headers de la Conexión**

#### Descripción

Modifica los headers de la solicitud WebSocket inicial, como `Origin` o `Sec-WebSocket-Key`, para intentar evadir restricciones.

***

### 13. **Inyección de Datos Persistentes**

#### Descripción

Envía mensajes que alteren datos almacenados en el servidor, como configuraciones de usuario o estados de sesiones.

***

### 14. **Bypass de Restricciones de Dominio**

#### Descripción

Intenta conectarte al WebSocket desde un origen no permitido para verificar si las restricciones de dominio están correctamente configuradas.

***

### 15. **Comunicaciones Sin Cifrar**

#### Descripción

Intercepta conexiones WebSocket que no utilizan `wss://` y analiza los mensajes para obtener datos sensibles transmitidos en texto plano.

***
