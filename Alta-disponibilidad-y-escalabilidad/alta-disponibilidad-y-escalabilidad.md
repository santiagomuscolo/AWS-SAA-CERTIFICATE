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