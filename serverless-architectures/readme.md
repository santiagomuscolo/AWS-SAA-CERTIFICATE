## My todo list
- Exponer como API REST con HTTPS
- Arquitectura serverless
- Los usuarios deberian poder interactuar directamente con su propia carpeta S3
- Los usuarios deben autenticarse a traves de un servicio gestionado sin servidor
- Los usuarios pueden escribir y leer to-dos, pero sobre todo leerlos
- La base de datos debe escalar y tener un alto rendimiento de lectura
- ![[Pasted image 20260409132945.png]]

## MyBlog.com
- debe escalar globalmente
- posee gran parte de archivos estaticos y una API REST dinamica
- Usa almacenamiento en cache siempre que sea posible
- cualquier nuevo usuario debe recibir un correo electronico de bienvenida
- cualquier foto subida al blog debe generar una miniatura
1. ![[Pasted image 20260410162221.png]]
2. ![[Pasted image 20260410162228.png]]
3. ![[Pasted image 20260410162234.png]]

## Arquitectura de microservicios
![[Pasted image 20260413165524.png]]

- Patrones sinconos: API Gateway, load balancers.
- Patrones asincronicos: SQS, Kinesis, SNS, Lambda Triggers (S3)
- Desafios:
	- sobrecarga repetida para crear cada nuevo microservicio
	- Problemas con la optimizacion de la densidad/utilizacion del servidor
	- Complejidad de ejecutar varias versiones de varios microservicios simultaneamente
	- Proliferacion de requisitos del codigo del lado del cliente para integrarse con muchos microservicios distintos

## Quiz
Question 1:
Una empresa emergente planea ejecutar su aplicación en AWS. Como arquitecto de soluciones, la empresa te ha contratado para que diseñes e implementes una API REST totalmente sin servidor. ¿Qué stack tecnológico recomiendas?
- API gateway + AWS Lambda

Question 2:
Los siguientes servicios de AWS tienen una función de almacenamiento en caché de serie, EXCEPTO .................
- Lambda

Question 3:
Estás ejecutando una aplicación móvil en la que quieres que cada usuario registrado suba/descargue imágenes a/desde su propia carpeta en el bucket de S3. Además, quieres que tus usuarios se registren e inicien sesión utilizando sus cuentas de redes sociales (por ejemplo, Facebook). ¿Qué servicio de AWS debes elegir?
- Amazon cognito

Question 4:
Tienes muchos archivos estáticos almacenados en un bucket de S3 que quieres distribuir globalmente a tus usuarios. ¿Qué servicio de AWS debes utilizar?
- Amazon Cloudfront

Question 5:
Has creado una tabla DynamoDB en `ap-northeast-1` y quieres que esté disponible en `eu-west-1`, por lo que has decidido crear una tabla global DynamoDB. ¿Qué hay que habilitar primero antes de crear una Tabla Global de DynamoDB?
- DynamoDB Streams

Question 6:
Has configurado una función Lambda para que se ejecute cada vez que se añada un elemento a una tabla de DynamoDB mediante DynamoDB Streams. La función está destinada a insertar mensajes en la cola de SQS para posteriores trabajos de procesamiento largo. Cada vez que se invoca la función Lambda, parece que es capaz de leer del Stream de DynamoDB, pero no es capaz de insertar los mensajes en la cola SQS. ¿Cuál crees que es el problema?
- Al rol IAM de ejecucion Lambda le faltan permisos

Question 7:
Te gustaría crear una arquitectura para una aplicación de microservicios cuyo único propósito es codificar vídeos almacenados en un bucket de S3 y almacenar los vídeos codificados de nuevo en un bucket de S3. Te gustaría que esta aplicación de microservicios fuera fiable y tuviera la capacidad de reintentar en caso de fallo. Cada vídeo puede tardar más de 25 minutos en ser procesado. Los servicios utilizados en la arquitectura deben ser asíncronos y deben tener la capacidad de detenerse durante un día y reanudarse al día siguiente a partir de los vídeos que aún no se han codificado. ¿Cuál de los siguientes servicios de AWS recomendarías en este escenario?
- Amazon SQS y Amazon EC2, SQS permite retener los mensajes durante dias y procesarlos mas tarde, mientras podemos retirar nuestras innstancias EC2 que actuaran como consumidores

Question 8:
Diriges un sitio web para compartir fotos en el que se descargan imágenes de todo el mundo. Cada mes publicas un paquete principal de bellas imágenes de montaña que tiene un tamaño de más de 15 GB. El contenido está actualmente alojado en un sistema de archivos Elastic File System (EFS) y distribuido por un Application Load Balancer y un conjunto de instancias EC2. Cada mes, estás experimentando un tráfico muy elevado que aumenta la carga de tus instancias EC2 y aumenta los costes de red. ¿Qué recomiendas para reducir la carga de EC2 y los costes de red sin refactorizar tu sitio web?
- Crear una distribucion de cloudfront ya que como CDN nos proporcionara de forma segura datos, videos, aplicaciones y APIs a clientes de todo el mundo con baja latencia y alta velocidad de transferencia.

Question 9:
Un servicio de AWS te permite capturar gigabytes de datos por segundo en tiempo real y entregar estos datos a múltiples aplicaciones consumidoras, con una función de repetición.
- Kinesis data streams, como servicio de streaming de datos en tiempo real es masivamente escalable y duradero. puede capturar continuamente gigabytes de datos por segundo de cientos de fuentes.