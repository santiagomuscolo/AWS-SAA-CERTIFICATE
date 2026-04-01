## Introduccion a la mensajeria
- Cuando empezamos a desplegar varias aplicaciones, es inevitanle que tengan que comunicarse entre si.
- Hay dos patrones de comunicacion entre aplicaciones.
	- Comunicacion sincrona (de aplicacion a aplicacion)
	- Comunicacion asincrona/basada en eventos (de aplicacion a la cola a la aplicacion)
- La sincronizacion entre aplicaciones puede ser problematica si hay picos repentinos de trafico, por ejemplo si de repente necesitamos codificar 1000 videos cuando normalmente son 10
- En ese caso, es mejor desacoplar tus aplicaciones:
	- Usando SQS: Modelo de cola
	- Usando SNS: Modelo pub/sub
	- Usando Kinesis: Modelo de flujo de datos en tiempo real

## Vision general Colas SQS

**Que es una cola?**
![[Pasted image 20260327103334.png]]

**Amazon SQS cola estandar**
- Servicio totalmente gestionado, utilizado para desacoplar aplicaciones
- Atributos:
	- Rendimiento ilimitado, numero ilimitado de mensajes en cola
	- Retencion de mensajes por defecto 4 dias, maximo 14 dias
	- Baja latencia (<10ms en publicacion y recepcion)
	- Limitacion de 256 KB por mensaje enviado
- Puede haber mensajes duplicados (al menos una entrega, ocasionalmente)
- Puede haber mensajes fuera de orden (orden de mejor esfuerzo)

**Produccion de mensajes**
- Producido a SQS utilizando el SDK (API SendMessage)
- El mensaje se conserva en SQS hasta que un consumidor lo elimina
- Retencion del mensaje: por defecto 4 dias, hasta un maximo de 14 dias.
- Ejemplo: Enviar un pedido para ser procesado
	- id de pedido
	- id de cliente
	- los atributos que quieras
- SQS estandar: rendimiento ilimitado

**Consumir mensajes**
- Consumidores (ejecutandose en instancias EC2, servidores o AWS Lambda)...
- Sondeo (encuestas) SQS en busca de mensajes (recibir hasta 10 mensajes a la vez)
- Procesar los mensajes (ejemplo: insertar el mensaje en una base de datos RDS)
- Eliminar los mensajes utilizando la API DeleteMessage
![[Pasted image 20260327104227.png]]

**SQS Consumidores de multiples instancias EC2**
- Los consumidores reciben y procesan los mensajes en paralelo
- Al menos una entrega
- Ordenacion de mensajes al mejor esfuerzo
- Los consumidores borran los mensajes despues de procesarlos
- Podemos escalar los consumidores horizontalmente para mejorar el rendimiento del procesamiento

**SQS con Auto Scaling Group (ASG)**
![[Pasted image 20260327104759.png]]

**SQS para desacoplar niveles de aplicacion**
![[Pasted image 20260327105025.png]]

Amazon SQS - Seguridad
- Cifrado:
	- Cifrado en vuelo mediante API HTTPS
	- Cifrado en reposo mediante claves KMS
	- Cifrado del lado del cliente si el cliente desea realizar cifrado/descifrado por si mismo
- Controles de acceso: Politicas IAM para regular el acceso a la API SQS
- Politicas de acceso a SQS (similares a las politicas del bucket S3)
	- Util para el acceso entre cuentas a las colas SQS
	- Util para permitir a otros servicios (SNS, S3...) escribir en una cola SQS


## SQS - tiempo de espera de la visibilidad de los mensajes
- Despues de que un consumidor sondee un mensaje, este se vuelve invisible para los demas consumidores.
- Por defecto, el "tiempo de visibilidad del mensaje" es de 30 segundos.
- Esto significa que el mensaje tiene 30 segundos para ser procesado.
- Una vez transcurrido el tiempo de espera, el mensaje es "visible" en SQS.
- ![[Pasted image 20260327144204.png]]

- Si un mensaje no se procesa dentro del tiempo de visibilidad, se procesara dos veces.
- El consumidor puede llamar a la API ChangeMessageVisibility para obtener mas tiempo.
- Si el tiempo de espera de visibilidad es alto (horas) y el consumidor se bloquea, el reprocesamiento llevara tiempo.
- Si el tiempo de visibilidad es demasiado bajo (segundos), puede haber duplicados.

## SQS - Long Polling
- Cuando un consumidor solicita mensajes de la cola, puede opcionalmente "esperar" a que lleguen los mensajes si no hay ninguno en la cola
- Esto se llama Sondeo Largo
- LongPolling disminuye el numero de llamadas API realizadas a SQS, al tiempo que aumenta la eficiencia y reduce la latencia de tu aplicacion.
- El tiempo de espera puede oscilar entre 1 y 20 segundos (preferiblemente 20 segundos)
- El sondeo largo es preferible al corto
- Puede activarse a nivel de cola o a nivel de API utilizando WaitTimeSeconds

## SQS - Colas FIFO
- FIFO: First in first out (ordenacion de los mensajes en la cola)
- Rendimiento limitado: 300 msg/s sin procesamiento por lotes, 3000 msg/s con procesamiento por lotes.
- Capacidad de envio exactamente una vez (eliminando los duplicados)
- El consumidor procesa los mensajes en orden

## SQS - ASG
sqs nos brinda la posibilidad de mediante una alarma de cloudwatch validado por una cloudwatch metric poder hacer auto-escalar un grupo ASG de instancias, sin embargo, esto presenta un inconveniente... si nosotros tuviesemos miles de millones de requests por segundo probablemente en el proceso de insercion en la base de datos algunas transacciones se perderian, esto es solucionable usando SQS como buffer de escrituras en la base de datos:
![[Pasted image 20260330103014.png]]

es asi que si alguna transaccion no llegase a estar en la DB permaneceria en SQS ya que es infinitamente escalable y confiable.

para finalizar el concepto mas relevante de SQS es el desacoplamiento de los diversos niveles de la aplicacion, permitiendonos una mejor usabilidad y escalabilidad:
![[Pasted image 20260330103200.png]]

## Amazon Simple Notification Service (SNS)
![[Pasted image 20260330103438.png]]

- El productor de eventos solo envia mensajes a un topic SNS
- Tantos "receptores de eventos" (suscriptores) como queramos para escuchar notificaciones del tema SNS
- Cada suscriptor al tema recibira todos los mensajes (nota: nueva funcion para filtrar mensajes)
- Hasta 12.500.000 subscribers por tema
- Limite de 100.000 topics
- ![[Pasted image 20260330103751.png]]
- Muchos servicios de AWS pueden enviar datos directamente a SNS para notificaciones:
	- CloudWatch alarms
	- AWS budgets
	- Lambda
	- ASG Notifications
	- S3
	- DynamoDB
	- CloudFormation (state changes)
	- AWS DMS (new republic)
	- RDS Events

**Como publicar**
- Publicacion de temas (mediante el SDK)
	- Crear un tema
	- Crear una suscripcion (o varias)
	- Publicar en el tema
- Publicacion directa (para aplicaciones moviles SDK)
	- Crear una aplicacion de plataforma
	- Crear un endpoint de plataforma
	- Publicar en el endpoint de la plataforma
	- Funciona con Google GCM, Apple APNS, Amazon ADM...

**Seguridad**
- Cifrado:
	- Cifrado en vuelo mediante API HTTPS (TLS/SSL)
	- Cifrado en reposo mediante claves KMS
	- Cifrado del lado del cliente si el desea realizar el cifrado/descifrado por si mismo.
- Controles de acceso: Politicas de IAM para regular el acceso a la API SNS
- Politicas de acceso SNS (similar a las politicas del bucket S3)
	- Util para el acceso entre cuentas a temas SNS

## SNS Y SQS: Fan Out
Fan out es el concepto de integracion de SNS + SQS:
- Se realiza un push una sola vez en SNS, este se recibe en todas las colas SQS que son suscriptores
- Totalmente desacoplado, sin perdida de datos
- SQS permite: persistencia de datos, procesamiento diferido y reintentos de trabajo.
- Posibilidad de agregar mas suscriptores SQS con el tiempo.
- Asegurate de que la politica de acceso a la cola SQS permite que SNS pueda escribir

**Aplicacion: Eventos S3 a multiples colas**
- Para la misma combinacion de: **tipo de evento** (p.e. creacion de objeto) y prefijo (p.e. imagenes/) solo puedes tener una regla de Evento S3.
- Si quieres enviar el mismo evento S3 a muchas colas SQS, utiliza Fan-out
- ![[Pasted image 20260330113528.png]]

**Aplicacion: SNS a Amazon S3 a traves de Kinesis Data Firehose**
- SNS puede enviar a Kinesis y por lo tanto podemos tener la siguiente arquitectura de soluciones:
  ![[Pasted image 20260330113731.png]]

**Amazon SNS - Tema (topic) FIFO
![[Pasted image 20260330113909.png]]
- Caracteristicas similares a SQS FIFO:
	- **Ordenacion**: por ID de grupo de mensajes (se ordenan todos los mensajes del mismo grupo)
	- **Deduplicacion**: mediante ID de deduplicacion o deduplicacion basada en contenido.
- Solo puede tener colas SQS FIFO como suscriptores
- Rendimiento limitado (el mismo que SQS FIFO)
- FIFO nace con la finalidad de solucionar el probelma fan-out + ordenacion + deduplicacion:
  ![[Pasted image 20260330114230.png]]

**Filtrado de mensajes**
- Politica JSON utilizada para filtrar lso mensajes enviados a las suscripciones del tema SNS
- Si una suscripcion no tiene una politica de filtrado, recibe todos los mensajes
- ![[Pasted image 20260330114532.png]]

## Vision general de Amazon Kinesis
- Facilita la recopilacion, el procesamiento, y el analisis de datos de flujo continuo en tiempo real.
- Ingesta de datos en tiempo real como: registros de aplicaciones, metricas, secuencias de clics de sitios web, datos telemetricos de IoT...
- Servicios:
	- Kinesis Data Streams: captura, procesa y almacena flujos de datos.
	- Kinesis Data Firehose: carga flujos de datos en almacenes de datos de AWS.
	- Kinesis Data Analytics: analiza flujos de datos con SQL o Apache Flink.
	- Kinesis Video Streams: captura, procesa y almacena transmisiones en vivo.

## Kinesis Data Streams
Es una forma de transmitir big data en nustros sistemas
![[Pasted image 20260401103958.png]]

- Retencion entre 1 dia y 365 dias.
- Posibilidad de volver a procesar (reproducir) los datos.
- Una vez que los datos se insertan en kinesis, no pueden borrarse (inmutabilidad).
- Los datos que comparten la misma particion van al mismo fragmento (ordenacion).
- Productores: SDK de AWS, bibkioteca de productores de kinesis (KPL), agente de kinesis.
- Consumidores: 
	- Escriba el suyo propio: kinesis client library (KCL), AWS SDK.
	- Administrados: AWS Lambda, Kinesis data firehose, kinesis data analytics.

**Modos de capacidad**
- **Modo aprovisionado**:
	- Tu eliges el numero de shards aprovisionados, escala manualmente o usando API.
	- Cada fragmento recibe 1 MB/s (o 1000 registros por segundo).
	- Cada fragmento recibe 2 MB/s (consumo en fan-out clasico o mejorado).
	- Se paga por cada fragmento aprovisionado por hora.
- **Modo bajo demanda**
	- No es necesario aprovisionar ni gestionar la capacidad.
	- Capacidad provisionada por defecto (4 MB/s de entrada o 4000 registros por segundo)
	- Escala automaticamente en funcion del pico de rendimiento observado durante los ultimos 30 dias.
	- Pago por flujo por hora y entrada/salida de datos por GB.

**Seguridad**
- Control de acceso / autorizacion mediante politicas IAM
- Cifrado en vuelo mediante endpoints HTTPS
- Cifrado en reposo mediante KMS
- Puede implementar el cifrado/descifrado de datos en el lado del cliente.
- Endpoints VPC disponibles para que kinesis acceda dentro de VPC
- Supervision de las llamadas a la API mediante CloudTrail.

## Vision general de Kinesis Data Firehose
es un servicio que toma los datos de los productores y escribe los datos en los destinos sin que nosotros tengamos que escribir ningun tipo de codigo.
![[Pasted image 20260401122658.png]]

- Servicio totalmente administrado, sin administracion, escalado automatico, sin servidor.
	- AWS: redshift, s3, elasticSearch
	- Servicios de terceros: datadog, new relic, mongo db, splunk...
	- Personalizado: Enviar a cualquier punto final HTTP.
- Pague por los datos que pasan por Firehose
- Casi en tiempo real:
	- Latencia minima de 60 segundos por lotes no completos
	- Un minimo de 1MB de datos a la vez
- Admite muchos formatos de datos, conversiones, transformaciones y compresion.
- Admite transformaciones de datos personalizadas mediante AWS lambda.
- Puede enviar datos fallidos o todos los datos a un bucket S3 de backup.

**Kinesis data streams vs data firehose**
- Kinesis data streams:
	- Servicio de streaming para la ingesta a escala.
	- Escribir codigo personalizado (productor/consumidor)
	- En tiempo real ( - 200ms)
	- Gestion del escalado (division/fusion de fragmentos)
	- Almacenamiento de datos de 1 a 365 dias.
	- Capacidad de reproduccion.
- Kinesis firehose:
	- Carga de datos de streaming en S3 / RedShift / ES / terceros / HTTP personalizado.
	- Totalmente gestionado.
	- Casi en tiempo real (tiempo de buffer de min 60 segundos)
	- Escalado automatico
	- Sin almacenamiento de datos
	- No soporta capacidad de repeticion

## Ordenacion de datos para kinesis vs SQS FIFO
- Imagina que tienes 100 camiones (camion_1, camion_2, camion_100) en la carretera enviando sus posiciones GPS regularmente a AWS.
- Se desea consumir los datos en orden para cada camion, de modo que pueda seguir su movimiento con precision.
- Como entonces enviamos los datos a kinesis? la respuesta es con un valor de 'Partition Key' del "truck_id" ("camion_id").
- La misma clave ira siempre al mismo fragmento
- ![[Pasted image 20260401130939.png]]

**Ordenar datos en SQS**
- Para SQS estandar, no hay ordenacion.
- Para SQS FIFO, si no se utiliza un ID de grupo, los mensajes se consumen en el orden en que se envian, con un solo consumidor.
- ![[Pasted image 20260401131306.png]]
- Si se desea escalar el numero de consumidores, pero quieres que los mensajes esten "agrupados" cuando se relacionan entre si se puede utilizar un "ID" de grupo (similar a la clave de particion en kinesis)
- ![[Pasted image 20260401131403.png]]

**Ordenacion Kinesis vs SQS**
- Supongamos que tenemos 100 camiones, 5 fragmentos kinesis, 1 SQS FIFO
- Kinesis data streams:
	- Por termino medio, tendras 20 camiones por fragmento.
	- Los camiones tendran sus datos ordenados dentro de cada fragmento.
	- La cantidad maxima de consumidores en paralelo que podemos tener serian 5 (5 fragmentos de 20 camiones cada uno, 1 consumidor por fragmento).
	- Puede recibir hasta 5 MB/s de datos (1MB/s por fragmento).
- SQS FIFO:
	- Solo tenemos 1 cola SQS FIFO.
	- Tendriamos 100 IDs de grupo.
	- Podriamos tener hasta 100 consumidores 1 por Group ID.
	- Tendriamos hasta 300 mensajes por esgundo (o 3000 si usamos batching)

## SQS vs SNS vs Kinesis
- SQS:
	- Los consumidores "tiran de los datos".
	- Los datos se borran despues de ser consumidos.
	- Podemos tener tantos trabajadores (consumidores) como queramos
	- No es necesario aprovisionar rendimiento.
	- Garantias de ordenacion solo en colas FIFO.
	- Capacidad de retardo de mensajes individuales.
- SNS:
	- Envio de datos a muchos suscriptores.
	- Hasta 12.500.000 suscriptores.
	- Los datos no se conservan (se pierden si no se entregan).
	- Pub/Sub.
	- Hasta 100.000 Topics.
	- No es necesario aprovisionar caudal.
	- Se integra con SQS para un patron de arquitectura fan-out.
	- Capacidad FIFO para SQS FIFO.
- Kinesis:
	- Estandar: extraer datos (2MB por fragmento).
	- Fan-out reforzado: datos push (2MB por fragmento y consumidor).
	- Posibilidad de reproducir los datos.
	- Pensado para big data en tiempo real, analisis y ETL.
	- Ordenacion a nivel de fragmento.
	- Los datos caducan a los X dias.
	- Modo aprovisionado o modo de capacidad bajo demanda.
- Cuando se refiere a "aprovisionar" quiere decir definir manualmente cuanta capacidad/escala va a tener un servicio antes de usarlo.
- Luego un shard que es aquello que se aprovisiona en Kinesis hace referencia a una "unidad de capacidad" dentro de un stream, este almacena temporalmente datos (stream), procesa registros en orden (dentro del shard), permite paralelismo (varios shards = procesamiento paralelo).
- Y con stream nos referimos a un flujo continuo de datos en tiempo real (es decir, datos que estan llegando constantemente)