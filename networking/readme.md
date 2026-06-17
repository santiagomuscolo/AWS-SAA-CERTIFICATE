## CIDR - IPv4 privada vs publica

> [!info] **CIDR (Classless Inter-Domain Routing/Enrutamiento entre dominios sin clase)** es un método para definir rangos de direcciones IP de manera flexible.

- Se utiliza en las reglas de los grupos de seguridad y en la red de AWS en general.
- ![[Pasted image 20260522213708.png]]
- Ayudan a definir un rango de direcciones ip
	- WW.XX.YY.ZZ/32 => una IP
	- 0.0.0.0/0 => todas las IP
	- 192.168.0.0/26 => 64 IPs

> [!tip]  
La cantidad de IPs se calcula con: 2(32−mascara)


Un CIDR consta de dos componentes

**IP base**
representa una IP contenida en el rango XX.XX.XX.XX, como por ejemplo: 10.0.0.0 o 192.168.0.0

**Mascara de subred**
Define cuantos bits no pueden cambiar en la IP, como por ejemplo: /0, /24, /32
- Puede adoptar varias formas:
	  /8 => 255.0.0.0
	  /16 => 255.255.0.0
	  /24 => 255.255.255.0
	  /32 =>255.255.255.255

### Mascara de subred
La mascara de subred basicamente permite que parte de la IP subyacente obtenga valores siguiented adicionales de la IP base.
![[Pasted image 20260522215136.png]]
### IP publica vs privada

> [!note]  
Algunas direcciones IPv4 fueron reservadas exclusivamente para redes privadas (LAN).

- La Ip privada solo puede permitir determinados valores
	- 10.0.0.0 - 10.255.255.255 (10.0.0.0/8)
	- 172.16.0.0 - 172.31.255.255 (172.16.0.0/12)
	- 192.168.0.0 - 192.168.255.255 (192.168.0.0/16)

> [!info] AWS RESERVA IPs
> Cada subnet pierde 5 IPs reservadas automaticamente 

## Vision general de la VPC por defecto
Virtual Private Cloud para empezar es un servicio que nos permite mantener recursos de nuestro cloud en una red privada, el mismo viene con una configuracion por defecto para los usuarios iniciantes con poco o nulo conocimiento de networking. Por otra parte, la VPC predeterminada tiene conectividad a internet y todas las instancias EC2 dentro de ella tienen IPv4 publicas, tambien obtenemos un nombre DNS IPv4 publico y otro privado

## Vision general de la VPC
> [!info] VPC = Virtual Private Cloud
> MAX 5 VPC por region (aunque es modificable)

- CIDR max. CIDR por VPC es 5, por cada CIDR:
  - el size minimo es de /28 (16 direcciones IP)
  - el size maximo es de /16 (65536 direcciones IP)
- Como la VPC es privada, solo se permiten los rangos IPv4 privados:
  - 10.0.0.0 - 10.255.255.255 (10.0.0.0/8)
  - 172.16.0.0 - 172.31.255.255 (172.16.0.0/12)
  - 192.168.0.0 - 192.168.255.255 (192.168.0.0/16)

> El CIDR de tu VPC no debe solaparse con tus otras redes

## Vision general de subredes
> [!info] Subred = subrango de direcciones IPv4 dentro de una VPC

AWS por defecto reserva 5 direcciones IP en cada subred, estas 5 direcciones no quedan disponibles para su uso y no se pueden asignar a una instancia EC2.
Ejemplo: si el bloque CIDR es 10.0.0.0/24, las direcciones IP reservadas son:
- 10.0.0.0 - Direccion de red
- 10.0.0.1 - reservada por AWS para el router de la VPC
- 10.0.0.2 - reservada por AWS para asignar al DNS proporcionado por amazon
- 10.0.0.3 - reservada por AWS para uso futuro
- 10.0.0.255 - Direccion de difusion de red, AWS no soporta broadcast en una VPC, por lo que la direccion esta reservada

> [!tip] Consejo de examen, si necesitas 29 direcciones IP que mascara necesitarias?
> - No puedes elegir una subred de tamano /27 =32 direcciones por que - 5 te daria 27
> - Una subred de tamano /26 es la ideal por que /26 = 64 direcciones - 5 te darian 59 direcciones disponibles

## Vision general de subredes
> [!info] Subred = subrango de direcciones IPv4 dentro de una VPC

AWS por defecto reserva 5 direcciones IP en cada subred, estas 5 direcciones no quedan disponibles para su uso y no se pueden asignar a una instancia EC2.
Ejemplo: si el bloque CIDR es 10.0.0.0/24, las direcciones IP reservadas son:
- 10.0.0.0 - Direccion de red
- 10.0.0.1 - reservada por AWS para el router de la VPC
- 10.0.0.2 - reservada por AWS para asignar al DNS proporcionado por amazon
- 10.0.0.3 - reservada por AWS para uso futuro
- 10.0.0.255 - Direccion de difusion de red, AWS no soporta broadcast en una VPC, por lo que la direccion esta reservada

> [!tip] Consejo de examen, si necesitas 29 direcciones IP que mascara necesitarias?
> - No puedes elegir una subred de tamano /27 =32 direcciones por que - 5 te daria 27
> - Una subred de tamano /26 es la ideal por que /26 = 64 direcciones - 5 te darian 59 direcciones disponibles

## Puertas de enlace (Gateways) de internet y tablas de rutas
> [!info] Internet Gateway permite que los recursos de una VPC se conecten a internet

**Caracteristicas**:
- Escala horizontalmente, tiene alta disponibilidad y redundancia
- Solo puede estar conectado a una VPC y viceversa
- Debe estar asociado a un router y a una tabla de rutas
![[Pasted image 20260527200436.png]]

**Que es una route table?**
Una route table es un mapa de rutas que permite definir por donde saldra el trafico de cada CIDR, por ejemplo:
|Destino|Target|
|---|---|
|`10.0.0.0/16`|local|
|`0.0.0.0/0`|igw-123|

de esta manera todo el CIDR 10.0.0.0/16 se podra comunicar entre si en la VPC pero todo lo que no corresponda a ese CIDR saldra a internet

## Bastion host
> [!info] aplicacion que se localiza en un servidor con la finalidad de proporcionar seguridad a la red interna

- Podemos utilizar un bastion host para acceder mediante SSH a nuestras instancias EC2 privadas
- El bastion esta en una subred publica, que a su vez esta conectada a todas las demas subredes privadas
- **El grupo de seguridad del Bastion Host debe permtir** la entrada desde Internet en el puerto 22 desde un CIDR restringido, por ejemplo un CIDR publico de tu empresa
- **El grupo de seguridad de las instancias EC2** debe permitir el grupo de seguridad del host Bastion, o la IP privada del bastion host.

## Instancia NAT
> [!info] fueron reemplazadas por los gateway NAT pero aun pueden aparecer en el examen

**NAT = Traduccion de direcciones de red**

- Permite que las instancias EC2 en subredes privadas puedan conectarse a internet
- Debe lanzarse en una subred publica
- Debe tener una IP elastica asociada 
- Las tablas de ruta deben estar cofiguradas para dirigir el trafico de subredes privadas a la instancia NAT

![[Pasted image 20260530145412.png]]![[Pasted image 20260530145600.png]]

## Gateway NAT
El gateway NAT es gestionado por AWS, ofreciendo mayor ancho de banda, mayor disponibilidad y sin administracion. Por otro lado, ofrece un sistema de precios basado en su uso y ancho de banda con 5 Gbps de ancho de banda + escalado automatico hasta los 45 Gbps

### Alta disponibilidad
Los NATGW son resilientes dentro de una unica zona de disponibilidad pero para una mejor tolerancia a fallos se aconseja tener varios NATGW en varias AZ.

> [!info] No es necesaria la conmutacion por error entre zonas de disponibilidad por que si una AZ se cae no necesitara un NATGW

### NATGW vs Instancia NAT
![[Pasted image 20260601200646.png]]

## NACL y grupos de seguridad
> [!info] NACL = network access control list

### High level work
![[Pasted image 20260601202225.png]]

## Overview
Las NACL (network access control list) son como un firewall que controla el trafico desde y hacia las subredes, en AWS se crea una NACL por subred al cual se le definen reglas:
- Las reglas tienen un numero de entre 1 - 32766, mayor precedencia con un numero menor.
- La primera coincidencia de reglas determinara la decision
- La ultima regla es un asterisco (*) y deniega una peticion en casod e que no coincida con ninguna regla.
- El estandar es agregar reglas de a 100

Las NACL recien creadas por defecto denegaran todo hasta que se definan sus reglas y son una buena forma de bloquear una direccion IP concreta a nivel de subred

La NACL por defecto aceptara todo lo que entra/sale y se aconseja no modificarla sino crear nuevas personalizadas.

### Puertos efimeros
Para que dos endpoints cualesquiera establezcan una conexion, deben utilizar puertos. Los clientes se conectan a un puerto definido, y esperan una respuesta en un puerto efimero que puede variar en distintos rangos segun el OS.
![[Pasted image 20260602193856.png]]

> [!info] es importante tener en cuenta que si presentamos multiples subredes (publicas/privadas) debemos editar las reglas de NACL para poder permitir las multiples combinaciones de conexiones

![[Pasted image 20260602194054.png]]

### Grupo de seguridad vs NACL
![[Pasted image 20260602194125.png]]


### Diferencia Bastion Host vs NATGW

**Bastion Host**
Punto de entrada a recursos privados (siendo generalmente una instancia EC2) que puede recibir conexiones entrantes y salientes, evitando que las instancias privadas obtengan internet directamente

**NATGW**
Sirve para que recursos privados puedan salir a internet sin ser expuestos, en los mismos no se reciben conexiones entrantes pero si salientes y se enmascara/protege la IP privada de cara a internet, ademas este permite que las intancias posean internet directamente.

## VPC Peering
VPC peering permite conectar de forma privada dos o mas VPC utilizando la red de AWS, la conexion no es transitiva, es decir, debe establecerce en todas las VPC que necesiten comunicarse entre si.

> [!info] Se deben actualizar las tablas de rutas de las subredes para que las instancias puedan comunicarse entre si

![[Pasted image 20260603125929.png]]

## VPC Endpoints
Estos endpoints nos permiten de forma privada acceder a servicios de AWS sin la necesidad de usar un internetGateway para ir a ellos por medio de internet (ya que cada servicio de AWS esta expuesto a internet con una URL publica), de esta manera evitamos hops, es mas eficiente y seguro.

**Tipos de endpoints**
- Endpoints de interfaz: funcionan con private link (ENI) como punto de entrada y debe adjuntarsele grupos de seguridad, soporta la mayoria de servicios de AWS y tienen un coste monetario por hora + GB de datos procesados
- Endpoints Gateway: Proporcionan un gateway y debe utilizarse como destino en la tabla de rutas, el mismo no utiliza grupos de seguridad y soporta dynamoDB y S3, siendo ademas gratis.

## Logs de flujo de la VPC
Los logs de flujo de una VPC capturan el trafico IP entre nuestras interfaces:
- Logs de flujo de subred
- Logs de flujo de VPC
- logs de flujo de ENI

y posee integracion con S3 / Cloudwatch logs

**Cuerpo de un log**
![[Pasted image 20260604160502.png]]

**Arquitecturas**
![[Pasted image 20260604160535.png]]

## VPN site to site
![[Pasted image 20260605163453.png]]

- Virtual private gateway / gateway privado virtual (VGW)
	- Concentrador VPN en el lado AWS de la conexion VPN
	- La VGW se crea y se adjunta a la VPC desde la que quieres crear la conexion VPN site-to-site
	- Posibilidad de personalizar el ASN (Numero de Sistema Autonomo)
- Gateway del cliente (CGW)
	- Aplicacion de software o dispositivo fisico en el lado del cliente de la conexion VPN

- Dispositivo gateway del lado del cliente (en las instalaciones)
	- Que direccion IP utilizar
		- Direccion Ip publica enrutable por internet para tu dispositivo Gateway del cliente
		- Si estas detras de un dispositivo NAT habilitado para atravesar NAT (NAT-T), utiliza la direccion IP publica del dispositivo NAT
- La propagacion de rutas debe estar habilitada para la puerta de enlace virtual en la tabla de rutas asociada a tus subredes
- Si necesitas hacer PING a tus instancias EC2 desde el local, asegurate de agregar el protocolo ICMP en la entrada de tus grupos de seguridad

![[Pasted image 20260605164412.png]]

**VPN de AWS CloudHub**
- Proporciona una comunicacion segura (cifrada) si se tiene varias conexiones VPN
- Modelo hub-and-spoke de bajo coste para la conectividad de red primaria o secundaria entre distintas sedes
- Es una conexion VPN, asi que va por internet publico
- Para configurarla, se necesita conectar varias VPN en la misma Virtual Private Gateway, establecer un enrutamiento dinamico y configurar tablas de rutas

## Direct connect (DX) & Direct connect gateway

**Direct connect**
Direct connect permite crear una conexion privada desde una red remota a tu VPC, permitiendo acceder a recursos publicos y privados en la misma conexion, con soporte IPv4 e IPv6.

**Casos de uso**
- Aumentar el rendimiento del ancho de banda - trabajar con grandes conjuntos de datos - menor coste
- Experiencia de red mas consistente - aplicaciones que utilizan alimentacion de datos en tiempo real
- Entornos hibridos (on prem + cloud)

**Diagrama**
![[Pasted image 20260608151900.png]]

Hay un caso de uso concreto en el que puede querer conectarse un direct connect a multiples VPCs en diferentes regiones, esto implica el uso de un direct connect gateway para habilitar dicha comunicacion.![[Pasted image 20260608152057.png]]

**Tipos de conexion**
- Conexiones dedicadas: capacidad de 1 Gbps, 10 Gbps y 100 Gbps
	- Puerto ethernet fisico dedicado a un cliente
	- Primero se hace la peticion a AWS y luego la completan los socios de AWS Direct Connect
- Conexiones alojadas: 50 Mbps, 500 Mbps a 10 Gbps
	- Las peticiones de conexion se realizan a traves de los socios de AWS Direct Connect
	- Se puede agregar o eliminar capacidad bajo demanda
	- 1, 2, 5, 10 Gbps disponibles en socios selectos de AWS Direct Connect
- Los plazos para establecer una conexion suelen ser superiores a un mes.

**Cifrado**
Los datos en transito no estan cifrados pero si son privados, ya que residen en una conexion privada. Por otro lado, existe una forma de agregar una capa extra de seguridad integrando DC + VPN (Virtual Private Network) para proporcionar una conexion privada cifrada mediante IPsec.

**Resiliencia**
![[Pasted image 20260608153612.png]]

## Direct connect + VPN site-to-site
En caso de que direct connect falle, se puede configurar una conexion de reserva de direct connect (opcion cara e ineficiente), o una conexion VPN site-to-site (sitio-a-sitio)
![[Pasted image 20260608153909.png]]

## Transit Gateway
![[Pasted image 20260609164255.png]]
Cuando se busca tener peering transitivo entre miles de VPC y en las instalaciones de utiliza un patron denominado hub-and-spoke (estrella) promovido en AWS con Transit Gateway para resolver esta problematica, funciona con DC & VPN y soporta IP multicast.

> [!info] IP multicast hace referencia al proceso en el que un solo emisor transmite a multiples receptores al mismo tiempo, en AWS solo Transit Gateway puede hacerlo.

**VPN site-to-site ECMP**
- ECMP = enrutamiento multitrayectoria de igual coste
- Estrategia de enrutamiento que permite reenviar un paquete por multiples rutas optimas
- Caso practico: crear varias conexiones VPN site-to-site para aumentar el ancho de banda de tu conexion a AWS.
	![[Pasted image 20260609164638.png]]
	![[Pasted image 20260609164836.png]]

**Direct connect + Transit Gateway**
![[Pasted image 20260609165005.png]]
## VPC Traffic mirroring (Observability)
VPC mirroring es una feature que te permite capturar e inspeccionar el trafico de red de tu VPC, realizando una copia del mismo (duplicado del trafico) y enviandolo a otro destino para su inspeccion.

La captura del trafico puede producirse:
- Desde (Fuente): ENIs
- Hacia (objetivos): una ENI o un Network Load Balancer

Esta feature nos permite capturar todos los paquetes entrantes o solo los paquetes que nos interesen, ademas de permitirnos tener todo en una VPC o en multiples VPC (VPC peering).
Casos de uso relacionados pueden ser:
- Inspeccion de contenidos
- Monitorizacion de amenazas
- Resolucion de problemas

![[Pasted image 20260609171854.png]]

## IPv6 para VPC

**Que es IPv6?**
IPv6 nace como sucesor de IPv4 debido a la cantidad de direcciones que este puede proporcionar (4.300 millones), siendo que IPv6 esta diseñado para proporcionar 3.4 x 10'38 direcciones IP unicas.
Cada direccion IPv6 es unica y enrutable a internet (no hay rango privado) y el formato es el siguiente: x.x.x.x.x.x.x (x es hexadecimal, el rango puede ser de 0000 a ffff).

Ejemplo: 2001:db8:3333:4444:5555:6666:7777:8888

**IPv6 en VPC**
No se puede deshabilitar IPv4 para las subredes y VPCs sino que podemos habilitar IPv6 para funcionar en modo dual stack (ipv4 & ipv6), de esta manera nuestras instancias EC2 tendran almenos una IPv4 interna privada y una IPv6 publica y pueden comunicarse a internet con cualquiera de las dos.
![[Pasted image 20260610202349.png]]

**Solucion a problemas de IPv6**
Al no poder deshabilitar IPv4 en nuestras subredes y VPC si no podemos levantar una instancia dentro de la misma es imposible que sea por falta de direcciones IPv6 ya que existen muchisimas, en todo caso es por que agotamos el rango de IPv4 disponibles de nuestro Classless inter-domain routing (CIDR), por lo que deberemos crear otro para asi levantar nuevas instancias.
![[Pasted image 20260610202707.png]]

## Gateway de internet solo de salida
Dentro de el internet gateway tenemos una opcion que nos permite establecer un gateway solo de salida para IPv6 (funciona de forma muy similar a un NAT gateway).
![[Pasted image 20260610204457.png]]

**Enrutamiento IPv6**
![[Pasted image 20260610204846.png]]

## Costes de red en AWS
![[Pasted image 20260612134359.png]]

**Minimizar el coste de la red en trafico de salida**
- Trafico de salida: trafico saliente (de AWS al exterior)
- Trafico de entrada: trafico entrante - del exterior a AWS (normalmente gratis)
- Intenta mantener la mayor cantidad de trafico de internet dentro de AWS para minimizar los costes
- Las ubicaciones de Direct Connect situadas en la misma region de AWS tienen un coste inferior para la red de salida
![[Pasted image 20260612134857.png]]

**Gateway NAT vs Gateway VPC endpoint**
![[Pasted image 20260612140347.png]]
## Firewall de red AWS
proporciona proteccion de capa 3 a capa 7 para toda tu amazon VPC y puede inspeccionarse en cualquier direccion
- Trafico de VPC a VPC
- Saliente a internet
- Entrante desde internet
- Hacia / desde Direct connect y VPN site-to-site

Internamente AWS network firewall usa AWS gateway load balancer y las reglas del mismo pueden gestionarse de forma centralizada entre cuentas de AWS firewall manager para aplicarlas a muchas VPC.
![[Pasted image 20260612140824.png]]

## Quiz

Question 1:

¿A qué corresponde el CIDR `10.0.4.0/28`?
10.0.4.0 a 10.0.4.15

Question 2:

Tienes una red corporativa de tamaño `10.0.0.0/8` y una oficina satélite de tamaño `192.168.0.0/16`. ¿Qué CIDR es aceptable para tu VPC de AWS si piensas conectar tus redes más adelante?
- Debemos seleccionar la maxima IPv4 privada siendo esta 172.16.0.0/16 que es el maximo CIDR disponible en AWS

Question 3:

Tienes previsto crear una subred y quieres que tenga al menos capacidad para 28 instancias EC2. ¿Cuál es el tamaño mínimo que debes tener para tu subred?
- /26 ya que necesitamos 28 ips para las instancias y 5 para aws

Question 4:

Los Grupos de Seguridad operan a nivel de ................. mientras que las NACL operan a nivel de ..................
- Instancia EC2, subred

Question 5:

Has conectado una Gateway de Internet a tu VPC, pero tus instancias EC2 siguen sin tener acceso a Internet. ¿Qué **NO** es un posible problema?
- Grupos de seguridad (estos son estatales, eso quiere decir que si el trafico puede salir puede volver a entrar)

Question 6:

Te gustaría proporcionar acceso a Internet a tus instancias EC2 en subredes privadas con IPv4, asegurándote al mismo tiempo de que esta solución requiere la menor cantidad de administración y se escala sin problemas. ¿Qué deberías utilizar?
- Nat gateway

Question 7:

Se ha habilitado el VPC Peering entre la VPC A y la VPC B, y se han actualizado las tablas de rutas para la VPC A. Pero, las instancias EC2 no pueden comunicarse. ¿Cuál es el problema más probable?
- Lo mas probable es que sean las tablas de rutas de la VPC B

Question 8:

Has configurado una conexión de Conexión Directa entre tu centro de datos corporativo y tu VPC A en tu cuenta de AWS. También necesitas acceder a la VPC B en otra región de AWS desde tu centro de datos corporativo. ¿Qué debes hacer?
- Utiliza un gateway de direct connect

Question 9:

Al utilizar VPC Endpoints, ¿cuáles son los dos únicos servicios de AWS que tienen un Gateway Endpoint disponible?
- S3 y DynamoDB

Question 10:

AWS reserva 5 direcciones IP cada vez que creas una nueva subred en una VPC. Cuando creas una subred con CIDR `10.0.0.0/24`, se reservan las siguientes direcciones IP, **EXCEPTO** ....................
- se reserva la .0, .1, .2, .3 y .255

Question 11:

Tienes 3 VPCs A, B y C. Quieres establecer una VPC Peering connection entre las 3 VPCs. ¿Qué debes hacer?
- La conexion no es transitiva por lo que debe establecerse 3 peering connections: A-B, A-C, B-C

Question 12:

¿Cómo puedes capturar información sobre el tráfico IP dentro de tus VPCs?
- Habilitar los logs de flujo de la VPC

Question 13:

Si quieres una conexión de conexión directa (Direct Connect) de 500 Mbps entre tu centro de datos corporativo y AWS, elegirías una conexión ...................
- Alojada, la cantidad no amerita una conexion dedicada

Question 14:

Cuando configuras una conexión VPN de sitio a sitio de AWS entre tu centro de datos corporativo en las instalaciones y las VPC en el Cloud de AWS, ¿cuáles son los dos componentes principales que quieres configurar para esta conexión?
- Gateway Privado Virtual y Gateway del cliente

Question 15:

Tu empresa tiene varias sedes locales en Estados Unidos. Estas sedes están actualmente enlazadas mediante conexiones privadas, pero tu proveedor de conexiones privadas ha sido recientemente bastante inestable, lo que ha hecho que tu arquitectura de IT esté parcialmente desconectada. Te gustaría crear una conexión de reserva que utilizara la Internet pública para enlazar tus sedes locales, y que pudieras recuperar en caso de problemas con tu proveedor. ¿Qué recomiendas?
- AWS VPN CloudHub ya que te permite comunicarte de forma segura con varios sitios enlazados utilizando AWS VPN.

Question 16:

Tienes que establecer una conexión dedicada entre tu centro de datos corporativo local y AWS Cloud. Esta conexión debe ser privada, consistente, y el tráfico no debe viajar a través de Internet. ¿Qué servicio de AWS debes utilizar?
- AWS direct connect

Question 17:

Utilizando una conexión de Conexión Directa, puedes acceder tanto a los recursos públicos como a los privados de AWS.
- Verdadero

Question 19:

Tienes una VPC en tu cuenta de AWS que se ejecuta en modo de doble stack. Intentas continuamente lanzar una instancia EC2, pero falla. Tras una investigación más profunda, has descubierto que ya no tienes direcciones IPv4 disponibles. ¿Qué debes hacer?
- Agrega un CIDR Ipv4 adicional a tu VPC

Question 20:

El backend de una aplicación web está alojado en instancias de EC2 en subredes privadas, con un Load Balancer de aplicaciones en subredes públicas. Es necesario dar a algunos de los desarrolladores acceso a las instancias EC2 del backend, pero sin exponer las instancias EC2 del backend a Internet. Has creado una instancia EC2 de host bastión en la subred pública y has configurado el Grupo de Seguridad de las instancias EC2 backend para permitir el tráfico desde el host bastión. ¿Cuál de las siguientes es la mejor configuración para el Grupo de Seguridad del host bastión para hacerlo seguro?
- Permite el trafico desde el puerto 22 (SSH) desde el CIDR publico de la empresa

Question 21:

Una empresa ha configurado una conexión de Conexión Directa entre su centro de datos corporativo y AWS. Es necesario preparar una conexión de respaldo segura y rentable en caso de que haya problemas con esta conexión de Conexión Directa. ¿Cuál es la solución más rentable y segura que recomiendas?
- Se puede configurar una VPN site-to-site de respaldo

Question 22:

¿Qué servicio de AWS te permite proteger y controlar el tráfico en tu VPC desde la capa 3 hasta la capa 7?
- AWS Network Firewall