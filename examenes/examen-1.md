
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

13. Un arquitecto de soluciones está diseñando una infraestructura de alta disponibilidad para una aplicación web alojada en instancias EC2 dentro de un Auto Scaling Group. Uno de los requisitos clave es garantizar que los datos almacenados en los volúmenes raíz de EBS no se eliminen si una instancia se termina. ¿Qué configuración debe aplicarse para cumplir con este requisito?
	 aqui yo coloque el uso de aws backup pero fue equivoco ya que EBS ofrece un atributo para evitar su borrado tras la finalizacion de la instancia este es denominado "deleteOnTermination"

**intento 2**

Preguntas erradas: 8
Preguntas acertadas: 65

1. Una firma de abogados internacional necesita almacenar documentos confidenciales de sus clientes en Amazon S3. Debido a políticas de cumplimiento corporativas y normativas legales internacionales, las claves maestras utilizadas para el cifrado deben ser controladas y almacenadas completamente fuera del entorno de AWS. Además, se requiere que los archivos estén cifrados antes de ser cargados al almacenamiento en la nube. ¿Qué técnica de cifrado de S3 debe utilizar el Solutions Architect?
	 Aqui coloque configurar SSE con claves proporcionadas por el cliente, pero esto deja dependencia en la nube, lo ideal es utilizar un cifrado del lado del cliente completamente.


2. Una empresa planea migrar una base de datos NoSQL a una instancia de EC2. La base de datos está configurada para replicar automáticamente los datos y mantener múltiples copias para redundancia. El arquitecto de soluciones necesita lanzar una instancia que tenga altos IOPS y acceso de lectura/escritura secuencial. ¿Cuál de las siguientes opciones cumple con el requisito si el rendimiento de E/S es la máxima prioridad?
	 Aqui coloque "Usar una instancia optimizada para memoria con volumenes EBS", no tiene sentido ya que en realidad para altos volumenes de IOPS se recomiendan instancias optimizadas para almacenamiento y instance store ofrece esto y una baja latencia.

3. Una empresa ha lanzado una aplicación de múltiples niveles. Tanto el nivel web como el nivel de base de datos se ejecutan en instancias de Amazon EC2 dentro de subredes privadas en la misma Zona de Disponibilidad (AZ). El equipo necesita rediseñar la arquitectura para agregar alta disponibilidad (HA) y tolerancia a fallos. ¿Cuál combinación de pasos debe tomar un arquitecto de soluciones para cumplir este requisito? (Selecciona DOS.)
	 aca siempre hay que pensar en multi AZ para una alta disponibilidad, por lo que lo mejor en los casos ofrecidos era: "Crear un ASG de EC2 y un ALB que abarque multiples AZs" y "Crear nuevas subredes privadas en la misma VPC pero en una AZ diferente. Migrar al base de datos a un despliegue multi-AZ de amazon RDS", creo que la confusion viene por el temos a la palabra "migracion" cuando no necesariamente representa algo malo, solamente hay que pensar en como cumplir el requisito.


4. Una empresa aloja todas sus aplicaciones en su centro de datos en la costa este de EE.UU. La mayoría de las cargas de trabajo son aplicaciones heredadas que se ejecutan en máquinas virtuales individuales con sistemas operativos Linux y Windows. La empresa planea migrar todas sus cargas de trabajo en máquinas virtuales a la nube de AWS. Para minimizar cambios en las aplicaciones durante el proceso de migración, se ha decidido utilizar una estrategia de 'lift-and-shift'. Además, la empresa desea minimizar el tiempo de inactividad durante la migración. ¿Cuál de las siguientes opciones debe implementar el arquitecto de soluciones para este escenario?
	 Aqui puse AWS Application discovery, es un servicio erroneo para este caso en realidad lo que se busca es replicar una arquitectura, por lo que es mas optimo el uso de un AWS Replication Agent, ademas application discovery se utiliza para rastrear el estado de migracion de aplicaciones on-premises desde el migration hub.

5. Se ha lanzado una instancia EC2 bajo demanda dentro de una subred privada en una VPC. La Network ACL asociada a esta subred permite todo el tráfico entrante, pero deniega todo el tráfico saliente. El grupo de seguridad de la instancia tiene una regla de entrada que permite conexiones SSH desde cualquier dirección IP, pero no tiene reglas de salida configuradas. En este contexto, ¿qué cambio debe realizarse para que sea posible establecer una conexión SSH a la instancia EC2?
	 Aqui necesita si o si modificarse el grupo de seguridad y el NACL para un correcto flujo.

6. Una agencia espacial privada necesita procesar imágenes satelitales de ultra alta resolución en instancias Amazon EC2. Para realizar los cálculos intensivos, requiere un almacenamiento en bloque de alto rendimiento cuyos datos persistan independientemente del ciclo de vida de las instancias. Los archivos originales deben almacenarse en un servicio de almacenamiento de objetos. Después de 60 días, deben trasladarse automáticamente a una clase de almacenamiento de archivo de bajo costo para su conservación a largo plazo. Aunque el acceso será poco frecuente, la agencia debe poder recuperar los archivos en pocas horas y disponer de una opción de recuperación acelerada en cuestión de minutos cuando sea necesario. ¿Qué solución cumple estos requisitos?
	 Aqui puse volumenes instance store temporales, no es necesario... un volumen EBS disponible para procesamiento constante luego S3 y S3 flexible retrieval con politicas de ciclo de vida es lo mas optimo, el volumen temporal no sirve.


7. Una empresa de comercio electrónico quiere optimizar la entrega de imágenes en su sitio web utilizando Amazon CloudFront. Para mejorar la experiencia del usuario, la empresa planea servir imágenes en formato AVIF a los navegadores que lo admitan y en formato PNG para aquellos que no lo soporten. Adicionalmente, desean agregar un encabezado personalizado en la respuesta para realizar un mejor seguimiento del tráfico de imágenes. Como arquitecto de soluciones, ¿qué estrategia recomendarías para cumplir con estos requisitos minimizando la sobrecarga operativa?
	 Cloudfront ofrece lambda@edge, por lo que es lo mejor para modificar encabezados y servir el formato correcto en base a los mismos, yo habia elegido Una integracion con fargate, se podia hacer pero era mas complejo a nivel operativo.

8. Una organización de servicios financieros está desarrollando una aplicación nativa en la nube en AWS para procesar y analizar datos de transacciones de clientes. La aplicación utiliza Amazon Aurora como base de datos, Amazon EFS para almacenamiento de archivos y Amazon EventBridge para activar AWS Step Functions en la orquestación de flujos de trabajo. La organización ha implementado AWS IAM Identity Center para la autenticación de usuarios. Los equipos de ciencia de datos, ingeniería y cumplimiento requieren acceso seguro a Amazon Aurora y Amazon EFS, manteniendo altos estándares de privacidad de datos. La solución debe adherirse al principio de privilegio mínimo y minimizar la sobrecarga administrativa. ¿Qué enfoque satisface mejor estos requisitos?
	 Aqui opte por control tower, sin embargo, AWS Identity Center es el servicio nombrado en uso y puede ese mismo servicio cumplir con el requerimiento de minimo privilegio por roles asignados a grupos sin la necesidad de integrar un servicio nuevo.