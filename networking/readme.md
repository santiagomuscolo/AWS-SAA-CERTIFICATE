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