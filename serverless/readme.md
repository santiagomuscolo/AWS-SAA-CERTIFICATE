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

	## Detalles avanzados DynamoDB

**Acelerador de DynamoDB (DAX)**
- Cache en memoria totalmente gestionada, de alta disponibilidad y sin interrupciones para DynamoDB
- Ayuda a resolver la congestion de lectura mediante almacenamiento en cache
- Latencia de microsegundos para los datos almacenados en cache
- No requiere modificacion de la logica de la aplicacion (compatible con las API de DynamoDB existentes)
- TTL de 5 minutos (configurable)
- ![[Pasted image 20260408184348.png]]

**DAX vs ElastiCache**
- DAX 
	- es un servicio de cache de lectura disenado exclusivamente para DynamoDB
	- puede usarse cuando nuestra app realiza multiples consultas de lectura a la base y se produce un cuello de botella
	- puede usarse cuando nuestras consultas a la base son altamente predictibles y se realizan con frecuencia
	- Puede usarse para alto rendimiento de lectura
- ElastiCache
	- es un servicio de memoria cache distribuido adaptable a diversas bases de datos incluyendo DynamoDB
	- puede usarse cuando se requiera una cache de lectura/escritura
	- puede usarse cuando nuestra app requiere flexibilidad de cara a la configuracion de clusters

**Procesamiento de flujos**
- Flujo ordenado de modificaciones a nivel de articulo (crear/modificar/borrar) en una tabla
- Casos practicos:
	- Reaccionar a los cambios en tiempo real
	- Analisis de uso en tiempo real
	- Implementar la replicacion entre regiones
	- Invocar AWS Lambda en los cambios de la tabla de DynamoDB
- **DynamoDB Streams**
	- Retencion de 24 hs
	- Numero limitado de consumidores
	- Procesamiento mediante activadores de AWS Lambda o el adaptador de Kinesis de DynamoDB Stream
- **Kinesis Data Streams**
	- Retencion de 1 year
	- Alto numero de consumidores
	- Proceso con AWS lambda, kinesis data analytics, data firehose, aws glue streaming etl
- ![[Pasted image 20260408185545.png]]

**Tablas globales**
Una tabla global es una tabla donde tenemos multiples tablas distribuidas a lo largo de diversas regiones cuya replicacion se produce en ambas direcciones, siendo que es posible que datos que se guardan en una region tambien se guarden en la otra.
- Hacer accesible una tabla de DynamoDB con baja latencia en varias regiones.
- Replicacion activa-activa
- Las aplicaciones pueden leer y escribir en cualquier tabla de cualquier region.
- Se debe habilitar DynamoDB streams como requisito previo

**TTL**
- Borrar automaticamente los elementos despues de una fecha de caducidad
- Casos practicos: reducir los datos almacenados conservando solo los elementos actuales, cumplir las obligaciones normativas, gestion de sesiones web...

**Copias de seguridad para recuperacion de desastres**
- Copias de seguridad continuas mediante recuperacion puntual (PITR)
	- Activacion opcional para los ultimos 35 dias
	- Recuperacion puntual en cualquier momento dentro de la ventana de copia de seguridad
	- El proceso de recuperacion crea una nueva tabla
- Copias de seguridad bajo demanda
	- Copias de seguridad completas para su conservacion a largo plazo, hasta su eliminacion explicita
	- No afecta al rendimiento ni a la latencia
	- Se puede configurar y administrar en AWS Backup (permite la copia entre regiones)
	- El proceso de recuperacion crea una nueva tabla

**Integracion con Amazon S3**
- Exportacion a S3 (se debe habilitar Point in time recovery)
	- Funciona para cualquier momento de los ultimos 35 dias
	- No afecta a la capacidad de lectura de tu tabla
	- Conserva snapshots para auditorias
	- ETL sobre los datos de S# antes de volver a importarlos a DynamoDB
	- Exportacion en formato DynamoDB JSON o ION
- Importacion a S3
	- Importacion en formato CSV, DynamoDB JSON o ION
	- No consume capacidad de escritura
	- Crea una nueva tabla
	- Los errores de importacion se registran en CloudWatch Logs.

## API Gateway
API Gateway es un servicio totalmente gestionado por AWS que permite crear, publicar, mantener, monitorear y securizar APIs.
- Compatibilidad con protocolo Websockets
- Gestion de versiones
- Gestion de entornos
- Gestion de seguridad
- Creacion de claves API, gestion de limitacion de solicitudes (rate limiting)
- Importacion de Swagger / Open API
- Transformacion y validacion de solicitudes y respuestas
- Generacion de SDK y especificaciones de API
- Almacenamiento en cache de respuestas de API

**Integraciones de alto nivel**
- **Funcion Lambda**
	- Invocacion de funcion lambda
	- Manera sencilla de exponer API REST respaldada por AWS Lambda
- **HTTP**
	- Exponer puntos de enlace HTTP en el backend
	- Agregar limitacion de velocidad, almacenamiento en cache, autenticaciones de usuario, claves API
- **Servicio AWS**
	- Exponer cualquier API AWS a traves de API Gateway
	- Ejemplo: iniciar un flujo de trabajo de AWS step function, enviar un mensaje a SQS
	- Agregar autenticacion, desplegar automaticamente, control de rate

**Tipos de endpoints**
- **Edge-optimized (por defecto)**: para clientes globales
	- Las solicitudes se enrutan a traves de las ubicaciones de cloudfront edge (para mejorar la latencia)
	- API Gateway sigue viviendo en una sola region
- **Regional**:
	- Para clientes de la misma region
	- Podria combinarse manualmente con CloudFront (mas control sobre las estrategias de almacenamiento en cache y la distribucion)
- **Privada**:
	- Solo se puede acceder desde tu VPC utilizando un endpoint de VPC de interfaz (elastic network interface)
	- Utiliza una politica de recursos para definir el acceso

**Seguridad**
- **Autenticacion de usuarios mediante** 
	- Roles IAM (util para apps internas)
	- Cognito (para usuarios externos)
	- Autorizador personalizado (tu propia logica)
- **Seguridad HTTPS de nombre de dominio** personalizado a traves de la integracion con AWS Certificate Manager
	- Si utilizas el punto de enlace Edge-optimized, el certificado debe estar en us-east-1
	- Si tuilzias el punto de enlace regional, el certificado debe estan en la region de API Gateway
	- Debes configurar el registro CNAME o A-alias en Route 53 (A-alias apunta un dominio directo a una IP, CNAME apunta un dominio a otro dominio (es como un alias))