# 🧑‍🦯 What to try?

## Mini-Tutoriales Ofensivos sobre Vulnerabilidades de GraphQL (Enfocado en Bug Bounty)

### 1. **Exposición de Esquema GraphQL**

#### Descripción

Consulta el endpoint `/graphql` con una solicitud introspectiva para descubrir el esquema completo, incluidas queries y mutaciones sensibles.

***

### 2. **Inyección GraphQL**

#### Descripción

Prueba inyectar caracteres especiales como `"` o `{}` dentro de las consultas para alterar el comportamiento del servidor o extraer datos no autorizados.

***

### 3. **Bypass de Autenticación en Mutaciones**

#### Descripción

Intenta ejecutar mutaciones críticas, como `updateUser` o `deletePost`, sin autenticarte o con un token de usuario básico.

***

### 4. **Exceso de Datos Devueltos**

#### Descripción

En consultas, solicita datos adicionales no mostrados en la interfaz (`__typename`, `password`, `token`) para identificar exposición de datos sensibles.

***

### 5. **Ataques de Denegación de Servicio (DoS)**

#### Descripción

Envía consultas recursivas o muy anidadas para consumir recursos del servidor y ralentizar su funcionamiento.

***

### 6. **Manipulación de Entradas en Variables**

#### Descripción

Envía variables malformadas o manipuladas para forzar comportamientos inesperados en el servidor.

***

### 7. **Omisión de Restricciones de Campos**

#### Descripción

Incluye campos adicionales en las consultas y verifica si puedes acceder a información que debería estar restringida.

***

### 8. **Ejecución de Consultas Introspectivas Deshabilitadas**

#### Descripción

Si la introspección está deshabilitada, intenta consultas indirectas para descubrir partes del esquema (`__type`, `__schema`).

***

### 9. **Mutaciones No Documentadas**

#### Descripción

Prueba mutaciones que no estén documentadas, como `deleteAccount` o `createAdminUser`, para identificar funcionalidades ocultas.

***

### 10. **Bypass de Restricciones de Campo**

#### Descripción

Solicita un campo restringido dentro de una consulta normal para verificar si puedes extraer información no autorizada.

***

### 11. **Exposición de Datos en Errores**

#### Descripción

Induce errores en el servidor para capturar mensajes detallados que puedan filtrar estructuras de base de datos o tokens.

***

### 12. **Consultas de Datos Profundamente Relacionados**

#### Descripción

Crea consultas anidadas que exploren múltiples niveles de relaciones de datos (`user -> posts -> comments`) para encontrar fugas de información.

***

### 13. **Operaciones Mutables en Batch**

#### Descripción

Prueba enviar múltiples mutaciones dentro de una sola operación para identificar si puedes realizar acciones masivas no autorizadas.

***

### 14. **Fuzzing en Campos y Operaciones**

#### Descripción

Realiza fuzzing en los campos y nombres de operaciones para descubrir consultas no documentadas o vulnerabilidades en la validación.

***

### 15. **Inyección de Scripts en Respuestas**

#### Descripción

Manipula entradas que son devueltas por el servidor en la respuesta para intentar inyectar scripts o código malicioso.

***
