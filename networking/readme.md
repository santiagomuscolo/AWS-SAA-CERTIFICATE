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

