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

## AWS shield - proteccion contra ataques DDoS

**Que es DDoS?**
Distributed denegation of services es un ataque cibernetico destinado a evitar servir nuestra servicio mediante la ejecucion de multiples peticiones desde multiples maquinas diferentes.

**Planes**
- AWS Shield Estandar: 
	- gratuito 
	- Proporciona proteccion contra ataques SYN/UDP Floods, ataques de reflexion y otros ataques de capa de 3/4
- AWS Shield Avanzado:
	- Servicio opcional de mitigacion de DDoS (3000 USD mensuales por org)
	- Protege contra ataques mas sofisticados en EC2, ELB, CloudFront, AWS Global Accelerator y Route 53
	- Acceso 24/7 al equipo de respuesta de DDoS de AWS 
	- Crea una mitigacion automatica de DDoS en la capa de aplicacion, evalua y despliega automaticamente reglas WAF para mitigar los ataques de capa 7

## Firewall Manager
Es un servicio que sirve para gestionar reglas en todas las cuentas de una organizacion de AWS

**Grupos de politicas de seguridad (regionales)**
- Reglas WAF (ALB, API Gateway, CloudFront)
- AWS Shield avanzado (ALB, CLB, NLB, Elastic IP, CloudFront)
- Grupos de seguridad para EC2, ALB y recursos ENI en VPC
- AWS network firewall (nivel VPC)
- Resolver firewall DNS de route 53

## Mejores practicas contra DDoS
![[Pasted image 20260519202734.png]]

- BP1 - cloudfront
	- Entrega de aplicaciones web en el borde
	- Protege de los ataques DDoS comunes (inundaciones SYN...)
- BP1 - global accelerator
	- Accede a tu aplicacion desde el edge
	- integracion con shield
	- Util si tu backend no es compatible con cloudfront
- BP3 - route 53
	- Resolucion de nombres de dominio en el borde
	- Mecanismo de proteccion DDoS
- Defensa de la capa de infraestructura (BP1, BP3, BP6)
	- Protege Amazon EC2 contra el trafico elevado
	- Esto incluye el uso de Global accelerator, route 53, cloudfront, elastic load balancing.
- Amazon EC2 con autoescalado BP7
	- Ayuda a escalar en caso de aumentos repentinos de trafico, incluyendo una multitud repentina o ataques DDoS
- Elastic Load Balancing BP6
	- Elastic load balancing escala con aumentos de trafico y distribuira el trafico a muchas instancias EC2
- Detecta y filtra peticiones web maliciosas (BP1, BP2)
	- Cloudfront cachea el contenido estatico y lo sirve en edge locations
	- AWS WAF se utiliza sobre cloudfront y ALB para filtrar y bloquear peticiones basadas en firmas de peticiones
	- Las reglas basadas en la tasa de WAF pueden blouqear automaticamente las IP de los malos actores
	- Utiliza reglas gestionadas en WAF para bloquear ataques basados en la reputacion de la IP, o bloquear IPs anonimas
	- CloudFront puede bloquear geografias especificas
- Shield Avanzado (BP1, BP2, BP6)
	- La mitigacion automatica de DDoS en la capa de aplicacion de shield advanced crea, evalua y despliega automaticamente reglas WAF de AWS para mitigar ataques de capa 7
- Ofuscar recursos de AWS BP1, 4 y 6
	- Uso de cloudfront, APi gateway, elastic load balancing para ocultar tus recursos de backend (funciones lambda, instancias EC2)
- Grupos de seguridad y ACLs de red BP5
	- Utiliza grupos de seguridad y NACLs para filtrar el trafico basado en IP especificas a nivel de subred o ENI
	- Las IP elasticas estan protegidas por shield advanced
- Proteger los endpoints API BP4
	- Ocultar EC2, Lambda, en otro lugar
	- Modo optimizado para edge, o cloudfront + modor egional (mas control sobre DDoS)
	- WAF + API gateway: limite de rafagas, filtrado de cabeceras, uso de claves API

## GuardDuty
GuardDuty es un servicio de descubrimiento inteligente de amenazas que utiliza machine learning (deteccion de anomalias y datos de terceros) para proteger la cuenta de AWS.

## Amazon inspector
Es un servicio que realiza evaluaciones de seguridad automatizadas para:
- Instancias ec2
- Imagenes enviadas a ECR (Elastic Container Registry)
- Funciones Lambda

Posee integracion con AWS security hub y eventBridge.
Este servicio funciona por una puntuacion a todas las vulnerabilidades encontradas para priorizarlas.

## Macie
Es un servicio de seguridad y privacidad de los datos totalmente gestionado que utiliza ML y la concordancia de patrones para descubrir y proteger tus datos sensibles en AWS, el mismo ayuda a identificar y alertar sobre datos sensibles, como la informacion personal identificable (PII)

## Quiz
Question 1:

Para activar el cifrado en vuelo (cifrado en tránsito), necesitamos tener ........................
- Un endpoint HTTPS con cifrado en vuelo

Question 2:

El cifrado del lado del servidor significa que los datos se envían cifrados al servidor.
- falso

Question 3:

En el cifrado del lado del servidor, ¿dónde se produce el cifrado y el descifrado?
- Tanto el cifrado como el descifrado ocurren en el servidor

Question 4:

En el cifrado del lado del cliente, el servidor debe conocer nuestro esquema de cifrado antes de que podamos cargar los datos.
- falso

Question 5:

Tienes que crear claves KMS en AWS KMS antes de poder utilizar las funciones de cifrado para EBS, S3, RDS...
- falso

Question 6:

AWS KMS soporta claves KMS tanto simétricas como asimétricas.
- verdadero

Question 7:

Cuando activas la Rotación Automática en tu Clave KMS, la clave de respaldo se rota cada .................
- 1 ano

Question 8:

Tienes una AMI que tiene una Snapshot de EBS encriptada mediante KMS CMK. Quieres compartir esta AMI con otra cuenta de AWS. Has compartido la AMI con la cuenta de AWS deseada, pero la otra cuenta de AWS sigue sin poder utilizarla. ¿Cómo resolverías este problema?
- se tiene que compartir la CMK de KMS utilizada para cifrar la AMI con la otra cuenta de AWS

Question 9:

Has creado una CMK gestionada por el cliente en KMS que utilizas para cifrar tanto los buckets de S3 como las Snapshots de EBS. La política de tu empresa exige que las claves de cifrado se roten cada 3 meses. ¿Qué deberías hacer?
- Rotar la CMK manualmente. Crea una nueva CMK y utiliza Alias de clave para referenciar la nueva CMK de KMS y conservar la antigua para poder decifrar datos antiguos.

Question 10:

¿Qué deberías utilizar para controlar el acceso a tus CMKs de KMS?
- politicas de claves

Question 11:

Tienes una función Lambda que se utiliza para procesar algunos datos en la base de datos. Te gustaría dar a tu función Lambda acceso a la contraseña de la base de datos. ¿Cuál de las siguientes opciones es la más segura?
- Tenerla como una variable de entorno cifrada y descifrar en tiempo de ejecucion

Question 12:

Tienes un valor secreto que utilizas con fines de encriptación, y quieres almacenar y rastrear los valores de este secreto a lo largo del tiempo. ¿Qué servicio de AWS deberías utilizar?
- Almacen de parametros SSM

Question 13:

Tu sitio web de cara al usuario es un objetivo de alto riesgo para los ataques DDoS y te gustaría obtener soporte 24 horas al día, 7 días a la semana, en caso de que se produzcan y el reembolso de la factura de AWS por los costes incurridos durante el ataque. ¿Qué servicio de AWS deberías utilizar?
- Tener shield avanzado

Question 14:

Te gustaría mantener externamente los valores de configuración de tu base de datos principal, para que sean recogidos en tiempo de ejecución por tu aplicación. ¿Cuál es el mejor lugar para almacenarlos para mantener el control y el historial de versiones?
- con un almacen de parametros SSM

Question 15:

AWS GuardDuty analiza las siguientes fuentes de datos, **EXCEPTO** ................
- Cloudwatch logs

Question 16:

Tienes un sitio web alojado en una flota de instancias de EC2, con un Load Balancer de aplicaciones al frente. ¿Qué deberías utilizar para proteger tu sitio web de los ataques comunes a las aplicaciones web (por ejemplo, la inyección SQL)?
- WAF

Question 17:

Te gustaría analizar las vulnerabilidades del sistema operativo desde las instancias EC2. Necesitas que estos análisis se produzcan semanalmente y te proporcionen recomendaciones concretas en caso de que se encuentren vulnerabilidades. ¿Qué servicio de AWS deberías utilizar?
- Inspector

Question 18:

¿Cuál es el servicio de AWS más adecuado para almacenar las contraseñas de las BD de RDS, que además te proporcione una rotación automática?
- AWS secrets manager

Question 19:

¿Qué servicio de AWS te permite gestionar de forma centralizada los Grupos de Seguridad de EC2 y AWS Shield Advanced en todas las cuentas de AWS de tu AWS Organizations?
- AWS Firewall Manager

Question 20:

¿Qué servicio de AWS te ayuda a proteger tus datos sensibles almacenados en buckets S3?
- AWS macie

Question 21:

Una empresa de pagos online utiliza AWS para alojar su infraestructura. El frontend se crea con VueJS y se aloja en un bucket S3 y el backend se desarrolla con PHP y se aloja en instancias EC2 en un Auto Scaling Groups. Como sus clientes están en todo el mundo, utilizan tanto CloudFront como la base de datos Aurora Global para implementar despliegues multirregionales para proporcionar la menor latencia y ofrecer disponibilidad y resiliencia. Se necesita una nueva función que ofrezca a los clientes la posibilidad de almacenar datos cifrados en la base de datos y que estos datos no puedan ser revelados ni siquiera por los administradores de la empresa. Los datos deben estar encriptados en el lado del cliente y almacenados en un formato cifrado. ¿Qué recomiendas para implementar esto?
- Cifrado del lado del cliente con aurora y claves multiregionales de KMS

Question 22:

Tienes un bucket de S3 que está encriptado con SSE-KMS. Se te ha encargado replicar los objetos a un bucket de destino en la misma región de AWS, pero con una clave KMS diferente. Has configurado la replicación de S3, el bucket de destino y la clave KMS de destino y sigue sin funcionar. ¿Qué falta para que funcione la replicación de S3?
- se tiene que configurar los permisos correspondientes para la clave de origen kms:decrypt y la de destino kms:encrypt para que sean utilizadas por el servicio de replicacion S3

Question 23:

Has generado un certificado público utilizando LetsEncrypt y lo has subido al ACM para poder utilizarlo y adjuntarlo a un Application Load Balancer que reenvía el tráfico a las instancias EC2. Como este certificado se genera fuera de AWS, no soporta la función de renovación automática. ¿Cómo podrías recibir una notificación 30 días antes de que este certificado caduque para poder generar uno nuevo manualmente?
- Utilizar eventBridge para notificar a SNS de los eventos de caducidad diarios mediante correo electronico

Question 24:

Has creado la principal API Gateway optimizada para el borde en la región de AWS `us-west-2`. Esta API Gateway principal con optimización de bordes reenvía el tráfico a la API Gateway de segundo nivel en `ap-southeast-1`. Quieres asegurar la API Gateway principal adjuntándole un certificado ACM. ¿En qué región de AWS vas a crear el certificado ACM?
- como el servicio que lidera es cloudfront el mismo solicita que los certificados esten siempre en us-east-1 independientemente del api gateway

Question 25:

Estás gestionando una AWS Organizations con varias cuentas de AWS. Cada cuenta tiene una aplicación independiente con diferentes recursos. Quieres una forma fácil de gestionar los Grupos de Seguridad y las Reglas WAF en todas esas cuentas, ya que hubo un incidente de seguridad la semana pasada y quieres reforzar tus recursos. ¿Qué servicio de AWS puede ayudarte a hacerlo?
- AWS firewall manager