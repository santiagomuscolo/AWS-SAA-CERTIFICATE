## Examen 3

Preguntas erradas: 9
Preguntas acertadas: 55


1. Una empresa tiene una arquitectura sin servidor compuesta por AWS Amplify, Amazon API Gateway y una función Lambda. La aplicación está conectada a una instancia de base de datos Amazon RDS MySQL en una subred privada. Además, una URL de función Lambda (Lambda Function URL) se implementa como el endpoint HTTPS dedicado para la función, con el siguiente valor: `https://joanstellart.lambda-url.us-west-2.on.aws/` Durante los picos de tráfico, la base de datos genera un error de 'too many connections', impidiendo que los usuarios accedan a la aplicación. ¿Qué solución podría implementar la empresa para resolver este problema?
	 El error "too many connections" refiere a un error de base de datos por lo que la solucion debe orientarse en ese camino, PROVISIONANDO UN RDS PROXY ENTRE LA FUNCION LAMBDA Y LA INSTANCIA DE LA BASE DE DATOS RDS nos aseguramos de poder administrar un gran numero de conexiones mediante el establecimiento de un POOL de conexiones evitando que la base se sobre-cargue con demasiadas conexiones simultaneas.

2. Un arquitecto de soluciones ha recibido la tarea de alojar un sitio web que consta de HTML, CSS y algunos archivos JavaScript. Las páginas web mostrarán varias imágenes de alta resolución. El sitio web debe tener tiempos de carga óptimos y ser capaz de responder a altas tasas de solicitudes.  ¿Cuál de las siguientes arquitecturas puede proporcionar la experiencia de carga más rápida y rentable?
	 En este caso lo mejor seria subir dichos archivos a un bucket S3 y habilitar el hosting de sitios web para luego crear una distribucion de cloudfront y apuntar el dominio al endpoint dle sitio web de s3.
	 S3 de por si para alojar archivos estaticos es altamente escalable y economico y en adicion a esto mismo una distribucion de cloudfront enfrente optimizaria muchisimo los tiempos de entrega.

3. Una empresa de software internacional proporciona a sus clientes soluciones y herramientas personalizadas diseñadas para recolección y análisis eficiente de datos en AWS. La empresa pretende gestionar centralmente y distribuir un conjunto estándar de soluciones y herramientas para las necesidades de autoservicio de sus clientes. ¿Qué solución satisfaría mejor estos requisitos?
	 AWS service catalog portafolios es la mejor solucion para este caso ya que nos ofrece la posibilidad de subir soluciones probadas para que los clientes utilicen a modo de auto-servicio.

4. Una empresa de logística tiene dos VPCs: VPC-A y VPC-B conectadas mediante un enlace de peering. VPC-A contiene solo subredes privadas, mientras que VPC-B tiene solo subredes públicas. Actualmente, la empresa utiliza una única conexión AWS Direct Connect con una interfaz virtual privada para conectar su red on-premises con VPC-A. Para mejorar la disponibilidad y redundancia de la conexión, ¿cuáles de las siguientes opciones son recomendadas? (Selecciona DOS)
	 Aqui lo mejor es agregar una segunda conexion redundante para aumentar la tolerancia a fallos y agregar una VPN site-to-site como respaldo de direct connect para tener una segunda red de respaldo si direct connect fallase

5. Una empresa de medios digitales comparte contenido estático con sus usuarios premium en todo el mundo y también con sus socios que distribuyen sus archivos multimedia. La empresa busca formas de reducir los costos de sus servidores y entregar de manera segura sus datos a sus clientes globalmente con baja latencia. ¿Qué combinación de servicios debe utilizarse para proporcionar la arquitectura más adecuada y rentable? (Selecciona DOS)
	 para el contenido estatico tenemos S3 y para la entrega del mismo de forma eficiente tenemos cloudfront como CDN para mejorar el tiempo de la misma.

6. Tanto los registros históricos como los datos de acceso frecuente se almacenan en un sistema de almacenamiento local en las instalaciones de la empresa. La cantidad de datos actuales está creciendo a un ritmo exponencial. Dado que la capacidad de almacenamiento está llegando a su límite, el Solutions Architect ha decidido mover los registros históricos a AWS para liberar espacio para los datos activos. ¿Cuáles de las siguientes arquitecturas proporcionan la mejor solución en términos de costos y gestión operativa?
	 Siempre que hablamos de transferencias de grandes volumenes de datos debemos pensar en DataSync para on-premise -> aws, aws -> aws y aws -> on-premise, ya que es sumamente eficiente.
	 Storage gateway cumple un rol fundamental en el almacenamiento hibrido con requerimiento de baja latencia a storage en aws pero para migraciones de grandes volumenes de datos no es ideal.
