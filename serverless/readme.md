## Introduccion a serverless
- Serverless es un nuevo paradigma en el que los desarrolladores ya no tienen que gestionar servidores
- Solo despliegan codigo
- Solo despliegan Funciones
- Inicialmente serverless era igual a Faas (funcion como servicio)
- Serverless fue pionero por AWS Lambda, pero tambien incluye todo lo que se gestiona como bases de datos, mensajeria, almacenamiento, etc...

**Servicios serverless**
- Lambda
- DynamoDB
- Cognito
- API Gateway
- S3
- SNS y SQS
- Kinesis Data Firehose
- Aurora serverless
- Step functions
- Fargate

## Lambda

**Diferencia EC2 y Lambda**
- EC2
	- Servidores virtuales en el cloud
	- limitado por la RAM y la CPU
	- funcionamiento continuo
	- Escalar significa intervenir para agregar o quitar servidores
- Lambda
	- Funciones virtuales que no hay que gestionar
	- Escalado automatizado
	- Limitado por tiempo - ejecuciones cortas
	- Ejecucion bajo demanda

**Beneficios**
- Precios sencillos
	- se paga por solicitud y tiempo de ejecucion
	- la capa gratuita cuenta con 1 millon de solicitudes y 400 mil GB de tiempo de computacion
- Integrado con todo el conjunto de servicios de AWS
- Dirigido por eventos - esto quiere decir que estas funciones pueden ser invocadas cuando se necesitan
- Integrado con muchos lenguajes de programacion
- Facil monitorizacion a traves de cloudwatch
- Facil de obtener mas recursos por funciones (hasta 10 GB de RAM)

**Pricing**
- pago por llamadas
	- Los primeros 1.000.000 de solicitudes son gratuitas
	- 0,20$ por cada millon de solicitudes a partir de entonces
- Pago por duracion (en incrementos de 1ms)
	- 400.000 GB de segundos de tiempo de calculo al mes
	- == 400.000 segundos si la funcion es de 1 GB de RAM
	- == 3.200.00 segundos si la funcion es de 128MB de RAM
	- Despues es 1 dolar por 600.000 GB segundos

	**limites**
- Ejecucion
	- Asignacion de memoria: 128 MB - 10 GB (incrementos de 1 MB)
	- Tiempo maximo de ejecucion: 900 segundos (15 minutos)
	- Variables de entorno (4 KB)
	- Capacidad de disco en el "contenedor de funciones" (/tmp): 512 MB a 10 GB
	- Concurrencia de ejecuciones: 1000 (puede aumentarse)
- Despliegue
	- Tamano del despliegue de la funcion lambda (.zip comprimido): 50 MB
	- Tamano del despliegue sin comprimir (codigo + dependencias): 250 MB
	- Puede utilizar el directorio /tmp para cargar otros archivos al inicio
	- Tamano de las variables de entorno: 4 KB

**Lambda Edge y Cloudfront**
- Muchas aplicaciones modernas ejecutan alguna forma de la logica en el borde
- **Funcion de borde (edge function)**
	- Un codigo que se escribe y adjunta a las distribuciones de cloudfront
	- Se ejecuta cerca de los usuarios para minimizar la latencia
- Cloudfront proporciona dos tipos
	- **Funciones cloudfront y Lambda@Edge
- No tienes que administrar ningun servidor, ya que es implementado globalmente
- Caso de uso: personalizar el contenido de la CDN

**Funciones de cloudfront y casos de uso de Lambda@Edge**
- Seguridad y privacidad de sitios web
- Aplicaciones web dinamicas en el Edge
- Optimizacion para motores de busqueda (SEO)
- Enrutamiento inteligente entre origenes y centros de datos
- Mitigacion de bots en el Edge
- Transformacion de imagenes en tiempo real
- Pruebas A/B
- Autenticacion y autorizacion de usuarios
- Priorizacion de usuarios
- Seguimiento y analisis de usuarios

**Funciones de Cloudfront**
- Funciones ligeras escritas en javascript
- Para personalizaciones de CDN a gran escala y sensibles a la latencia
- Tiempos de arranque inferiores a milisegundos, millones de solicitudes/segundo
- Se utiliza para modificar las solicitudes y respuestas de los espectadores:
	- **Solicitud del espectador**: despues de que Cloudfront reciba la solicitud del espectador
	- **Respuesta al espectador**: antes de que Cloudfront envie la respuesta al espectador
- Caracteristica nativa de cloudfront (gestiona el codigo completamente dentro de cloud front)
- ![[Pasted image 20260407104634.png]]

**Lambda@Edge**
- Funciones lambda escritas en NodeJS o Python
- Escala a 1000s de peticiones/segundo
- Se utiliza para modificar las solicitudes y respuestas de cloudfront
	- **Solicitud del espectador**: despues de que cloudfront reciba una solicitud del espectador
	- **Solicitud al origen**: antes de que cloudfront reenvie la solicitud al origen
	- **Respuesta del origen**: despues de que cloudfront reciba la respuesta del origen
	- **Respuesta al espectador**: antes de que cloudfront reenvie la respuesta al espectador.
- Podemos crear las funciones en una region de AWS ej: us-east-1, luego cloudfront replica a tus ubicaciones.
- ![[Pasted image 20260407105048.png]]

**Funciones de cloudfront vs Lambda@Edge**
![[Pasted image 20260407105351.png]]

**Casos de uso**
- Funciones de cloudfront
	- Normalizacion a traves del cache (transformacion de los atributos de la solicitud como cabeceras, cookies, cadenas de consulta, URL para crear una clave de cache optima)
	- Manipulacion de cabceras (insercion/modificacion/eliminacion de cabeceras HTTP en la solicitud o la respuesta)
	- Reescritura o redireccionamiento de URL
	- Autenticacion y autorizacion de solicitudes
	- Creacion y validacion de tokens generados por el usuario (por ejemplo, JWT) para permitir o denegar solicitudes
- Lambda@Edge
	- Mayor tiempo de ejecucion (varios ms)
	- CPU o memoria ajustables
	- El codigo depende de una tercera libreria (aws SDK) para acceder a otros servicios de AWS
	- Acceso a la red para utilizar servicios externos para el procesamiento
	- Acceso al sistema de archivos o acceso al cuerpo de solicitud HTTP

**Lambda en VPC**
- Por defecto, la funcion Lambda se lanza fuera de la propia VPC (en una VPC propiedad de AWS)
- Por lo tanto, no puedes acceder a los recursos de la VPC (RDS, ElastiCache, ELB interno...)
- Para poder usar Lambda en una subred privada se debe definir el ID de la VPC, las subredes y los grupos de seguridad, a partir de esto Lambda creara una Elastic Network Interface en las subredes 
- ![[Pasted image 20260407114545.png]]

**Lambda con proxy RDS**
- Si las funciones lambda acceden directamente a tu base de datos, pueden abrir demasiadas conexiones bajo carga elevada.
- Proxy RDS
	- Mejora la escalabilidad agrupando y compartiendo a la base de datos
	- Mejora la disponibilidad reduciendo en un 66% el tiempo de conmutacion por error y conservando las conexiones
	- Mejora la seguridad aplicando la autenticacion IAM y almacenando las credenciales en Secrets Manager.
- Para esto la funcion Lambda debe desplegarse en la VPC, ya que RDS proxy nunca es accesible publicamente
- ![[Pasted image 20260407114935.png]]

## DynamoDB
- Totalmente gestionado, de alta disponibilidad con replicacion a traves de multiplse AZs
- Base de datos NoSQL - no relacional - con soporte de transacciones
- Escala a cargas de trabajo masivas, ya que es una base de datos distribuida
- Millones de peticiones por segundo, billones de filas, cientos de TB de almacenamiento
- Rendimiento rapido y constante
- Integracion con IAM para seguridad, autorizacion y administracion
- Bajo coste y capacidad de autoescalado
- Sin mantenimiento ni parches, siempre disponible
- Clase de tabla de acceso estandar e infrecuente

**conceptos basicos**
- Dynamo DB se compone de tablas
- Cada tabla tiene una clave primaria
- Cada tabla puede tener un numero infinito de filas
- Cada elemento tiene atributos
- El tamano maximo de un elemento es de 400 kb
- Los tipos de datos soportados son:
	- Tipos escalares - Cadenas, booleanos, binarios, numeros, nulo
	- Tipo documento - Lista, Mapa
	- Tipo de conjuntos - conjunto de cadenas, conjunto de numeros, conjunto de binarios
- Por lo tanto DynamoDB puede evolucionar rapidamente los schemas (no se requieren migraciones complejas)

**Modos de capacidad de lectura/escritura
- Controla como gestiona la capacidad de tu tabla (rendimiento lectura/escritura)
- **Modo aprovisionado**
	- Especificas el numero de lecturas/escrituras por segundo
	- Es necesario planificar la capacidad de antemano
	- Pagas por unidades de capacidad de lectura (RCU) y escritura (WCU) provisionadas.
	- Posibilidad de agregar el modo de autoescalado para RCU y WCU
- **Modo bajo demanda**
	- Las lecturas/escrituras aumentan/disminuyen automaticamente con tus cargas de trabajo
	- No es necesario planificar capacidad
	- Pagas por lo que utilizas
	- Ideal para cargas de trabajo impredecibles, picos repentinos pronunciados