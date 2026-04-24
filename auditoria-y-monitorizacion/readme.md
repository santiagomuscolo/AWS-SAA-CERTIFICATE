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