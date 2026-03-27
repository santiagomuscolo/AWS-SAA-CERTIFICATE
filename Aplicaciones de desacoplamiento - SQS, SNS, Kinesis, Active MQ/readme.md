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