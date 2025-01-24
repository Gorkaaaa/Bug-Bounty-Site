# 👁️ What to try?

## Mini-Tutoriales Ofensivos sobre Vulnerabilidades de File Upload (Enfocado en Bug Bounty)

### 1. **Subida de Archivos con Contenido Malicioso**

#### Descripción

Sube archivos con extensiones permitidas pero contenido malicioso, como scripts PHP o payloads que exploten vulnerabilidades del servidor.

***

### 2. **Evasión de Restricciones de Extensión**

#### Descripción

Prueba subir archivos con extensiones dobles (`shell.php.jpg`) o extensiones no convencionales (`shell.phar`). Algunos sistemas solo validan la extensión visible.

***

### 3. **Path Traversal mediante Subida de Archivos**

#### Descripción

Manipula el nombre del archivo cargado para incluir secuencias de directorio (`../../shell.php`). Esto puede permitirte escribir en ubicaciones críticas del sistema.

***

### 4. **Bypass de Validación de Tipo MIME**

#### Descripción

Modifica manualmente el tipo MIME del archivo en el encabezado de la solicitud (`Content-Type: image/png`) para evadir filtros basados en MIME.

***

### 5. **Almacenamiento Público de Archivos Subidos**

#### Descripción

Sube un archivo y verifica si puedes acceder directamente al archivo desde una URL pública. Esto podría exponerte a la ejecución de scripts o robo de datos.

***

### 6. **Ejecución de Código en el Servidor**

#### Descripción

Sube un archivo ejecutable (como un `.php`, `.jsp` o `.aspx`) y accede a él directamente. Si el servidor ejecuta el archivo, puedes obtener control total.

***

### 7. **Subida de Archivos de Gran Tamaño**

#### Descripción

Intenta subir archivos excesivamente grandes para saturar el almacenamiento del servidor o causar una denegación de servicio (DoS).

***

### 8. **Inyección de Metadatos en Archivos**

#### Descripción

Manipula metadatos del archivo, como nombres o comentarios, para incluir carga útil que pueda ser interpretada por el servidor o los clientes.

***

### 9. **Desbordamiento de Buffer en Procesamiento de Archivos**

#### Descripción

Sube archivos diseñados para explotar vulnerabilidades en bibliotecas de procesamiento de archivos, como imágenes con datos corruptos.

***

### 10. **Subida de Archivos sin Autenticación**

#### Descripción

Busca endpoints de subida que no requieran autenticación y prueba subir archivos maliciosos o datos confidenciales.

***

### 11. **Ejemplo de Manipulación de Cabeceras de Archivo**

#### Descripción

Sube un archivo con una cabecera manipulada, como un archivo PNG con contenido PHP incrustado. Algunos servidores no verifican la congruencia entre la cabecera y el contenido.

***

### 12. **Uso de Payloads en Archivos Comprimidos**

#### Descripción

Crea un archivo ZIP o RAR que contenga un script malicioso. Algunos sistemas descomprimen automáticamente los archivos sin verificar su contenido.

***

### 13. **Subida de Archivos con Nombres Manipulados**

#### Descripción

Intenta subir archivos con nombres largos o que contengan caracteres especiales (`/`, \`
