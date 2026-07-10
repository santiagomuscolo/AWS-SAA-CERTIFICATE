## Introduccion
1. Whitepaper de well architected framework
2. Herramienta Well Architected
3. Asesor de confianza de AWS
4. Recursos de arquitecturas de referencia 

## Well Architected Framework
Well architected es un principio que nos ayuda a construir infraestructuras resilientes, seguras, performantes y eficientes para una variedad de flujos de trabajo y aplicaciones.

**Well Architected Pilars**
Well architected se basa en 6 pilares claves siendo estos, la excelencia operacional, la seguridad, la reliabilidad, la eficiencia en la performance, la optimizacion de costos y la sostenibilidad

**Well architected Framework**
Well architected framework nos describe conceptos clave, principios de diseño y buenas practicas de arquitetura para diseñar flujos de trabajo que funcionen en el cloud.

**AWS Well Architected Tool**
Well architected tool nos proporciona una herramienta muy util para analizar nuestras arquitecturas en base a los 6 pilares previamente nombrados y asi adoptar mejores practicas.

Como funciona?
- Seleccionamos una carga de trabajo y respondemos unas preguntas
- Revisamos nuestras respuestas en comparacion a los 6 pilares
- Obtenemos asesoramiento con videos y documentacion, generamos un informa y vemos los resultados en un dashboard

## AWS Trusted Advisor
Este servicio de AWS nos permite realizar un analisis high level de nuestras cuentas de aws y proporcionando recomendaciones hasta en 5 categorias.

sirve para:
- Optimizacion de costes
- Rendimiento
- Seguridad

![[Pasted image 20260710184024.png]]

**Planes de soporte**
1. 7 Core checks (basic & developer)
	- Permisos de buckets s3
	- Security groups
	- Uso de IAM
	- MFA en la cuenta root
	- EBS Public snapshots
	- RDS Public snapshots
	- Service Quotas
2. Full checks (business & enterprise)
	- Comprobaciones completas disponibles en las 5 categorias
	- Posibilidad de establecer alarmas de cloudwatch cuando se alcanzan los limites
	- Acceso programado mediante la AWS support API

**categorias**
- Optimizacion de costos
- Rendimiento
- Seguridad
- Tolerancia a errores
- Cuotas de servicio

## Mas ejemplos de arquitecturas
LINKS IMPORTANTISIMOS PARA EL USO DIARIO:
1. https://aws.amazon.com/architecture/
2. https://aws.amazon.com/solutions/

## Quiz
Question 1:

¿Qué servicio de AWS analiza tu cuenta de AWS y da recomendaciones para la optimización de los costes, el rendimiento, la seguridad, la tolerancia a los fallos y los límites del servicio?
- AWS Trusted Advisor