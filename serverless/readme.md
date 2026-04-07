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