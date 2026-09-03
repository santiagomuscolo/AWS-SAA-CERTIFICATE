## Examen 6

intento 1

correctas: 55
incorrectas: 10

1. Una organización con múltiples equipos de desarrollo ha desplegado una variedad de recursos en AWS para distintas iniciativas de innovación. Cada equipo tiene autonomía para crear recursos según sus necesidades. Para evitar interrupciones operativas, la organización quiere monitorear proactivamente el uso de sus servicios en AWS y prevenir que se superen los límites de cuota establecidos. ¿Qué combinación de acciones debería implementar el arquitecto de soluciones para satisfacer estos requerimientos? (Selecciona DOS)
	 Aqui necesitamos una convinacion de varias herramientas, inicialmente AWS Trusted ADvisor y una funcion Lambda que actualice las verificaciones de service limits en el servicio cada 24h y eventBridge + SNS para capturar el evento y notificarlo.
	 Aqui yo puse Config en lugar de trusted advisor pero fue erroneo ya que config no permite manejar limites de servicios, TA esta creado especificamente para brindar las mejores practicas en los servicios, incluyendo recomendacion de costes, optimizaciones de seguridad, etc...

2. Un laboratorio de investigación genética está planeando lanzar un clúster de computación de alto rendimiento (HPC) en AWS para ejecutar análisis masivos de secuencias de ADN. La solución debe escalar automáticamente los análisis para evaluar grandes volúmenes de datos y generar resultados de forma más rápida y precisa. El clúster está compuesto por servidores Windows que se ejecutan en instancias EC2 t3a.medium. Como Solutions Architect, debes garantizar que la arquitectura proporcione mayor ancho de banda, mayor rendimiento de paquetes por segundo (PPS) y menores latencias entre instancias de manera consistente. ¿Cuál es la solución más adecuada y rentable que el arquitecto debería implementar para cumplir con estos requisitos?
	 Cuando hablamos de computacion de alto rendimiento Elastic Network Adapter para obtener un mayor ancho de banda es el ideal proporcionando hasta 100 GBps del mismo, pero yo coloque EFA (Elastic Fabric Adapter) el cual es una version mejorada de ENA pero no posee soporte para servidores windows.

3. Una organización planea ejecutar una aplicación en un servidor físico dedicado que no utiliza virtualización. Los datos de la aplicación se almacenarán en una solución de almacenamiento que usa el protocolo NFS. Para prevenir la pérdida de datos, necesitas utilizar un servicio de almacenamiento en la nube duradero para almacenar una copia de los datos. ¿Cuál de las siguientes opciones es la solución más adecuada para cumplir con este requisito?
	 En este caso la mejor opcion en storage gateway + file gateway que posee integracion directa con NFS y SMB para almacenar y recuperar datos de S3.
	 Yo coloque Volume gateway que si bien tiene integracion SMB no posee integracion NFS.

4. Una empresa diagnostica problemas operativos en su arquitectura en la nube registrando el historial de llamadas a la API de AWS de todos los recursos. El Solutions Architect debe implementar una solución que permita identificar rápidamente los cambios recientes realizados en los recursos de su entorno, incluyendo creación, modificación y eliminación de recursos de AWS. Uno de los requisitos es que los archivos de registro generados deben estar cifrados para evitar problemas de seguridad. ¿Cuál de las siguientes es la mejor opción para implementar el cifrado?
	 Aqui lo ideal es usar la configuracion por defecto de cloudtrail que utiliza el cifrado del lado del servidor en S3, yo marque esto pero haciendolo de forma manual lo cual ya esta contemplado por el servicio previamente nombrado.

5. Una plataforma de investigación biomédica está alojada en una instancia EC2 que procesa datos clínicos confidenciales. La instancia EC2 se encuentra en una subred privada y todos los registros se almacenan en un bucket de Amazon S3. Los investigadores acceden a los datos clínicos a través de Internet mediante URLs pre-firmadas generadas por la aplicación. El equipo de cumplimiento está preocupado porque la conectividad a Internet desde Amazon S3 representa un riesgo de seguridad. En este escenario, ¿qué harías para resolver esta vulnerabilidad de la manera más rentable?
	 Aqui un gateway VPC endpoint nos permitira acceder desde la VPC a S3 sin costes adicionales y sin pasar por el internet, yo marque usar un interface endpoint pero esto genera costes extras por hora y cantidad de datos procesados.


6. Amazon Elastic Kubernetes Service (Amazon EKS) es utilizado por una empresa de comercio electrónico para desplegar y administrar sus aplicaciones en contenedores. El sitio web experimenta un aumento en el tráfico durante las festividades, lo que incrementa significativamente la carga. El objetivo es garantizar que la infraestructura subyacente se escale de manera automática en respuesta a la demanda. ¿Cuáles de las siguientes opciones cumplirían con los requisitos con la menor cantidad de sobrecarga operativa? (Selecciona DOS)
	 Aqui se deberia de habilitar el servidor de metricas de kubernetes en el cluster de EKS y habilitar el Horizontal Pod autoescaling y configurar karpenter que permite escalar con poca configuracion un cluster de EKS y permite ajustar automaticmente el numero de nodos cuando los pods fallen o sean registrados a otros nodos.

7. Un importante banco de inversión está en proceso de construir una plataforma de trading de Forex. Para garantizar alta disponibilidad y escalabilidad, diseñaste la plataforma de trading para utilizar un Elastic Load Balancer frente a un grupo de Auto Scaling de instancias EC2 On-Demand distribuidas en múltiples Zonas de Disponibilidad. Para la capa de base de datos, elegiste utilizar una única instancia de Amazon Aurora para aprovechar su sistema de almacenamiento distribuido, tolerante a fallos y auto-reparable. En caso de que la instancia de base de datos principal falle, ¿qué sucede con Amazon Aurora durante la conmutación por error?
	 Aqui aurora intentara primero crear una nueva instancia de la base de datos en la misma zona de disponibilidad y si la AZ esta muy afectada impidiendolo ahi intentara en otra AZ.

8. Una empresa tiene una infraestructura donde las instancias EC2 en una subred privada recuperan objetos de Amazon S3 a través de una instancia NAT. El Solutions Architect ha recibido la instrucción de reducir los costos de la solución actual. ¿Cómo debe el Solutions Architect rediseñar la arquitectura de la manera más rentable?
	 Aqui lo mejor es usar un gateway endpoint permitiendo reducir los costos de comunicacion entre la EC2 y S3 ya que el endpoint de interfaz cobra por hora y cantidad de datos procesados.

9. Una organización gubernamental está desarrollando una plataforma para gestión de trámites ciudadanos, desplegada en instancias EC2 dentro de una VPC de Amazon. Planean utilizar un Network Load Balancer para distribuir el tráfico entrante entre las instancias de la aplicación. El equipo de cumplimiento normativo exige que se inspeccione todo el tráfico que entra y sale de la VPC para cumplir con los estándares de seguridad nacional. ¿Cuál de los siguientes enfoques satisface los requisitos?
	 Aqui lo mejor seria configurar un WAF a nivel de VPC y agregar grupos de reglas custom para inspeccionar el trafico de entrada y salida, de esta forma tenemos un analisis constante ya que todo el trafico pasaria por el WAF.
	 ![[Pasted image 20260903170614.png]]

10. Una empresa usa un grupo de Auto Scaling con instancias EC2 detrás de un ALB. Algunas instancias fallan en verificaciones de estado HTTPS y se terminan, perdiendo registros efímeros. ¿Cómo puede un AWS Solutions Architect recopilar automáticamente los registros antes de la terminación de las instancias?
	 Aqui lo mejor seria utilizar el lifecycle hook termination:Wait y con una regla de eventos de CloudWatch + lambda enviar los registros a cloudwatch logs antes de la terminacion.
	 Aqui coloque Run command y si bien es utilizable requiere mas configuracion.


