### Cifrado S3
Los buckets pueden cifrarse utilizando uno de los 4 metodos siguientes

**Cifrado del lado del servidor (SSE)**
- SSE con claves gestionadas por amazon s3 (SSE-S3)
- SSE con claves KMS almacenadas en AWS KMS (SSE-KMS)
- SSE con claves proporcionadas por el cliente (SSE-C)

**Cifrado del lado del cliente**

#### SSE-S3
- Cifrado mediante claves manejadas, gestionadas y propiedad de AWS
- El objeto se cifra del lado del servidor
- El tipo de cifrado utilizado es AES-256
- Debe establecerse la cabecera "x-amz-server-side-encryption": "AES256"


#### SSE-KMS
- Cifrado mediante claves manejadas y gestionadas por AWS KMS (Key Management Service)
- Ventajas del KMS: control de usuario + auditoria del uso de las claves mediante CloudTrail
- El objeto se cifra en el servidor
- Debe establecerse la cabecera "x-amz-server-side-encryption": "aws:kms"
- Cuando se sube un archivo, se llama a la API KMS GenerateDataKey
- Cuando se descarga un archivo, se llama a la API KMS Decrypt
- Cuenta para la cuota KMS por segundo (5500, 10000, 30000 req/s segun la region)

#### SSE-C
- Cifrado del lado del servidor mediante claves totalmente gestionadas por el cliente fuera de AWS
- Amazon S3 NO almacena la clave de cifrado que proporciones
- Se tiene que utilizar HTTPS
- La clave de cifrado debe proporcionarse en las cabeceras HTTP, para cada peticion HTTP realizada

#### Cifrado del lado del cliente
- Utilizar bibliotecas de clientes como la biblioteca de cifrado del lado del cliente de Amazon S3
- Los clientes deben cifrar los datos ellos mismos antes de enviarlos a Amazon S3
- Los clientes deben decifrar los datos ellos mismos al recuperarlos de Amazon S3
- El cliente gestiona completamente las claves y el ciclo de cifrado.

#### Cifrado en transito (SSL/TLS)
- Amazon S3 expone dos endpoints:
	- HTTP endpoint - no cifrado
	- HTTPS endpoint - cifrado
- Se recomienda HTTPS
- HTTPS es obligatorio para SSE-C
- La mayoria de los clientes usarian el endpoint HTTPS por defecto

### Cifrado por defecto vs politicas de buckets
- Una forma de "forzar el cifrado" es utilizar una politica de bucket y rechazar cualquier llamada a la API para PUT de un objeto S3 sin cabeceras de cifrado
![[Pasted image 20260319210503.png]]

- Otra forma mas inteligente y sencilla es habilitar la opcion de "cifrado por defecto" de S3 que independientemente de la cabecera cifrara los objetos subidos.
- NOTA: las politicas de los buckets se evaluan antes del "cifrado por defecto"

### S3 CORS
El Cross Origin Resources Sharin (CORS) hace refetencia a "compartir recursos entre origenes".
- Origen = esquema (protocolo) + host (dominio) + puerto
	- Ejemplo: https://www.example.com (el puerto implicito para HTTPS es 443, o 80 para HTTP)
- Mecanismo basado en el navegador web para permitir peticiones a otros origenes mientras se visita el origen principal
- El mismo origen seria -> http://example.com y http://example.com/app2
- Diferentes orgines serian -> http://example.com y http://www.other.example.com
- Las peticiones no se cumpliran a menos que el otro origen permita las peticiones, utilizando cabeceras CORS (ejemplo: Access-Control-Allow-Origin)

**S3 CORS
- Si un cliente hace una peticion de origen cruzado en nuestro bucket de S3, tenemos que habilitar las cabeceras CORS correctas.
- Pueder permitir un origen especifico o * (todos los origenes)

### S3 MFA Delete
- MFA (Autenticacion de factores multiples): obliga a los usuarios a generar un codigo en un dispositivo (normalmente un telefono movil o un hardware) antes de realizar operaciones importantes en el S3
- MFA sera necesario para:
	- Eliminar permanentemente una version de un objeto
	- Suspender el control de versiones en el bucket
- MFA no sera necesario para:
	- Habilitar el control de versiones
	- Listar las versiones eliminadas
- Para utilizar MFA Delete, el control de versiones debe estar activado en el bucket
- Solo el propietario del bucket (cuenta root) puede activar/desactivar MFA Delete

### Logs de acceso al S3
- Para fines de auditoria, es posible que quieras registrar todos los accesos a los buckets de S3
- Cualquier peticion realizada a S3, desde cualquier cuenta, autorizada o denegada, se registrara dentro de otro bucket S3
- Esos datos pueden ser analizados con herramientas de analisis de datos...
- El bucket de logs de destino debe estar en la misma region de AWS

**Warning**
- No configures tu bucket de logs para que sea un bucket monitorizado
- Se creara un bucle de logs, y tu bucket crecera exponencialmente.

### S3 - URLs pre-firmadas
- Generar URLs pre-firmadas usando la consola de S3, la CLI de AWS o el SDK
- Expiracion de la URL
	- Consola S3 - de 1 minuto a 720 minutos (12 horas)
	- CLI de AWS - configurar la caducidad con el parametro --expires-in en segundos (por defecto 3600 segs, max 604800 segs - 168 horas)
- Los usuarios a los que se les de una URL pre-firmada heredan los permisos del usuario que genero la URL para GET / PUT
- Ejemplos:
	- Permite que solo los usuarios que han iniciado sesion descarguen un video premium de tu bucket de S3
	- Permitir que una lista de usuarios cambiantes descargue archivos generando URLs dinamicamente
	- Permitir temporalmente que un usuario suba un archivo a una ubicacion precisa de tu buket S3
