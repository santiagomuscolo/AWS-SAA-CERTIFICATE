### Alta disponibilidad y escalabilidad
- La escalabilidad significa que una aplicacion/sistema puede manejar mayores cargas adaptandose.
- Hay dos tipos de escalabilidad:
	- Vertical
	- Horizontal (elasticidad)
- La escalabilidad esta vinculada a la disponibilidad pero es diferente.

**Escalabilidad vertical**
- La escalabilidad vertical significa aumentar el size de la instancia.
- Por ejemplo, tu aplicacion se ejecuta en una instancia t2.micro
- Escalar esa aplicacion verticalmente sinifica ejecutarla en una instancia t2.large
- La escalabilidad vertical es muy comun para sistemas no distribuidos como una base de datos.
- Por lo general, hay un limite en cuanto a lo que se puede escalar verticalmente (limite de hardware).

**Escalabilidad horizontal**
- Escalabilidad horizontal significa aumentar el numero de instancias / sistemas para la aplicacion.
- El escalado horizontal implica sistemas distribuidos.
- Esto es muy comun para las aplicaciones web / aplicaciones modernas.
- Es facil escalar horizontalmente gracias a las ofertas en el cloud como AWS EC2.

**Alta disponibilidad**
- La alta disponibilidad suele ir de la mano con el escalado horizontal.
- Alta disponibilidad significa ejecutar la aplicacion / sistema en al menos 2 zonas de disponibilidad
- El objeto de la alta disponibilidad es sobrevivir a la perdida del centro de datos (desastre)

### Vision general de ELB (Elastic Load Balancing)
Los load balancers son servidores que reenvian el trafico a otros servidores en sentido descendente.

**Por que utilizar load balancer**
- Repartir la carga entre varias instancias descendentes.
- Exponer un unico punto de acceso (DNS) a tu aplicacion.
- Manejar sin problemas los fallos de las instancias descendentes.
- Realiza comprobaciones periodicas de la salud de tus instancias.
- Proporciona terminacion SSL (HTTPS) para tus sitios web.
- Imponer la adherencia con las cookies.
- Alta disponibilidad entre zonas.
- Separar el trafico publico del privado.

**Tipos de Load Balancer en AWS**
- AWS tiene 4 tipos de Load Balancers gestionados
	- Classic Load Balancer (V1 vieja generacion): HTTP, HTTPS, TCP, SSL
	- Application Load Balancer (V2 nueva generacion): HTTP, HTTPS. WebSocket
	- Network Load Balancer (v2 nueva generacion): TCP, TLS, UDP
	- Gateway Load Balancer - 2020: Funciona en la capa 3 (Protocolo IP)

### Application Load Balancer (ALB)
- Es de capa 7 (HTTP)
- Equilibrio de carga para multiples aplicaciones HTTP en distintas maquinas.
- Equilibrio de carga para multiples aplicaciones en la misma maquina (por ejemplo, contenedores).
- Soporte para HTTP/2 y WebSocket.
- Soporta redireccionamientos (de HTTP a HTTPS, por ejemplo)
- Talas de enrutamiento a diferentes grupos de destino:
	- Enrutamiento basado en url (example.com/users)
	- Enrutamiento basado en el nombre de host en la URL (one.example.com)
	- Enrutamiento basado en las cabeceras (example.com/users?id=123)
- Los ALB son muy adecuados en arquitecturas de microservicios.
- Tienen una funcion de mapeo para redirigir a un puerto dinamico en ECS
- En comparacion, necesitariamos varios CLB por aplicacion.

**ALB Trget Groups**
- Instancias EC2 (pueden ser gestionadas por ASG) - HTTP
- Tareas de EC2 (gestionadas por el propio ECS) - HTTP
- Funciones Lambda - La peticion HTTP se traduce en un evento JSON
- Direcciones IP - deben ser IPs privadas.
- El ALB puede enrutar a multiples grupos de destino.
- Las comprobaciones de salud son a nivel de grupo de destino.

### Network Load Balancer (NLB)
- Los network load balancer son de capa 4 y incluyen:
	- Reenviar el trafico TCP y UDP a tus instancias.
	- Manejar millones de peticiones por segundo.
	- Menor latencia - 100 ms
- El NLB tiene una IP estatica por AZ y soporta la asignacion de IP elastica

**Target Groups**
- Instancias EC2
- Direcciones IP - privadas
- Application Load Balancer
  Los controles de salud soportan protocolos HTTP, TCP y HTTPS

  ### Gateway Load Balancer (GLB)
- Implementa, escala y administra una flota de dispositivos virtuales de red de terceros en AWS.
- Ejemplo: Firewalls, Sistemas de deteccion y prevencion de intrusiones, sistemas de inspeccion profunda de paquetes, manipulacion de cargas.
- Opera en la capa 3 (capa de red) - paquetes IP
- Combina las siguientes funciones:
	- Gateway transparente - entrada/salida unica para todo el trafico.
	- Load Balancer - distribuye el trafico a tus dispositivos virtuales.
- Utiliza el protocolo GENEVE en el puerto 6081

**Que es GENEVE?**
Geneve es un protocolo que encapsula trafico de red cuando lo envia a dispositivos virtuales (firewalls, IDS/IPS, Appliances de seguridad, etc).

GENEVE = Generic Network Virtualization Encapsulation.

**Target Groups**
- Instancias EC2.
- Direcciones IP privadas.

### Elastic Load Balancer - Sesiones Persistentes (Sticky sessions)
- Es posible implementar stickness para que le mismo cliente sea redirigido a la misma isntancia detras del balanceador de carga.
- Esto funciona para los classic load balancer y los application load balancer.
- La cookie utilizada para la adherencia tiene una dueDate que es calculable.
- Caso de uso: asegurarse de que el usuario no pierda sus datos de session.

### Elastic Load Balancer - Balanceo de carga entre zonas
- Con load balancer de zona cruzada:
  Cada instancia del load balancer distribuye uniformemente entre todas las instancias registradas en todas las AZ de una misma region.
- Sin load balancer de zona cruzada:
  Las solicitudes se distribuyen en las instancias del nodo del elastic load balancer.

  ### Elastic Load Balancer (ELB) - Certificados SSL/TLS
- Un certificado SSL permite que el trafico entre tus clientes y tu load balancer este cifrado en transito (cifrado en vuelo).
- SSL - Secure Socket Layer
- TLS - Transport Layer Security, es una version mas reciente.
- Los certificados SSL publicos son emitidos por autoridades de certificacion (CA).
- Los certificados SSL tienen fecha de caducidad y deben ser renovados.
- El load balancer utiliza un certificado X.509 (certificado de servidor SSL/TLS)
- Puedes gestionar los certificados mediante ACM (AWS Certificate Manager).
- Tambien se pueden crear y subir certificados propios.

**Server Name Indication (SNI)**
- SNI resuelve el problema de cargar varios certificados SSL en un servidor web.
- Es un protocolo y requiere que el cliente indique el nombre del servidor de destino en el handshake del SSL inicial.
- El servidor encontrara entonces el certificado correcto o devolvera el predeterminado.

### Elastic Load Balancer - Drenaje de la conexion
- Nombre de la caracteristica:
	- Drenaje de la conexion - CLB
	- Retraso en el desregistro - ALB y NLB
- Tiempo para completar las "peticiones en vuelo" mientras la instanciase esta desregistrando o no esta sana.
-  Deja de enviar nuevas peticiones a la instancia EC2 que se esta desregistrando.
- Entre 1 y 3600 segundos (por defecto: 300 segundos).
- Se puede desactivar (fijar el valor en 0).
- Establece un valor bajo si tus peticiones son cortas.

### Vision general de los Auto Scaling Groups (ASG)
El objetivo de un ASG es el siguiente:
- Reducir (agregar instancias EC2) para adaptarse a un aumento de carga. 
- Aumentar (Eliminar instancias EC2) para que coincida con una disminucion de la carga.
- Asegurar que tenemos un numero minimo y maximo de instancias EC2 en funcionamiento.
- Registrar automaticamente nuevas instancias en un Load Balancer.
- Volver a crear una instancia EC2 en caso de que se elimine una anterior

**Atributos**
- Una plantilla de lanzamiento:
	- AMI + Tipo de instancia
	- Datos de usuario EC2
	- Volumenes EBS
	- Grupos de seguridad
	- Par de claves SSH
	- Roles IAM para instancias EC2
	- Informacion sobre la red y subred
	- Informacion del Load Balancer
- Size minimo / maximo / capacidad inicial.
- Politicas de escalado.

## Politicas de escalado dinamico

- Escalado de seguimiento de objetivos:
	- Lo mas sencillo y facil de configurar
	- Ejemplo: Quiero que la media de la CPU de ASG se mantenga en torno al 40%
- Escalado simple / escalonado:
	- Cuando se active una alarma de CloudWatch (por ejemplo, CPU > 70%), agregamos 2 unidades.
	- Cuando se active una alarma de CloudWatch (ejemplo CPU < 30%), eliminamos 1.
- Acciones programadas
	- Anticipa un escalado basado en patrones de uso conocidos.
	- Ejemplo: aumentar la capacidad minima a 10 a las 17 horas de los viernes.

## Escalado predictivo
Prevision continua de la carga y programacion del escalado por adelantado.

**Cuales son las buenas metricas para escalar**
- CPUUtilization:  Utilizacion media de la CPU en tus instancias.
- RequestCountPerTarget: para asegurarse de que el numero de peticiones por instancia EC2 es estable.
- Promedio de entrada/salida de red (si tu aplicacion esta vinculada a la red).
- Cualquier metrica personalizada (que impulses con CloudWatch)

**Enfriamiento de la escala**
Por defecto cuando se produzca una actividad de escalado entraras en periodo de "enfriamiento" durante 300s con la finalidad de estabilizar las metricas.
Lo mas recomendado es utilizar una AMI (Amazon Machine Image) para reducir el tiempo de configuracion y enfriamiento.

### QUIZ
1. Escalar una instancia EC2 de `r4.large` a `r4.4xlarge` se llama .....................
	Escalado vertical
2. Ejecutar una aplicación en un Auto Scaling Groups que escala el número de instancias EC2 de entrada y salida se llama .....................
	Escalado horizontal
3. Los Elastic Load Balancers proporcionan un .......................
	Nombre DNS estatico que podemos utilizar en nuestra aplicacion
4. Estás ejecutando un sitio web en 10 instancias de EC2 dirigidas por un Elastic Load Balancers. Tus usuarios se quejan de que el sitio web siempre les pide que se vuelvan a autentificar cuando se mueven entre las páginas del sitio web. Estás desconcertado porque funciona bien en tu máquina y en el entorno de desarrollo con 1 instancia EC2. ¿Cuál podría ser la razón?
	Los ELB no tienen activadas las sticky sessions
5. Estás utilizando un Load Balancer de aplicaciones para distribuir el tráfico a tu sitio web alojado en instancias EC2. Resulta que tu sitio web sólo ve el tráfico procedente de direcciones IPv4 privadas que, de hecho, son las direcciones IP de tu Load Balancer de aplicaciones. ¿Qué debes hacer para obtener la dirección IP de los clientes conectados a tu sitio web?
	Modifica el backend para obtener la IP del cliente a partir de la cabecera X-Forwarded-For
6. Has alojado una aplicación en un conjunto de instancias de EC2 encabezadas por un Elastic Load Balancer. Una semana después, los usuarios empiezan a quejarse de que a veces la aplicación no funciona. Investigas el problema y descubres que algunas instancias de EC2 se bloquean de vez en cuando. ¿Qué deberías hacer para evitar que los usuarios se conecten a las instancias EC2 que se bloquean?
	Habilitar las comprobaciones de salud del ELB
7. Estás trabajando como Arquitecto de Soluciones para una empresa y tienes que diseñar una arquitectura para una aplicación de alto rendimiento y baja latencia que recibirá millones de peticiones por segundo. ¿Qué tipo de Elastic Load Balancer deberías elegir?
	Network load balancer
8. Los Load Balancer de aplicaciones soportan los siguientes protocolos, **EXCEPTO**:
	TCP
9. Los Load Balancer de aplicaciones pueden enrutar el tráfico a diferentes Grupos de Destino en función de lo siguiente, **EXCEPTO**:
	Ubicacion del cliente (geografia)
10. Los destinos registrados en un Grupo de Destino para un Load Balancer de Aplicaciones pueden ser uno de los siguientes, **EXCEPTO**:
	Network Load Balancer
11. Por motivos de normativa, te gustaría exponer una dirección IP estática fija a tus usuarios finales para que puedan escribir reglas de firewall que sean estables y aprobadas por los reguladores. ¿Qué tipo de Elastic Load Balancer elegirías?
	Network load balancer
12. Quieres crear una cookie personalizada basada en la aplicación en tu Load Balancer de aplicaciones. ¿Cuál de las siguientes opciones puedes utilizar como nombre de la cookie?
	APPUSERC
13. Tienes un Network Load Balancer que distribuye el tráfico entre un conjunto de instancias EC2 en `us-east-1`. Tienes 2 instancias EC2 en `us-east-1b` AZ y 5 instancias EC2 en `us-east-1e` AZ. Has observado que la utilización de la CPU es mayor en las instancias EC2 en `us-east-1b` AZ. Tras una investigación más profunda, te has dado cuenta de que el tráfico se distribuye por igual en las dos AZ. ¿Cómo resolverías este problema?
	Habilitar el Load balancer entre zonas (Cross-Zone Load Balancing)
14. ¿Qué función de los Application Load Balancer y de los Network Load Balancer te permite cargar varios certificados SSL en un oyente?
	Indicacion del nombre del servidor (SNI)
15. Tienes un Load Balancer de aplicaciones que está configurado para redirigir el tráfico a 3 grupos de destino basados en los siguientes nombres de host: **users.example.com**, **api.external.example.com** y **checkout.example.com**. Te gustaría configurar HTTPS para cada uno de estos nombres de host. ¿Cómo configuras el ALB para que esto funcione?
	Utilizar el SNI
16. Tienes una aplicación alojada en un conjunto de instancias de EC2 gestionadas por un Auto Scaling Group que has configurado tanto la capacidad deseada como la máxima a 3. Además, has creado una alarma de CloudWatch que está configurada para escalar tu ASG cuando la utilización de la CPU alcance el 60%. Tu aplicación ha recibido de repente un tráfico enorme y ahora está funcionando con un 80% de utilización de la CPU. ¿Qué ocurrirá?
	Nada
17. Tienes un Grupo de Auto Scaling encabezado por un Load Balancer de aplicaciones. Has configurado el ASG para que utilice las comprobaciones de salud del ALB, y una instancia EC2 acaba de ser reportada como no saludable. ¿Qué pasará con la instancia EC2?
	El ASG terminara la instancia 
18. Tu jefe te ha pedido que escales tu Auto Scaling Groups en función del **número de peticiones por minuto** que tu aplicación hace a tu base de datos. ¿Qué debes hacer?
	Crear una metrica personalizada de CloudWatch y luego crea una alarma de CloudWatch sobre esta metrica para escalar el ASG
19. Una aplicación web alojada en una flota de instancias EC2 gestionada por un Auto Scaling Groups. Estás exponiendo esta aplicación a través de un Load Balancer de aplicaciones. Tanto las instancias EC2 como el ALB están desplegados en una VPC con el siguiente CIDR `192.168.0.0/18`. ¿Cómo se configura el grupo de seguridad de las instancias EC2 para garantizar que sólo el ALB pueda acceder a ellas en el puerto `80`?
	Agrega una regla de entrada con el puerto 80 y el grupo de seguridad de ALB como origen
20. Una aplicación se despliega con un Application Load Balancer y un Auto Scaling Groups. Actualmente, escalas manualmente el ASG y te gustaría definir una Política de Escalado que garantice que el número medio de conexiones a tus instancias EC2 sea de unas 1000. ¿Qué política de escalado deberías utilizar?
	Politica de seguimiento del objetivo
21. Tienes un ASG y un Network Load Balancer. La aplicación en tu ASG soporta el protocolo HTTP y está integrada con los controles de salud del Load Balancer. Actualmente utilizas las comprobaciones de salud TCP. Te gustaría migrar a utilizar las comprobaciones de salud HTTP ¿Qué debes hacer?
	Migra la comprobacion de salud a HTTP
22. Tienes un sitio web alojado en instancias de EC2 en un Auto Scaling Groups, con un Load Balancer de aplicaciones al frente. Actualmente, el sitio web se sirve a través de HTTP, y te han encargado que lo configures para utilizar HTTPS. Has creado un certificado en ACM y lo has adjuntado al Application Load Balancer. ¿Qué puedes hacer para obligar a los usuarios a acceder al sitio web utilizando HTTPS en lugar de HTTP?
	Configura el application load balancer para redirigir HTTP a HTTPS