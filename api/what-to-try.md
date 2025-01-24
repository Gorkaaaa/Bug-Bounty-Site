# 🪳 What to try?

## Mini-Tutoriales Ofensivos Exclusivos sobre Vulnerabilidades en APIs

### 1. **Broken Object Level Authorization (BOLA)**

#### Descripción

Envía solicitudes manipulando identificadores en endpoints como `/users/{userId}`. Cambia el ID para acceder a datos de otros usuarios o realizar acciones que no te corresponden.

***

### 2. **Mass Assignment**

#### Descripción

Envía un payload con campos adicionales, como `role: admin` o `balance: 100000`. Algunas APIs permiten la modificación directa de atributos sensibles.

***

### 3. **Lack of Rate Limiting**

#### Descripción

Ejecuta ataques de fuerza bruta o inundación de solicitudes (`/login`, `/password-reset`) para verificar si el sistema permite abuso continuo sin restricciones.

***

### 4. **Insecure Direct Object References (IDOR)**

#### Descripción

Modifica parámetros en la URL o en el cuerpo de las solicitudes (`orderId`, `userId`). Si puedes acceder o manipular recursos que no te pertenecen, el IDOR es explotable.

***

### 5. **Excessive Data Exposure**

#### Descripción

Intercepta respuestas JSON o XML y busca información adicional no visible en la interfaz, como claves API, datos personales o detalles internos del sistema.

***

### 6. **Improper Error Handling**

#### Descripción

Provoca errores con entradas malformadas (`{'id': 'undefined'}` o `NULL`). Busca mensajes que filtren información como estructuras de base de datos o detalles del stack.

***

### 7. **Injection Attacks**

#### Descripción

Inserta payloads maliciosos (`' OR 1=1 --`) en parámetros de entrada para manipular bases de datos (SQLi), directorios (Path Traversal), o inyectar comandos (RCE).

***

### 8. **Security Misconfiguration**

#### Descripción

Accede a configuraciones por defecto o endpoints no protegidos (`/swagger-ui.html`, `/debug`). Busca claves hardcodeadas o configuraciones de desarrollo activas.

***

### 9. **Cross-Origin Resource Sharing (CORS) Misconfiguration**

#### Descripción

Prueba solicitudes CORS desde dominios no autorizados. Si el servidor responde con datos sensibles, explota esta debilidad para extraer información desde otro origen.

***

### 10. **Server-Side Request Forgery (SSRF)**

#### Descripción

Envía URLs internas como `http://169.254.169.254/latest/meta-data/` en campos de entrada. Si la API realiza solicitudes hacia estas direcciones, puedes extraer información interna.

***

### 11. **Weak Authentication Mechanisms**

#### Descripción

Fuerza el mecanismo de autenticación probando contraseñas débiles, tokens predecibles o reutilización de sesiones mediante herramientas como Burp o Hydra.

***

### 12. **Improper Assets Management**

#### Descripción

Descubre subdominios, rutas o endpoints olvidados utilizando fuzzing o herramientas como dirsearch. Busca APIs antiguas o sin mantenimiento.

***

### 13. **Weak Encryption in API Responses**

#### Descripción

Intercepta respuestas HTTPS con herramientas como Wireshark para identificar configuraciones de cifrado débiles o datos sensibles transmitidos en texto plano.

***

### 14. **API Key Leakage**

#### Descripción

Busca claves API en repositorios públicos, variables de entorno expuestas o headers de las respuestas. Úsalas para realizar solicitudes en nombre de usuarios legítimos.

***

### 15. **Improper Input Validation**

#### Descripción

Envía datos malformados o excesivamente grandes a parámetros (`' or sleep(5)#`). Busca comportamientos anómalos como demoras, errores o respuestas inconsistentes.

***
