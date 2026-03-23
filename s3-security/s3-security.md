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

### Glacier Vault Lock y S3 Object Lock

**S3 Glacier Vault Lock**
- Adopta un modelo WORM (Write Once Read Many)
- Crea una politica de bloqueo de boveda
- Bloquea la politica para futuras ediciones (no se puede modificar ni borrar)
- Util para el cumplimiento de la normativa y la retencion de datos

**S3 Object Lock**
En este tipo de locking el versionado debe estar activado.
- Adopta un modelo WORM
- Bloquea el borrado de una version del objeto durante un tiempo determinado
- Modo de retencion - normativa:
	- Las versiones de los objetos no pueden ser sobrescritas ni borradas por ningun usuario, incluido el usuario root.
	- Los modos de retencion de los objetos no pueden cambiarse, y los periodos de retencion no pueden acotarse.
- Modo de retencion - gobernanza:
	- La mayoria de los usuarios no pueden sobrescribir o eliminar una version de un objeto ni alterar su configuracion de bloqueo.
	- Algunos usuarios tienen permisos especiales para cambiar la retencion o eliminar el objeto.
- Periodo de retencion: protege al objeto durante un periodo fijo, que puede ser ampliado.
- Retencion legal:
	- Protege el objeto indefinidamente, independientemente del periodod e retencion.
	- Puede colocarse y eliminarse libremente mediante el permiso IAM s3:PutObjectLegalHold

### Puntos de acceso S3 y Objeto Lambda

**Puntos de acceso**
- Cada punto de acceso tiene su propio DNS y politica para limitar quien puede acceder a el
	- Un usuario / grupo IAM especifico
	- Una politica por Punto de Acceso => Mas facil de gestionar que las complejas politicas de bucket
![[Pasted image 20260323174203.png]]

**Objeto S3 Lambda**
- Utiliza las funciones Lambda de AWS para modificar el objeto antes de que lo recupere la aplicacion que lo llama
- Solo se necesita de un bucket S3, sobre el que creamos puntos de acceso de S3 y puntos de acceso de S3 object Lambda
- Casos de uso:
	- Redactar informacion de identificacion personal para entornos de analisis o de no produccion.
	- Convertir entre formatos de datos, como convertir de XML a JSON.
	- Redimensionar y poner marcas de agua a las imagenes sobre la marcha utilizando detalles especificos de la persona que llama, como el usuario que solicito el objeto.
![[Pasted image 20260323175045.png]]
### Quiz

Question 1:
Tu cliente quiere asegurarse de que el cifrado de los archivos se realiza en S3, pero quiere gestionar completamente las claves de cifrado y no almacenarlas nunca en AWS. Le recomiendas que utilice ............................
- SSE-C (El cifrado se produce en AWS pero tenes control total de las claves)

Question 2:
Una empresa para la que trabajas quiere que sus datos almacenados en S3 estén cifrados. No les importa que las claves de cifrado sean almacenadas y gestionadas por AWS, pero quieren mantener el control sobre la política de rotación de las claves de cifrado. Les recomiendas que utilicen ....................
- KMS, ya que el cifrado se produce en AWS, las claves son gestionadas por AWS, pero el usuario tiene control total sobre la politica de rotacion de claves de cifrado, las cuales se almacenan en AWS

Question 3:
Tu empresa no confía en AWS para el proceso de cifrado y quiere que éste se realice en la aplicación. Les recomiendas que utilicen ....................
- Encriptacion del lado del cliente, ya que le permite gestionar el encriptado y decriptado total.

Question 4:
Tienes un sitio web que carga archivos desde un bucket de S3. Cuando pruebas la URL de los archivos directamente en tu navegador Chrome funciona, pero cuando el sitio web que visitas intenta cargar estos archivos no lo hace. ¿Cuál es el problema?
- Problemas de CORS (Cross Origin Resources Sharing)

Question 5:
Una empresa de comercio electrónico tiene los datos de sus clientes y pedidos almacenados en un bucket de S3. El director general de la empresa quiere generar un informe que muestre la lista de clientes y los ingresos de cada uno de ellos. Los datos de los clientes almacenados en los archivos del bucket de S3 tienen información sensible que no queremos exponer en el informe. ¿Cómo recomiendas que se cree el informe sin exponer la información sensible?
- Hacer uso de S3 Object Lambda y modificar los objetos antes de que estos sean recuperados por la aplicacion generadora de informes.

Question 6:
Sospechas que algunos de tus empleados intentan acceder a archivos en un bucket de S3 al que no tienen acceso. ¿Cómo puedes verificar que esto es así sin que se den cuenta?
- Habilitar los S3 access logs y analizarlos con athena

Question 7:
Quieres proporcionar URLs temporales a una lista creciente de usuarios federados para permitirles realizar una carga de archivos en tu bucket de S3 a una ubicación específica. ¿Qué deberías utilizar?
- Pre-signed URLs

Question 8:
Por razones de normativa, tu empresa tiene el mandato de que las copias de seguridad de las bases de datos deben conservarse durante 4 años. No debería ser posible borrarlas. ¿Qué recomiendas?
- Bovedas de Glacier (vault locks) con politicas de bloqueo de bovedas

Question 9:
Te gustaría que todos tus archivos en un bucket de S3 estuvieran encriptados por defecto. ¿Cuál es la forma óptima de conseguirlo?
- Habilitar el cifrado por defecto

Question 10:
Has habilitado el control de versiones y quieres ser muy cuidadoso a la hora de borrar archivos en un bucket de S3. ¿Qué deberías habilitar para evitar los borrados permanentes accidentales?
- Habilitar el borrado MFA

Question 11:
Una empresa tiene sus datos y archivos almacenados en algunos buckets de S3. Algunos de estos archivos deben conservarse durante un periodo de tiempo predefinido y estar protegidos para que no se sobrescriban ni se eliminen según la normativa de la empresa. ¿Qué función de S3 te ayuda a hacer esto?
- S3 Object Lock - Modo de cumplimiento de la normativa de retencion

Question 12:
¿Cuál de las siguientes configuraciones de Bloqueo de Objetos de S3 (S3 Object Lock) te permite impedir que un objeto o sus versiones se sobrescriban o eliminen indefinidamente y te da la posibilidad de eliminarlo manualmente?
- Modo de retencion legal

FALLE EN:
- La question 12, pense que era una politica (aunque asi sea se considera un modo tambien)
- La question 2, Server Side Encryption with Key Manager Service es el que maneja las claves y encriptado permitiendote de igual forma gestionar la politica de rotacion de las claves
- La question 1, Server Side Encryption Client, el usuario gestiona sus claves pero le cede el cifrado a AWS