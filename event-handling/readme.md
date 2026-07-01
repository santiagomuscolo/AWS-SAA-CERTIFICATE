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

## High performance computing
En el HPC se busca crear un numero muy elevado de recursos en muy poco tiempo, permitiendo acelerar el tiempo de obtencion de los resultados agregando mas recursos y solamente pagar por lo utilizado.
High performance computing es un termino muy utilizado en la quimica computacional, modelizacion de riesgos financieros, prediccion meteorologica, aprendizaje profundo, conduccion autonoma.

Que servicios ayudan a realizar HPC?

- Gestion y transferencias de datos
	- AWS Direct connect: Mover GB/s de datos al cloud, a traves de una red privada
	- Snowball y Snowmobile: mover PB de datos al Cloud
	- DataSync: mover grandes cantidades de datos entre instalaciones hacia S3, EFS, FSx
- Informatica y redes
	- Instancias EC2: Optimizadas para CPU y GPU
	- Grupos de colocacion EC2: Cluster para buen rendimiento de red
	- ![[Pasted image 20260630193629.png]]
	- Redes mejoradas EC2 (SR-IOV): Mayor ancho de banda, mayor paquetes por segundo y menor latencia
		- ENA (Elastic Network Adapter) hasta 100 GBps
		- Intel 82599 VF haasta 10 gbps - legacy
	- Adaptador Elastic Fabric (EFA)
		- ENA mejorado para HPC, solo funcional en linux
		- Excelente para comunicaciones entre nodos y cargas de trabajo estrechamente acopladas
- Almacenamiento
	- Conectado a la instancia
		- EBS: hasta 256.000 IOPS con io2 block express
		- Instance Store: escala a millones de IOPS, vinculado a la instancia EC2, baja latencia
	- Almacenamiento de red
		- S3: blob grande, no es un sistema de archivos
		- EFS: escala IOPS en funcion del size total, o utiliza IOPS provisionadas
		- FSx para lustre: Sistema de archivos distribuido optimizado para HPC que escala a millones de IOPS
- Automatizacion y orquestacion
	- AWS Batch: permite soportar multiples trabajos en paralelo multinodo
	- AWS ParallelCluster: Herramienta de gestion de cluster de codigo abierto para implementar HPC en AWS


	## Quiz
Question 1:

Estás trabajando en una aplicación sin servidor en la que quieres procesar objetos subidos a un bucket de S3. Has configurado S3 Events en tu bucket de S3 para invocar una función Lambda cada vez que se sube un objeto. Quieres asegurarte de que los eventos que no se pueden procesar se envíen a una cola de letra muerta (DLQ) para su posterior procesamiento. ¿Qué servicio de AWS debes utilizar para configurar la DLQ?
- La funcion lambda es asincrona, por lo que el DLQ debe establecerse en el lado de la funcion.

Question 2:

Como Arquitecto de Soluciones, has creado una arquitectura para una empresa que incluye los siguientes servicios de AWS CloudFront, Web Application Firewall (AWS WAF), AWS Shield, Application Load Balancer e instancias EC2 gestionadas por un Auto Scaling Group. A veces, la empresa recibe solicitudes maliciosas y quiere bloquear estas direcciones IP. Según tu arquitectura, ¿dónde deberías hacerlo?
- AWS WAF, cloudfront libera ips publicas de cloudfront por ende una vez sale del mismo no es interceptable la IP origen.

Question 3:

Tus instancias EC2 están desplegadas en un Grupo de Colocación de Clústeres para llevar a cabo una Computación de Alto Rendimiento (HPC). Te gustaría maximizar el rendimiento de la red entre tus instancias EC2. ¿Qué deberías utilizar?
- Al buscarse el maximo rendimiento sin lugar a dudas la version mejorada para HPC de EL Elastic Network Adapter -> Elastic Fabric Adapter es la eleccion, si simplemente se buscase mejorar el ancho de banda de el cluster de instancias EC2 podria optarse por Elastic Network Adapter sin dudas.