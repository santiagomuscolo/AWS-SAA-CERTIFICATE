
## Examen 1

Preguntas erradas: 13
Preguntas acertadas: 52

Revision de preguntas erradas:
1. Una empresa aloja todas sus aplicaciones en su centro de datos en la costa este de EE.UU. La mayoría de las cargas de trabajo son aplicaciones heredadas que se ejecutan en máquinas virtuales individuales con sistemas operativos Linux y Windows. La empresa planea migrar todas sus cargas de trabajo en máquinas virtuales a la nube de AWS. Para minimizar cambios en las aplicaciones durante el proceso de migración, se ha decidido utilizar una estrategia de 'lift-and-shift'. Además, la empresa desea minimizar el tiempo de inactividad durante la migración. ¿Cuál de las siguientes opciones debe implementar el arquitecto de soluciones para este escenario?
	 Aqui yo indique el uso de data sync pero no es lo correcto, en realidad la pregunta nos ofrecia una opcion que sugeria la utilizacion de un AWS Replication Agent instalado en las maquinas virtuales objetivo, esto reduce sugerentemente el tiempo de inactividad y permite la replicacion exacta en AWS

2. Una empresa de monitoreo de seguridad desea registrar detalles del tráfico que fluye hacia un Elastic Load Balancer (ELB) en su entorno de producción. Necesitan capturar información precisa como dirección IP de origen, puerto de destino y protocolo utilizado, para cumplir con sus políticas de auditoría y análisis de red. ¿Cuál es la opción más confiable y segura para recopilar esta información?
	 Si bien aca comprendi que nos referiamos a VPC logs se ofrecian dos posibles niveles, a nivel ENI (Elastic Network Interface) o a nivel subred, la realidad es que si disponemos de el ENI conviene meter el vpc flow log ahi, ya que la informacion que se solicita abarca varias capas

3. Una empresa global de e-learning que transmite clases en vivo y almacena grabaciones bajo demanda está expandiendo su infraestructura. Necesita una base de datos que escale automáticamente durante eventos masivos (como lanzamientos de cursos), sea altamente disponible, tolerante a fallos y permita realizar cambios frecuentes en el modelo de datos sin afectar el rendimiento. También requiere tiempos de respuesta en milisegundos para miles de usuarios accediendo simultáneamente. ¿Cuál es la solución de base de datos más adecuada para cumplir con estos requisitos?
	 Aqui la clave estaba en  "cambiar el modelo de datos sin afectar el rendimiento", esto produjo que la opcion que yo elegi que era Aurora sea descartada, en su lugar la opcion correcta era DynamoDB con replicacion global.

4. Una organización almacena y gestiona registros financieros de varias empresas en su centro de datos on-premises, el cual está casi sin espacio disponible. La administración ha decidido migrar todos los registros existentes a un servicio de almacenamiento en la nube. Todos los registros financieros futuros también se almacenarán en la nube. Para mayor seguridad, los registros deben estar protegidos contra eliminaciones o sobrescrituras.  ¿Cuál de las siguientes opciones debes implementar para cumplir con este requisito?
	 Aqui elegi storage gateway y fue un error, ese servicio requiere de una solucion muy personalizada, en su contraparte DataSync nos ofrece una migracion de datos directa y con menor esfuerzo guardando los datos en S3 que es el mejor servicio de almacenamiento que dispone AWS bajo el object lock para no poder borrar los registros.
