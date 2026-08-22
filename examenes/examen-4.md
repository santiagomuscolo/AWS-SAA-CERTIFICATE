## Examen 4

1. Una empresa de e-learning almacena sus materiales educativos en Amazon S3 y utiliza CloudFront para distribuir los archivos a estudiantes en diferentes regiones. Actualmente, los estudiantes pueden acceder a los archivos directamente desde las URL de S3 o mediante CloudFront. El arquitecto de soluciones debe garantizar que solo los estudiantes autorizados accedan a los archivos y que el contenido solo se sirva a través de CloudFront para mejorar la seguridad y la distribución de los materiales. ¿Qué combinación de acciones debe implementar el arquitecto para cumplir con estos requisitos? (Selecciona DOS)
	 Para conseguir este nivel de seguridad pueden utilizarse URLs firmadas y cookies firmadas que le brinden acceso unicamente a los usuarios autorizados, ademas se puede configurar una Identidad de acceso de origen (OAI) en cloudfront y otorgarle los permisos para poder leer dichos archivos.

2. Una plataforma global de streaming de contenido ha desplegado su infraestructura en AWS y espera un tráfico masivo de usuarios conectándose desde diversas regiones del mundo. El servicio debe estar disponible las 24 horas del día, los 7 días de la semana, sin interrupciones. Además, la arquitectura debe ser lo suficientemente resiliente para soportar la caída de una región completa de AWS sin afectar la experiencia del usuario. Para cumplir con este requisito, el arquitecto de soluciones ha distribuido los recursos en múltiples regiones de AWS y necesita configurar Route 53 para garantizar la máxima disponibilidad. Cuando un recurso se vuelva no saludable, Route 53 debe detectarlo y dejar de incluirlo en las respuestas a las consultas DNS. ¿Cuál de las siguientes configuraciones de enrutamiento es la más tolerante a fallos que debería utilizar el arquitecto de soluciones en este escenario?
	 En active-active todos los recursos deben estar disponibles al mismo tiempo por lo que configurar un active-active con enrutamiento ponderado es una solucion adecuada ya que automaticamente se descartarian recursos no saludables y permanecerian los que si lo estan. 


3. Un arquitecto de soluciones está diseñando la infraestructura para una nueva aplicación serverless basada en contenedores. La aplicación se ejecutará a partir de una imagen de Docker almacenada en Amazon Elastic Container Registry (ECR) y requiere al menos 8 GB de almacenamiento temporal durante su ejecución. ¿Qué servicio satisface mejor estos requisitos?
	 Ejecutar la aplicacion en un cluster de amazon ECS con fargate es ideal para aplicaciones serverless que ademas provee de la memoria necesaria con un almacenamiento efimero de hasta 20 GiB.

4. Un arquitecto de soluciones está escribiendo una función AWS Lambda que procesará documentos cifrados desde un sistema de archivos Amazon FSx for NetApp ONTAP. Los documentos están protegidos con una clave de cliente de AWS KMS. Después de procesar los documentos, la función Lambda almacenará los resultados en un bucket de S3 con la clase de almacenamiento Amazon S3 Glacier Flexible Retrieval. El arquitecto de soluciones debe asegurarse de que la función Lambda pueda descifrar los archivos. ¿Qué acción cumple con este requisito?
	 Para comenzar deberiamos establecer el permiso en el rol de ejecucion de la funcion lambda "kms:decrypt" para que la misma pueda realizar la accion de desencriptado, luego agregar los permisos en KMS para que el rol de ejecucion pueda llevar a cabo dicha accion.
	 Como punto clave: para que lambda pueda interactuar con otros servicios de AWS lo que debe modificarse son los permisos de su rol de ejecucion, esto es crucial, ya que la politica en si de lambda solo indica con que otros servicios puede interactuar no que accion puede realizar.

5. Una empresa aeroespacial ha adoptado recientemente una infraestructura híbrida en la nube con AWS. Una de las tareas del arquitecto de soluciones es lanzar una VPC con subnets públicas y privadas para sus instancias EC2, así como para sus instancias de base de datos. ¿Cuáles de las siguientes afirmaciones son verdaderas con respecto a las subnets en una VPC de Amazon? (Selecciona DOS)
	 Hay dos verdades aqui, la primera es que cada subnet se asigna a una unica AZ y la segunda es que cada vez que una subnet es creada es asociada automaticamente a la tabla de rutas principal de la vpc (esto significa que a no ser que asociemos de forma manual una subnet a una tabla de rutas diferente siempre usara por default la principal)

6. Una empresa almacena reportes financieros y documentos legales en un bucket de Amazon S3. Para cumplir con una auditoría de seguridad, se ha asignado al arquitecto de soluciones la tarea de rastrear todos los objetos nuevos agregados al bucket y aquellos eliminados. También debe detectar cuando un objeto versionado es eliminado permanentemente. El arquitecto debe configurar Amazon S3 para enviar notificaciones de estos eventos a una cola para procesamiento automático y a un tema de SNS para notificar al equipo de cumplimiento. ¿Cuál de las siguientes opciones es la más adecuada para que el arquitecto implemente?
	 Aqui se puede crear un nuevo Amazon SNS topic y una Amazon SQS queue. Agregar una configuración de notificación de eventos en el bucket de S3 para publicar los eventos s3:ObjectCreated: y s3:ObjectRemoved:Delete en SQS y SNS

7. Una startup de tecnología financiera ha implementado una API de pagos en AWS. - El backend de la API se ejecuta en instancias Amazon EC2 dentro de un Auto Scaling Group.
- Los datos de clientes y transacciones se almacenan en un clúster Amazon Aurora for PostgreSQL.
- Por razones de cumplimiento y seguridad, es obligatorio almacenar de forma cifrada las claves API, tokens de acceso y credenciales de la base de datos.
- La solución debe ser escalable y minimizar los costos operativos. 
¿Cuál de las siguientes opciones es la mejor solución para cumplir con estos requisitos?
	 En cuanto a la minimizacion de costos operativos parameter store con secure string nos ofrece la posibilidad de guardar las claves cifradas correspondientes, con secrets manager esto tiene un coste extra por lo que no seria la opcion viable (ademas de que se indica de hacer una req en cada solicitud para obtener las credenciales jaja), por ende al no ser credenciales de bases de datos que necesitan rotacion automatica parameter store es ideal

8. Una empresa de análisis de datos financieros almacena grandes volúmenes de registros históricos en Amazon S3 con una política de ciclo de vida que los mueve a Glacier cada 60 días. Por regulaciones de cumplimiento, la empresa debe garantizar que, en caso de auditoría, cualquier dato solicitado pueda recuperarse en menos de 10 minutos con un rendimiento de al menos 100 MB/s. ¿Qué opciones debes implementar para cumplir con este requisito? (Selecciona DOS)
	 1- **Usar Expedited Retrieval para acceder a los datos financieros**: expedited retrieval nos permite recuperar datos de glacier en un periodo de 1 a 5 minutos lo que satisface el requisito de tiempo.
	 2- **Comprar capacidad de recuperación provisionada**: esto permite que las expedited retrievals esten siempre disponibles, proporcionando tres retrievals aceleradas cada cinco minutos con un rendimiento de hasta 100 MB/s

9. En Amazon EC2, puedes administrar tus instancias desde el momento en que las inicias hasta su terminación. Para optimizar costos, es importante conocer cómo se facturan los distintos estados de una instancia EC2. ¿Cuáles de las siguientes afirmaciones son correctas con respecto a la facturación de EC2? (Selecciona DOS)
	 Las instancias en estado terminated o estado stopping por hibernacion se seguiran cobrando.

10. Una gran empresa de ciberseguridad necesita configurar un bastion host en Linux para permitir el acceso a las instancias de Amazon EC2 que se ejecutan en su VPC. Por razones de seguridad, solo los clientes que se conecten desde la dirección IP pública externa de la empresa (192.168.45.200) deben tener acceso SSH al host. ¿Cuál es la mejor opción para cumplir con este requisito?
	 Aqui puede configurarse una capa temprana de seguridad en la ACL TCP con rango de puertos 22 de origen 192.168.45.200/0

11. Una empresa de comercio electrónico utiliza Auto Scaling para gestionar la capacidad de su infraestructura en AWS. Recientemente, el equipo de operaciones ha creado una nueva Amazon Machine Image (AMI) con mejoras de seguridad y rendimiento. El equipo ahora necesita asegurarse de que todas las nuevas instancias EC2 lanzadas a través del Auto Scaling Group utilicen esta nueva AMI. ¿Qué cambio debe realizarse?
	 Aqui tenemos el mismo target group por lo que debemos simplemente crear el nuevo launch template y asociarlo al ASG para que todo lo nuevo que se lance lo utilice.

12. Una empresa de comercio electrónico ha desplegado su base de datos en Amazon Aurora con una instancia db.r5.large. Durante la mayor parte del día, el rendimiento es estable, pero en eventos de alta demanda, como el Black Friday, la base de datos no escala lo suficientemente rápido para manejar el tráfico masivo de usuarios. El arquitecto de soluciones debe migrar la base de datos a Aurora Serverless con el menor tiempo de inactividad posible para garantizar la continuidad de la aplicación. ¿Qué estrategia debe implementarse para cumplir con este requisito?
	 Aqui se debe utilizar AWS DMS (database migration service) para migrar de el cluster Aurora a un cluster Aurora serverless con replicacion en tiempo real.

13. Una empresa está desplegando una aplicación de análisis en AWS Fargate. La aplicación requiere almacenamiento conectado que ofrezca acceso concurrente a archivos y alto rendimiento. ¿Qué opción de almacenamiento debe recomendar el arquitecto de soluciones?
	 Para acceso concurrente mediante un sistema de archivos EFS es la solucion adecuada, ya que ademas de esto ofrece un alto rendimiento.

14. Una empresa de tecnología ha estado ejecutando varias instancias EC2 reservadas para alojar una plataforma de análisis de datos. Debido a un cambio en los objetivos de negocio, la plataforma fue descontinuada la semana pasada. La empresa ahora busca minimizar costos y deshacerse de estas instancias reservadas lo antes posible. ¿Qué acciones rentables debe tomar el equipo de AWS para optimizar los costos en esta situación? (Selecciona DOS)
	 Aqui lo primero es terminar las instancias reservadas para evitar que una vez pasado el tiempo de reserva sean facturadas como on-demand, en adicion, publicarlas en el marketplace para poder venderlas.

**Intento 2**

Incorrectas: 4

1. Una empresa está implementando un entorno de Microsoft Dynamics CRM en AWS utilizando CloudFormation. El arquitecto de soluciones necesita instalar y configurar la arquitectura, que incluye controladores de dominio de Microsoft Active Directory (AD), Microsoft SQL Server 2016 y múltiples instancias de Amazon EC2 para alojar Microsoft Dynamics CRM y otras dependencias. El arquitecto debe asegurarse de que los componentes requeridos se estén ejecutando correctamente antes de que CloudFormation continúe con la creación de la pila. ¿Cuál de las siguientes opciones debe configurar el arquitecto para cumplir con este requisito?
	 Configurar un atributo CreationPolicy en la instancia dentro de la plantilla de CloudFormation. Enviar una señal de éxito después de que las aplicaciones estén instaladas y configuradas utilizando el script auxiliar cfn-signal es la mejor opcion ya que crearemos a partir de la signal de exito cfn-signal para poder completar la pila.

2. Una nueva plataforma de banca en línea ha sido rediseñada para tener una arquitectura de microservicios en la que las aplicaciones complejas se descomponen en servicios más pequeños e independientes. La nueva plataforma utiliza Kubernetes y los contenedores de la aplicación están optimizados para ejecutar servicios pequeños y desacoplados. La nueva solución debe eliminar la necesidad de aprovisionar y administrar servidores, permitir especificar y pagar solo por los recursos utilizados por la aplicación y mejorar la seguridad mediante aislamiento a nivel de aplicación. ¿Cuál de las siguientes es la solución más adecuada para implementar esta nueva plataforma en AWS?
	 Siempre que hablamos de Kubernetes hablamos en aws de su version EKS (Elastic Kubernetes service), en este caso para evitar la necesidad de aprovisionamiento (Creacion de recursos de forma manual) puede utilizarse un grupo de auto scaling para la paltaforma bancaria basada en contenedores con AWS Fargate en amazon EKS.

3. Una empresa está utilizando 10 cuentas de AWS consolidadas mediante AWS Organizations. Desean copiar más de 500 objetos de un bucket de S3 a otro bucket de S3 que pertenece a una cuenta diferente dentro de la misma organización. El arquitecto de soluciones recibió instrucciones para configurar los permisos necesarios para esta tarea y garantizar que la cuenta de destino sea la propietaria de los objetos copiados, en lugar de la cuenta de origen. ¿Cómo puede el arquitecto lograr este requisito?
	 Aqui siempre que se quiere compartir recursos entre buckets de S3 de diferentes cuentas, lo ideal es configurar permisos y politicas IAM entre las cuentas para poder copiar el contenido de un bucket al otro.

4. Un servidor on-premises utiliza un compartido de archivos SMB para almacenar datos de la aplicación.

- La aplicación genera 50 MB de datos por día, pero solo necesita acceder a algunos de ellos para procesos diarios.
- Para reducir costos, la empresa planea migrar todos los datos a AWS, pero quiere mantener acceso de baja latencia, similar al compartido de archivos local.
- La empresa no tiene la capacidad de desarrollar una solución personalizada para esta integración.

¿Qué servicio de AWS debería usar la empresa?
	 Aqui al no poder utilizar una solucion customizada lo ideal es usar storage gateway permitiendo un hibrido entre almacenamiento on-premise y almacenamiento en la nube con cache local y soporte SMB 