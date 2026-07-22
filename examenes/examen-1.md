
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


5. Una empresa ha alojado una aplicación web en una instancia Amazon EC2 con Linux en una subred pública.

- La instancia utiliza un grupo de seguridad por defecto.
- Tiene una dirección IP elástica (EIP) asignada.
- El Network ACL está configurado para bloquear todo el tráfico entrante y saliente.

El arquitecto de soluciones debe permitir tráfico entrante en el puerto 443 para que los usuarios puedan acceder a la aplicación desde cualquier lugar.

¿Cuáles de los siguientes pasos cumplirán con este requisito? (Selecciona DOS)

aca marque cosas correctas como establecer la regla conexion tcp del SG pero le erre al establecer como deberia permitirse la regla de entrada y salida del NACL, ya que si, el puerto entrante proviene del origen 0.0.0.0/0 en el puerto 443 (HTTPS) pero la saliente va al destino 0.0.0.0/0 desde los puertos efimeros no 443.


6. Una empresa ha desarrollado una aplicación que se ejecuta en AWS Fargate y utiliza Amazon RDS con Multi-AZ para su base de datos. El equipo de seguridad ha solicitado que todas las credenciales de la base de datos, claves API y otros secretos estén cifrados y se roten periódicamente para minimizar riesgos de seguridad. La aplicación debe poder recuperar automáticamente las credenciales actualizadas sin necesidad de redeploys manuales. ¿Cuál de las siguientes opciones es la más adecuada para cumplir con estos requisitos?
	 Punto clave aca por el que me confundi, pide guardar los datos cifrados, esto cambia todo el margen de la respuesta y systems manager parameter store deja de ser el ideal, pasando a tomar credito el secrets manager con rotacion de claves.

7. Un hospital tiene una aplicación de misión crítica que utiliza una API RESTful impulsada por Amazon API Gateway y AWS Lambda. Los médicos suben informes en formato PDF al sistema, que luego se almacenan como contenido de medios estáticos en un bucket de Amazon S3. El equipo de seguridad desea mejorar la visibilidad en cuanto a ciberataques y garantizar el cumplimiento de HIPAA (Ley de Portabilidad y Responsabilidad del Seguro Médico). La empresa busca una solución que monitoree continuamente las operaciones a nivel de objeto en S3 e identifique información de salud protegida (PHI) en los informes, con un impacto mínimo en la función Lambda existente. ¿Cuál de las siguientes soluciones cumple con estos requisitos con el menor sobrecarga operativa?
	 Aca la confusion fue muy basica, la pregunta dice "con un impacto minimo en la funcion lambda", no con un impacto 0... provocando que la opcion de usar textract y crear una nueva func lambda sea equivoca. por su lado usar textract para la extraccion del texto a pdf y integrar comprehend medical en la lambda para identificar phi en el texto extraido era la correcta.
 

8. Un arquitecto de soluciones está administrando una aplicación que se ejecuta en una instancia Windows EC2 con un Amazon FSx for Windows File Server adjunto. Para reducir costos, la empresa ha decidido detener la instancia fuera del horario laboral y reiniciarla solo cuando sea necesario. Se ha observado que la aplicación tarda varios minutos en estar completamente operativa, lo que impacta en la productividad. ¿Cómo puede el arquitecto de soluciones acelerar el tiempo de carga de la instancia sin aumentar los costos?
	 para hacer esto lo ideal era, migrar a una EC2 y poner el modo hibernacion reduciendo costsos y acelerando el tiempo de carga, pese a ser una EC2 no se puede activar el modo 'hibernacion' sobre una instancia existente.


9. Una organización con múltiples equipos de desarrollo ha desplegado una variedad de recursos en AWS para distintas iniciativas de innovación. Cada equipo tiene autonomía para crear recursos según sus necesidades. Para evitar interrupciones operativas, la organización quiere monitorear proactivamente el uso de sus servicios en AWS y prevenir que se superen los límites de cuota establecidos. ¿Qué combinación de acciones debería implementar el arquitecto de soluciones para satisfacer estos requerimientos? (Selecciona DOS)
	 Punto clave, aca acertamos en EventBridge + cloudwatch + sns pero le erramos en la revision de trusted advisor, por su lado lo mejor era una lambda que actualice las verificaciones de service limits en trusted advisor y se ejecute cada 24h

10. Una startup ha desarrollado un sitio web informativo que solo contiene contenido estático y lo ha alojado en un bucket de Amazon S3. Para que el sitio web esté accesible a través de su dominio personalizado recientemente adquirido en Route 53, el equipo necesita configurar correctamente el enrutamiento de tráfico. ¿Cuáles son los requisitos previos para permitir que Amazon Route 53 dirija tráfico al sitio web alojado en un bucket de Amazon S3? (Selecciona DOS)
	 Aca acerte en el registro del dominio en route 53 pero luego seleccione modificar las politicas de acceso del bucket s3 ppara el acceso publico y es un error la respuesta correcta era colocarle el mismo nombre que el dominio registrado al bucket, esto nace como requerimiento de S3 y para que sea accesible se crea un ALIAS para el bucket en route 53.

11. Una empresa planea desarrollar un servicio de mensajería personalizado que será utilizado para entrenar una IA con una función de respuesta automática. Se espera que el servicio reciba miles de mensajes por día, los cuales serán procesados por un clúster de Amazon EMR. Es crucial que ninguno de los mensajes se pierda, que no se generen duplicados y que los mensajes sean procesados en EMR en el mismo orden en que llegaron. ¿Cuál de las siguientes opciones puede cumplir con este requisito?
	 Aca seleccione SQS, gran error por que los mensajes si se pueden perder y en una cola estandar no garantizamos un orden, con kinesis no perdemos datos y los ordenamos con las claves de particion.

12. Una empresa ha desarrollado APIs públicas que se ejecutan en un grupo de Auto Scaling de instancias Amazon EC2 detrás de un Elastic Load Balancer. Estas APIs serán consumidas por clientes externos desde redes on-premises que solo pueden acceder a direcciones IP estáticas aprobadas en sus firewalls. El arquitecto de soluciones ha sido asignado para garantizar que los clientes puedan acceder a las APIs sin interrupciones. ¿Qué solución debe implementar?
	 aqui yo puse global accelerator pero lo correcto seria implementar un NLB antes del ALB que maneje las IPs estaticas y configurarlo como balanceador de carga de la red ya que ese tipo de balancer nos permite un control fino sobre las mismas que el ALB no permite.
	 Global accelerator proporciona IPs estaticas pero esta pensado para optimizar el enrutamiento global, no para asignar direcciones IP estáticas a balanceadores de carga dentro de una región.

