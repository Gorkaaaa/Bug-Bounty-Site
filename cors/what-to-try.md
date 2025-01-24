# ♥️ What to try?

## Mini-Tutoriales Ofensivos sobre Vulnerabilidades de CORS (Enfocado en Bug Bounty)

### 1. \*\*CORS Misconfiguration con Access-Control-Allow-Origin: \*\*\*

#### Descripción

Verifica si el servidor responde con `Access-Control-Allow-Origin: *`, permitiendo que cualquier dominio acceda a los recursos de la API.

***

### 2. **Explotación con Access-Control-Allow-Credentials**

#### Descripción

Prueba si el servidor permite `Access-Control-Allow-Credentials: true` en combinación con un origen no confiable, lo que permite acceso a cookies y datos sensibles.

***

### 3. **Bypass con Subdominios No Autorizados**

#### Descripción

Prueba orígenes como `http://attacker.example.com` para verificar si el servidor acepta cualquier subdominio relacionado.

***

### 4. **CORS Reflection con Orígenes Arbitrarios**

#### Descripción

Envía solicitudes con un header `Origin` modificado para observar si el servidor refleja este valor en el header `Access-Control-Allow-Origin`.

***

### 5. **CORS con Wildcards y Credenciales**

#### Descripción

Identifica si el servidor utiliza wildcards en `Access-Control-Allow-Origin` mientras permite credenciales, lo que puede exponer datos sensibles.

***

### 6. **Inyección en Headers CORS**

#### Descripción

Manipula headers como `Origin` o `Referer` para incluir payloads maliciosos. Verifica si el servidor procesa y devuelve estos valores.

***

### 7. **Uso de Métodos HTTP Ampliados**

#### Descripción

Prueba métodos menos comunes como `PUT`, `DELETE` o `TRACE` en combinación con CORS para acceder o modificar recursos sensibles.

***

### 8. **Exploración de Recursos con Prefetch Requests**

#### Descripción

Envía solicitudes `OPTIONS` a diferentes endpoints para mapear qué recursos están accesibles mediante CORS.

***

### 9. **CORS con Multiple Origin Headers**

#### Descripción

Prueba si el servidor permite múltiples orígenes en los headers `Access-Control-Allow-Origin`, lo que puede exponer datos a orígenes no confiables.

***

### 10. **Bypass mediante Null Origin**

#### Descripción

Envía solicitudes con `Origin: null` para comprobar si el servidor permite este origen, lo que puede ser explotado desde archivos locales.

***

### 11. **CORS Policy Leakage**

#### Descripción

Utiliza orígenes conocidos o herramientas de fuzzing para descubrir configuraciones de CORS que expongan información sensible.

***

### 12. **Acceso Cruzado a Recursos Sensibles**

#### Descripción

Prueba recursos privados (como `/admin` o `/internal`) desde un dominio externo para verificar si CORS permite acceso no autorizado.

***

### 13. **Validación Inadecuada de Orígenes Dinámicos**

#### Descripción

Prueba orígenes malformados o que contengan caracteres especiales para verificar si el servidor los acepta y los incluye en las respuestas CORS.

***

### 14. **Cache Poisoning con CORS**

#### Descripción

Combina headers CORS con ataques de cache poisoning para almacenar respuestas maliciosas en el navegador de los usuarios.

***

### 15. **CORS en APIs REST Públicas**

#### Descripción

Verifica si una API REST pública permite solicitudes de lectura y escritura desde orígenes no confiables, lo que puede ser explotado para ataques CSRF.

***
