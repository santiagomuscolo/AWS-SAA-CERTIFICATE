### IP privada vs publica vs elastica
- La red tiene dos tipos de IPs. IPv4 e IPv6:
  - IPv4: 1.160.10.240
  - IPv6: 3ffe:1900:4545:3:200:f8ff:fe21:67cf
  - IPv4 sigue siendo el formato mas utilizado de internet.
  - IPv6 es mas reciente y resuelve problemas del internet de las cosas (IoT).
  - El IPv4 permite 3.7 mil millones diferentes direcciones en el espacio publico.
  - IPv4: [0-255].[0-255].[0-255].[0-255].

#### Diferencias fundamentales entre IP privada y publica (IPv4)
- IP publica:
	- La IP publica significa que la maquina puede ser identificada en internet (WWW)
	- Debe ser unica en toda la red (no puede haber dos maquinas con la misma IP publica).
	- Se puede geolocalizar facilmente.

- IP privada:
  - La IP privada significa que la maquina solo puede ser identificada en una red privada.
  - La IP debe ser unica en toda la red privada.
  - PERO dos redes privadas diferentes (dos empresas) pueden tener las mismas IP.
  - Las maquinas se conectan a la WWW mediante un NAT + Gateway de internet (Un proxy).
  - Solo se puede utilizar un rango especifico de IPs como IP privada.

#### IPs elasticas
- Cuando paras y luego arrancas una instancia EC2, puede cambiar su IP publica.
- Si necesitas tener una IP publica fija para tu instancia, necesitas una IP elastica.
- Una IP elastica es una IPv4 publica que te pertenece mientras no la elimines.
- Puedes asignarla a una instancia a la vez.
- Con una direccion IP elastica, puedes enmascarar el fallo de una instancia o software reastignando rapidamente la direccion a otra instancia de tu cuenta.
- Solo puedes tener 5 Elastic IP en tu cuenta (puedes pedir a AWS que lo aumente).
- En general, intentar evitar el uso de IP elasticas:
  - Suelen reflejar malas decisiones de arquitectura
  - En su lugar, utiliza una IP publica aleatoria y registra un nombre DNS en ella.
  - O utiliza un Load Balancer y no uses una IP publica.