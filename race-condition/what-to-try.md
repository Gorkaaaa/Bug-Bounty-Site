# 🦗 What to try?

## Mini-Tutoriales Ofensivos sobre Vulnerabilidades de Race Conditions (Enfocado en Bug Bounty)

### 1. **Condiciones de Carrera en Actualización de Saldo**

#### Descripción

Envía múltiples solicitudes simultáneas para realizar transacciones financieras. Verifica si puedes actualizar un saldo más de una vez antes de que el sistema lo bloquee.

***

### 2. **Duplicación de Tokens de Promoción**

#### Descripción

Envía varias solicitudes usando el mismo código promocional para ver si puedes redimirlo más veces de lo permitido.

***

### 3. **Compra de Productos con Inventario Limitado**

#### Descripción

Realiza solicitudes simultáneas para adquirir productos con inventario limitado. Verifica si puedes adquirir más unidades de las permitidas.

***

### 4. **Ejecución Paralela en Puntos de Lealtad**

#### Descripción

Haz múltiples solicitudes concurrentes para redimir puntos de lealtad y verifica si puedes gastar más puntos de los que tienes.

***

### 5. **Transferencias Duplicadas**

#### Descripción

Envía varias solicitudes simultáneas de transferencia de dinero para ver si el saldo del remitente se valida correctamente antes de cada operación.

***

### 6. **Cambios en Configuraciones de Usuario**

#### Descripción

Realiza solicitudes paralelas para modificar configuraciones de usuario. Verifica si puedes establecer valores no permitidos debido a inconsistencias en la validación.

***

### 7. **Actualización de Contraseñas**

#### Descripción

Prueba enviar solicitudes simultáneas para cambiar la contraseña y verifica si puedes desincronizar la autenticación del usuario.

***

### 8. **Subida de Archivos Simultánea**

#### Descripción

Envía múltiples solicitudes de subida de archivos para ver si puedes sobreescribir archivos existentes o crear inconsistencias en el almacenamiento.

***

### 9. **Bypass en Pagos Parciales**

#### Descripción

Divide un pago en partes pequeñas y envía solicitudes concurrentes. Verifica si puedes completar el pago sin cubrir el monto total.

***

### 10. **Saltos en Procesos de Aprobación**

#### Descripción

Envía solicitudes paralelas para eludir pasos intermedios en un flujo de aprobación, como en sistemas de registro o pagos.

***

### 11. **Asignación Duplicada de Recursos**

#### Descripción

Envía solicitudes simultáneas para asignar un recurso limitado, como habitaciones de hotel o boletos, y verifica si puedes obtener más de lo permitido.

***

### 12. **Creación de Cuentas Duplicadas**

#### Descripción

Envía solicitudes concurrentes para registrar una cuenta con el mismo correo electrónico y verifica si puedes crear múltiples cuentas.

***

### 13. **Aumentar Roles de Usuario**

#### Descripción

Envía solicitudes simultáneas para cambiar el rol de usuario y verifica si puedes obtener privilegios más altos sin autorización adecuada.

***

### 14. **Actualización de Inventario**

#### Descripción

Envía solicitudes simultáneas para agregar o retirar inventario. Verifica si puedes crear inconsistencias entre los registros y el estado real.

***

### 15. **Ejecución Paralela en Descuentos**

#### Descripción

Aplica un descuento y realiza múltiples solicitudes para ver si puedes aplicar el descuento varias veces en una misma transacción.

***
