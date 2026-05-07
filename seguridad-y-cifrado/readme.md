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