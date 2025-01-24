# 🦹‍♂️ What to try?

## Mini-Tutoriales Ofensivos sobre Vulnerabilidades de Web Cache (Enfocado en Bug Bounty)

### 1. **Cache Poisoning mediante Headers**

#### Descripción

Manipula headers como `Host`, `X-Forwarded-Host` o `X-Original-URL` para inyectar contenido malicioso en la caché del servidor.

***

### 2. **Cache Deception Attack**

#### Descripción

Agrega extensiones al final de la URL (`/private-data/secret.pdf`) para engañar al sistema de caché y almacenar contenido sensible.

***

### 3. **Cache Poisoning con Query Parameters**

#### Descripción

Envía solicitudes con parámetros modificados (`/page?lang=en<script>`) y verifica si puedes inyectar contenido persistente en la respuesta almacenada.

***

### 4. **Cache Key Collision**

#### Descripción

Manipula parámetros no utilizados en la URL para generar colisiones de caché y servir contenido no deseado a otros usuarios.

***

### 5. **Cache Invalidation**

#### Descripción

Prueba si puedes invalidar contenido de la caché enviando solicitudes específicas que alteren el comportamiento del sistema de almacenamiento.

***

### 6. **Ejecución de Scripts mediante Web Cache**

#### Descripción

Intenta cargar un script malicioso en la respuesta almacenada en caché para que otros usuarios lo ejecuten al acceder a la página.

***

### 7. **Exploración de Contenido Cacheado**

#### Descripción

Solicita diferentes recursos con herramientas como Burp Repeater para descubrir contenido previamente cacheado que debería ser privado.

***

### 8. **Cache Timing Attack**

#### Descripción

Mide los tiempos de respuesta para identificar qué recursos están almacenados en la caché y cuáles no, revelando posibles configuraciones inseguras.

***

### 9. **Bypass de Autenticación mediante Caché**

#### Descripción

Verifica si puedes acceder a contenido protegido solicitándolo desde un contexto no autenticado y recibiendo respuestas cacheadas.

***

### 10. **Cacheable HTTPS Responses**

#### Descripción

Identifica si las respuestas de páginas HTTPS se están almacenando en la caché, lo que puede exponer contenido sensible.

***

### 11. **Inyección de Contenido Persistente**

#### Descripción

Manipula el contenido de la respuesta para insertar código HTML o scripts en la versión cacheada del recurso.

***

### 12. **Cache Partitioning**

#### Descripción

Envía solicitudes con diferentes valores de cookies o parámetros de usuario para identificar si puedes acceder al contenido cacheado de otros usuarios.

***

### 13. **Ataques de Inyección en JSON Cacheado**

#### Descripción

Intenta modificar respuestas JSON almacenadas en la caché para incluir datos que puedan explotar funcionalidades de front-end.

***

### 14. **Evade Controles de Seguridad con Caché**

#### Descripción

Prueba si configuraciones de caché permiten eludir headers de seguridad como `Content-Security-Policy` o `X-Frame-Options` en respuestas almacenadas.

***

### 15. **Persistencia de Contenido Obsoleto**

#### Descripción

Busca versiones antiguas de contenido almacenadas en la caché que puedan contener vulnerabilidades ya parcheadas en la versión actual.

***
