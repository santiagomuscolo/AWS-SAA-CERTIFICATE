## Lambda, SNS y SQS
![[Pasted image 20260626160623.png]]

**Patron fan out: Entrega a varios SQS**

- Patron basico donde pueden surgir inconvenientes que afecten la entrega/consumision a la sqs
- ![[Pasted image 20260626160816.png]]

- Patron Fan out - evita la perdida/bloqueo de la entrega a multiples SQS mediante Pub/sub
- ![[Pasted image 20260626160910.png]]

### Notificaciones de eventos S3
S3 dentro de su propio servicio ofrece las notificaciones de eventos que suelen entregarse en segundos pero a veces pueden demorar minutos o mas, es bastante flexible ya que permite crear x numero de eventos segun se desee y posee filtros integrados
![[Pasted image 20260626161103.png]]

sin embargo, tenemos la posibilidad tambien de usar EventBridge para la gestion de eventos en S3

![[Pasted image 20260626162821.png]]

ofreciendo:
- Opciones avanzadas de filtrado con reglas JSON (metadatos, size del objeto, nombre)
- Multiples destinos - funciones step, kinesis streams / firehose
- capacidades de eventbridge - archivar, repeticion de eventos, entrega fiable

## Estrategias de almacenamiento en cache
![[Pasted image 20260629195952.png]]
## Bloquear una direccion IP en AWS

**NACL, SG y Firewall software**
![[Pasted image 20260629200405.png]]

**NLB**
![[Pasted image 20260629201254.png]]

**ALB**
![[Pasted image 20260629201311.png]]

**ALB + WAF**
![[Pasted image 20260629201501.png]]

**ALB + CLoudfront + WAF**
![[Pasted image 20260629201623.png]]