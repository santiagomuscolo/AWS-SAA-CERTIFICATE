## CloudWatch Metrics
Es un servicio enfocado en la monitorizacion ofreciendo metricas para cada servicio de AWS
- Una metrica es una variable a monitorizar (CPUUtilization, NetworkIn...)
- Las metricas pertenecen a espacios de nombres
- Las metricas tienen marcas de tiempo
- Se pueden crear dashboard a partir de las mismas
- Se pueden crear metricas personalizadas de cloudwatch (para la RAM, por ejemplo)

**Flujos de metricas de cloudwatch**
- Transmite continuamente metricas de CloudWatch a un destino de tu eleccion, con entrega casi en tiempo real y baja latencia.
	- Amazon Kinesis Data Firehose (y luego sus destinos: S3, Redshift, OpenSearch)
	- Proveedor de servicios de terceros: DataDog, Splunk, New Relic, Mongo, etc...
- Opcion de filtrar metricas para transmitir un solo subconjunto de ellas
- ![[Pasted image 20260423120210.png]]

## CloudWatch Logs
- Grupos de registro: nombre arbitrario (normalmente representando una aplicacion)
- Flujo de registro: instancias dentro de la aplicacion / archivos de registro / contenedores
- Puede definir politicas de expiracion de logs (nunca expiran, 30 dias, etc...)
- CloudWatch logs puede enviar logs a:
	- S3
	- Flujos de datos de kinesis
	- Kinesis data firehose
	- Lambda
	- OpenSearch

**Fuentes**
- SDK, agente de CloudWatch Logs, agente unificado de CloudWatch
- Elastic Beanstalk: recogida de logs desde la aplicacion
- ECS: recopilacion en contenedores
- Lambda: recopilacion de registros de funciones
- Registro de flujo de VPC: registros especificos de VPC
- API Gateway
- CloudTrail basado en filtro
- Route53: registro de consultas DNS

**Filtro de metricas e informacion**
- CloudWatch Logs puede utilizar expresiones de filtro
	- por ejemplo, encontrar una IP especifica dentro de un log
	- O contar ocurrencias de "ERROR" en sus registros
- Los filtros de metricas pueden utilizarse para activar alarmas de CloudWatch
- CloudWatch Logs Insights puede utilizarse para consultar registros y agregar consultas a CloudWatch Dashboards

**Exportacion a S3**
- Los datos de registro pueden tardar hasta 12 horas en estar disponibles para su exportacion usando la API CreateExportTask
- Puede exportarse directamente de CloudWatch Logs a S3

**Suscripciones a CloudWatch Logs**
CloudWatch logs nos ofrece la posibilidad de utilizar filtros de suscripcion para agilizar el proceso de exportacion pudiendo linkear con lambdas personalizadas o gestionadas por AWS o mismo con Kinesis Data Firehose obteniendo los logs casi en tiempo real.
![[Pasted image 20260423121907.png]]

**Multi-cuenta y Multi-region**
Kinesis data streams nos ofrece la posibilidad de centralizar los streams de datos con los logs y usar salidas como data firehose para la insercion de los mismos en sus multiples salidas (S3, Redshift, OpenSearch)
![[Pasted image 20260423122055.png]]
## Agentes de cloudwatch
- Para servidores virtuales (instancias EC2, servidores locales...)
- Agente de logs de cloudwatch
	- Version antigua del agente
	- Solo puede enviar a cloudwatch logs
- Agente unificado de cloudwatch
	- Recoge metricas adicionales a nivel de sistema, como RAM, procesos, etc...
	- Recoge logs para enviarlos a CloudWatch Logs
	- Configuracion centralizada mediante el Almacen de Parametros SSM

**Agente unificado - metricas**
- CPU (activa, huesped, inactiva, sistema, usuario)
- Metricas de disco (Libre, usado, total), I/O de disco (escrituras, lecturas, bytes, iops)
- RAM (gratis, inactiva, usada, total, en cache)
- Netstat (numero de conexiones TCP y UDP, paquetes netos, bytes)
- Procesos (totales, muertos, bloqueados, inactivos, en ejecucion, en reposo)
- Espacios de intercambio (gratis, usado, % usado)

## CloudWatch Alarms
- Las alarmas se utilizan para activar notificaciones para cualquier metrica
- Varias opciones (muestreo, %, max, min, etc...)
- Estados de alarma:
	- OK
	- DATOS_INSUFICIENTES
	- ALARMA
- Periodo:
	- Tiempo en segundos para evaluar la metrica
	- Metricas personalizadas de alta resolucion: 10 segundos, 30 seg o multiplos de 60 seg

**Objetivos**
Detener, Terminar, Reiniciar o Recuperar una instancia EC2
Activar la accion de autoescalado
Enviar una notificacion SNS (desde donde puedes hacer practicamente cualquier cosa)

**Alarmas compuestas**
- Las alarmas CloudWatch son sobre una metrica unica
- Las alarmas compuestas supervisan los estados de otras alarmas multiples
- Condiciones AND y OR
- Utiles para reducir el "ruido de alarma" creando alarmas compuestas complejas

**Recuperacion de instancias**
- Comprobacion de estado
	- Estado de la instancia = comprueba la maquina virtual de EC2
	- Estado del sistema = comprueba el hardware subyacente
- Recuperacion bajo la misma IP privada, publica, elastica, metadatos, grupo de colocacion

## EventBridge
 - Programar: scripts programados (cron jobs)
 - Patron de eventos: reglas de eventos para reaccionar ante un servicio que hace algo
 - Activa funciones lambda, envia mensajes sqs/sns
 - ![[Pasted image 20260424114033.png]]
 - ![[Pasted image 20260424114233.png]]
- Otras cuentas de AWS pueden acceder a los buses de eventos mediante politicas basadas en recursos
- Puedes archivar eventos (todos/filtro) enviados a un bus de eventos (indefinidamente o por un periodo determinado)

**Registro de esquemas**
- EventBridge puede analizar los evntos de tu bus e inferir el esquema
- El registro de esquemas te permite generar codigo para tu aplicacion, que sabra de antemano como se estructuran los datos en el bus de eventos
- El esquema puede versionarse
- ![[Pasted image 20260424114704.png]]

**Politica basada en recursos**
- Gestionar permisos para un bus de eventos especifico
- Ejemplo: permitir/denegar eventos de otra cuenta AWS o region AWS
- Caso practico: agregar todos los eventos de tu Organizacion AWS en una unica cuenta AWS o region AWS

## Cloudwatch insights y visibilidad operativa

**Container insights**
- Recoge, agrega y resume las metricas y logs de los contenedores
- disponible en 
	- ECS
	- EKS
	- Plataformas Kubernetes en EC2
	- Fargate (tanto para ECS como para EKS)
- En Amazon EKS y Kubernetes, CloudWatch Insights utiliza una version en contendores del Agente CloudWatch para descubrir contenedores

**Lambda Insights**
- Recopila, agrega y resume metricas a nivel de sistema, incluyendo tiempo de CPU, memoria, disco y red
- Recopila, agrega y resume informacion de diagnostico, como arranques en frio y cierres de trabajadores lambda.
- Lambda insights se proporciona como una capa de Lambda.

**Cloudwatch contributors insights**
- Analiza los logs y crea series temporales que muestren los datos de los colaboradores
	- Ver metricas de los N colaboradores principales.
	- El numero total de colaboradores unicos y su uso.
- Esto te ayuda a encontrar a los que mas hablan, y a comprender quien o que esta afectando al rendimiento del sistema
- Funciona para cualquier logs generado por AWS (VPC, DNS, etc...)
- Por ejemplo, puedes encontrar hosts defectuosos, identificar a los usuarios de red mas pesados o encontrar las URL que generan mas errores
- Puedes crear tus reglas desde 0, o tambien pueden utilizarse las reglas que AWS disponibiliza

**Cloudwatch application insights**
Proporciona dashboards automatizados creados por SageMaker para mostrar problemas potenciales de aplicaciones monitorizadas, permitiendo aislar problemas en curso.
- Aplicaciones que se ejecutan en EC2 solo con determinadas tecnologias (Java, .NET, Microsoft IIS Web Server, bases de datos...)
- Y puedes utilizar otros recursos de AWS como: EBS, RDS, ELB, ASG, Lambda, SQS, DynamoDB, S3 bucket, ECS, EKS, SNS, API Gateway
- Las conclusiones y alertas se pueden enviar a EventBridge mediante notificaciones de eventos al eventbus y a SSM OpsCenter

## Cloudtrail
Es un servicio para gobernanza, normativa y auditoria, cloudtrail ofrece logs de todos los llamados a la API de AWS, permitiendo guardar los mismos en CloudWatch logs o S3, aplicable de forma multi regional o solo regional.

**Eventos de gestion**
Los eventos de gestion estan activados por defecto y hacen referencia a las operaciones que se realizan en los recursos de tu cuenta de AWS, como por ejemplo:
- Configurar la seguridad
- Configurar reglas para enrutar datos
- Configurar logs
Los eventos de gestion son divisibles por eventos de lectura (aquellos que no modifican recursos) y eventos de escritura (aquellos que si los modifican.

**Eventos de datos**
Estan desactivados por defecto y son divisibles por lectura y escritura, como son eventos masivos como por ejemplo api invokes o lecturas de objetos estos no son visibles por defecto.

**Insights events**
Insights usa los eventos de gestion como base para detectar anomalias en nuestros recursos de AWS, verificando los eventos de escritura y sus patrones.
![[Pasted image 20260427200149.png]]

**Retencion**
Cloudtrail almacena logs durante un periodo de 90 dias, luego para su respectivo almacenamiento y analisis deberian pasarse a un bucket S3 y ser analizados por athena

**Aclaracion**
En administracion los eventos de lectura hacen referencia a la consulta de configuracion/inventario de la cuenta no al recurso individual ya que eso pertenece al evento de datos.
En otras palabras, en management events se pena la configuracion del recurso en si y en data events se panea todo lo que esta dentro de ese recurso a nivel individual

## AWS Config 
Es un servicio que nos sirve para auditar y registrar la normativa de nuestros servicios de AWS, ayudando a registrar cambios y configuraciones a lo largo del tiempo, es un servicio por region que nos provee notificaciones sns para alertas y almacenamiento de datos en S3.

**reglas de configuracion**
AWS disponibiliza +75 reglas de configuracion, sin embargo, podemos tener reglas custom y pueden activarse o desactivarse, se cobra 0,003$ por elemento configurado por region mas 0,001% por evaluacion de regla de configuracion por region.
![[Pasted image 20260427203802.png]]

**Autoremediacion**
AWS config nos permite ver si los servicios estan "conformes" o "no conformes", mediante esta evaluacion podemos activar acciones de autorremediacion para corregir dicho estado con un maximo de 5 reintentos.
![[Pasted image 20260427204039.png]]

**Notificaciones**
Config puede enviar notificaciones a eventBridge y que eventBridge distribuya el evento a otros servicios de AWS, permitiendonos notificar frente a servicios no "conformes" y/o cambios en las configuraciones
![[Pasted image 20260427204259.png]]
![[Pasted image 20260427204303.png]]