## CloudFormation
CloudFormation es una forma declarativa de esbozar tu infraestructura de AWS para cualquier recurso mediante IaC (Infraestructure as code) mediante plantillas XML.

**Ventajas**
- Infraestructura como codigo
	- No se crean recursos manualmente, lo que es excelente para control
	- Los cambios en la infraestructura se revisan a traves del codigo
- Coste
	- Cada recurso dentro de la pila esta etiquetado con un identificador para que pueda revisarse cuanto cuesta la misma
	- Pueden estimarse costes de los recursos en base a plantillas de cloudformation
	- Existen estrategias de ahorro
- Productividad
	- Posibilidad de destruir y volver a crear infraestructura en el cloud sobre la marcha
	- Generacion automatizada de diagramas para las plantillas
	- Programacion declarativa (no es necesario averiguar el orden y la orquestacion)
- No es necesario crear de 0 plantillas
	- Existen muchas plantillas en la web y bien documentadas

**Stack designer**
CloudFormation nos permite diseñar nuestra pila y ver el diagrama y las relaciones entre los componentes, por ejemplo:
![[Pasted image 20260702202248.png]]

## AWS SES (Simple Email Service)
Simple Email Service es un servicio totalmente gestionado para enviar correos electronicos de forma segura, global y a escala, permitiendo correos entrantes y salientes, dahsboards de reputacions, perspectivas de rendimiento e informacion antispam.
![[Pasted image 20260703174921.png]]
## Amazon Pinpoint
Amazon pinpoint nace como la nueva generacion para gestion de marketing que antes se realizaba con SNS o SES, pinpoint como tal es un servicio de comunicaciones de marketing bidireccional (entrante/saliente), soporta correos electronicos, SMS, push, voz y mensajeria in-app ofreciendo la posibilidad de segmentar y personalizar lso mensajes con el contenido adecuado para los clientes.

**En que caso podria aplicar pinpoint?**
Un ejemplo podria ser la realizacion de campañasenviando mensajes SMS de marketing, masivos y transaccionales.

**Cual es la diferencia con SNS o SES?**
La principal diferencia es que en pinpoint podemos crear plantillas de mensajes, horarios de entrega, segmentos altamente segmentados y campañas completas, mientras que en SNS y SES se debe gestionar la audiencia, el contenido y el calendario de entrega de cada mensaje.

Pongamoslo de otro modo:
SNS: es un pub/sub destinado a el manejo de eventos/broadcast de mensajeria.
SES: es un servicio low level para el envio de correos electronicos via SMTP/API.
Pinpoint: es un servicio high-level destinado a marketing + engagement multicanal.

![[Pasted image 20260703175628.png]]

## AWS Systems Manager (SSM)
Systems Manager es un servicio hibrido que te permite gesonar tus sistemas EC2 y On-Premise a escala, obteniendo datos operativos del estado de la infraestructura de los mismos.

El mismo permite:
- Automatizar parches para mejorar la normativa
- Ejecutar comandos en toda la flota de servidores
- Almacenar la configuracion de parametros con el almacen de parametros SSM

**Como funciona?**
Para usar SSM es necesario instalar el agente SSM en las instancias que controlamos, se instala por defecto en AMIs de Amazon Linux y en algunas AMI de ubuntu
![[Pasted image 20260706142448.png]]

## AWS Cost Explorer
Cost explorer nos permite visualizar, entender y gestionar costes en AWS a lo largo del tiempo, creando infomres personalizados, analizando datos a alto nivel y obteniendo informacion con granularidad mensual, por horas a nivel de recursos
![[Pasted image 20260706144723.png]]

Hay una feature muy interesante de este servicio que se llama "Prevision de uso" o en ingles "Usage forecast" que infiere mediante nuestros gastos mensuales lo **posibles** gastos de los proximos meses.
![[Pasted image 20260706144952.png]]

## AWS Transcoder
Este servicio de AWS nos permite convertir archivos multimedia almacenados en S3 a archivos multimedia con un formato soportado por la diversidad de dispositivos que existen (tablets, computadores, etc...).

ventajas
- Facil de usar
- Altamente escalable - puede manejar grandes volumenes de archivos multimedia y archivos de gran size
- Rentable: modelo de precios basado en la duracion

![[Pasted image 20260706145319.png]]