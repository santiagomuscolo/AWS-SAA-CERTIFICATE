## Cifrado 101

**Cifrado en vuelo**
El cifrado en vuelo es una tecnica criptografica realizada mediante el protocolo de cifrado SSL (Socket Layer Security) o TLS (Transport Layer Security) con la finalidad de evitar un ataque MITM (Man in the middle) que pueda comprometer nuestros datos.
![[Pasted image 20260507164425.png]]

**Cifrado en reposo**
La realidad es que no basta con encriptar en vuelo los datos enviados en la request sino que se necesita de una capa mas de seguridad para nuestros datos una vez que estos llegan al servidor, esto es denominado **cifrado en reposo**, una vez que el server desencripta los datos cifrados con ssl los vuelve a cifrar para guardarlos mediante una clave a la que el server siempre tiene acceso para decifrar antes de volverlos a brindar

**Cifrado del lado del cliente**
Esta tecnica es la contraparte del cifrado en reposo, mientras el nombrado previamente caeria en manos de AWS cuando nosotros ciframos/deciframos del lado del cliente los datos se guardan y se envian siempre encriptados quitandole a AWS toda responsabilidad en la gestion de las llaves.
![[Pasted image 20260507165104.png]]
## KMS (Servicio de administracion de claves)
KMS es el servicio clave para la gestion de claves cifradas en AWS, permitiendo diversos puntos de conexion como puede ser integracion con IAM para autenticacion, Integracion con otros servicios de AWS para alojar claves cifradas (e.g EBS, S3, RDS, SSM) y lo mejor es que es absolutamente auditable el uso de estas claves mediante CloudTrail.

**Tipos de claves**
- Clave simetrica (AES-256)
	este tipo de clave utiliza la misma llave para cifrar y descifrar, desde AWS nunca tenemos acceso a la clave KMS sin cifrar debe si o si llamarse a la API KMS para obtenerla.
- Clave asimetrica (par de claves RSA y ECC)
	Este tipo de clave utiliza una llave para el cifrado y otra para el descifrado, mientras que la llave de cifrado es publica y  obtenible la clave de descifrado es privada y no puede accederse sin cifrar

**AWS KMS**
En KMS existen 3 tipos de claves:
- Clave administrada por AWS - gratuita.
- Clave gestionada por el cliente (CMK) - $1 dolar por clave /mes
- Clave gestionada por el cliente importada (si o si claves simetricas de 256 bits) - $1 dolar por clave /mes
Es importante saber que la api de KMS tiene un coste de $0,03 USD x 1000 llamadas.
Un punto de gran valor es que AWS KMS rota las claves para cada uno de los casos de forma automatica:
- Clave administrada por AWS - rota automaticamente cada 1 ano
- Clave gestionada por el cliente (debe estar activada): rota automaticamente cada 1 ano.
- Clave gestionada por el cliente importada: solo es posible la rotacion manual mediante alias

**Copiar snapshots entre regiones**
Es totalmente posible pero no es tan sencillo, la copia y utilizacion de volumenes cifrados (ejemplo) en una region diferente implica la re-encriptacion de los mismos con una clave KMS diferente.
![[Pasted image 20260507170834.png]]

**Politicas clave KMS**
Al igual que las politicas del bucket S3 tenemos las politicas de las claves KMS, la diferencia radica en que no podemos controlar el acceso sin ellas.

- Politica de claves por defecto:
	KMS crea una politica de claves por defecto asignandole el acceso completo a la clave al usuario root = cuenta de AWS
- Politicas personalizadas:
  Permite definir usuarios y roles con acceso a la clave KMS, Define quien puede administrar la clave y es util para el acceso entre cuentas de tu clave KMS.

**Copiar snapshots entre cuentas**
Esto es viable mediante una politica personalizada, como bien se mostro en la copia entre regiones el snapshot se re-encripta pero para eso primero debe desencriptarse para hacer la copia del mismo, en base a esto es donde entran en juego las politicas KMS personalizadas
![[Pasted image 20260507171959.png]]
En la politica mostrada somos absolutamente estrictos con el usuario que tiene acceso a esa clave y que puede decriptar en este caso un volumen EBS via EC2.
El proceso seria algo asi:
- Usuario crea snapshot cifrada con su propia clave KMS (gestionada por el cliente)
- Usuario adjunta politica KMS para permitir el acceso a la misma entre cuentas
- Usuario comparte la snapshot cifrada
- Usuario destino descifra el snapshot compartido, lo copia y lo cifra con su propia clave KMS
- Usuario destino crea su propio volumen EBS a partir de la copia cifrada que creo.

## Claves multi region KMS
Las claves multiregion son claves KMS identicas en diferentes regiones de AWS que pueden utilizarse indistintamente, estas poseen el mismo ID de clave, rotacion automatica, etc...
Las mismas no son globales - Primario + replicas y cada una se gestiona de forma independiente.
**Caso de uso**: Cifrado global del lado del cliente, cifrado en DynamoDB Global, Aurora Global.

**Caso de uso - cifrado de tablas globales de dynamoDB con claves multiregion KMS**
![[Pasted image 20260508191113.png]]

**Caso de uso - Cifrado del lado del cliente de las claves globales Aurora y KMS multiregion**
![[Pasted image 20260508191400.png]]

## Replicacion S3 con encriptacion
En S3 poseemos 3 tipos de cifrado SSE-S3, SSE-C y SSE-KMS, estos presentan diveresas caracteristicas de cara al cifrado frente a la replicacion.
- SSE-S3 replica objetos cifrados y no cifrados por defecto.
- SSE-C no replica objetos cifrados
- SSE-KMS presenta la opcion de replicacion de objetos cifrados 
	- Se debe aclarar que clave KMS se utilizara para cifrar los objetos dentro del bucket de destino
	- Adaptar la politica de claves KMS para la clave de destino
	- Un rol IAM kms:Encrypt para la clave KMS de origen y kms:Decrypt para la clave KMS de destino.
- Las claves multiregion son utilizables pero S3 las trata como claves independientes.

## Proceso de comparticion de AMI encriptada
Las AMIs de origen suelen estar encriptadas en la cuenta del usuario gestionadas mediante claves KMS, sin embargo, estas pueden compartirse por medio de lo que se llama "permiso de lanzamiento" este es configurado para agregar el id de la cuenta destino a la que se compartira dicha AMI encriptada por lo que la cuenta destino requerira de algunos roles IAM como lo son DescribeKey, ReEncrypted, CreateGrant... como bien fue nombrado con anterioridad la AMI es cifrada con una clave KMS por lo que al compartir dicha ami tambien en paralelo debemos compartir la clave misma (mediante politica de clave) para que la cuenta destino pueda trabajar sobre la misma y asi lanzar su instancia EC2 en su propia cuenta para cifrar sus volumenes.
![[Pasted image 20260511110145.png]]
## SSM Parameter Store
Es un servicio para el almacenamiento seguro de la configuracion y los secretos que ofrece un cifrado sin fisuras mediante la utilizacion de SSM y la comprobacion de identidad con IAM.
![[Pasted image 20260511110535.png]]

**Jerarquia del almacen**
Es un servicio cuyo registro de secretos es manejado por directorios y subdirectorios accesibles mediante SDK, o Lambda (llamando a la API)

**precios**
![[Pasted image 20260511110916.png]]

**Politicas de parametros**
- TTL a parametros para forzar su actualizacion o eliminacion 
- Asignacion de politicas en simultaneo
- ![[Pasted image 20260511111140.png]]

## AWS Secrets Manager
Es un servicio que esta destinado a guardar unicamente secretos con el beneficio de que estos tienen la capacidad de forzar su rotacion cada x dias.
- Permite rotar secretos automaticamente mdiante lambda
- posee integracion con RDS
- Los secretos son cifrables mediante KMS

**soporte multiregion**
Los secretos son replicables entre regiones permitiendo el uso multi-region de los secrets, esto sirve para recuperacion de desastres, bases de datos multi region, etc...

## AWS certificate manager
Certificate manager es un servicio que permite aprovisionar, gestionar y desplegar facilmente certificados TLS/SSL, el mismo maneja soporte para certificados TLS publicos y gratuitos, renovacion de los mismos de forma automatica (solo si son creados en ACM si son importados no) y soporte con: ELB, API gateway, Distribuciones de cloudfront, EC2 no es compatible...

## WAF
WAF es un servicio de firewall para proteger a las aplicaciones de los diversos exploits de capa 7 que existen, el mismo puede desplegarse en: ALB, Gateway API, Cloudfront, API GraphQL de AppSync y Grupo de usuarios cognito.

**reglas**
WAF nos permite definir una serie de reglas para la proteccion de nuestra web denominadas reglas ACL (access control list):
- Conjunto de IP hasta 10.000 direcciones IP
- Bloqueo por geo-match
- Cabeceras HTTP, body HTTP, String URI para proteger de inyecciones sql y XSS
- Reglas basadas en tasa para evitar DDoS

Estas reglas son regionales exceptuando Cloudfront donde se aplican a nivel global. Ademas son definibles por grupos para poder re-utilizarlas.

**problematica: IP fija al usar WAF con un ALB**
Aca la problematica nace en que un ALB no puede usar una IP fija por defecto, siendo el caso AWS nos ofrece AWS global accelerator para obtenerla.
![[Pasted image 20260515111252.png]]