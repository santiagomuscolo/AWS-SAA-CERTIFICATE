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