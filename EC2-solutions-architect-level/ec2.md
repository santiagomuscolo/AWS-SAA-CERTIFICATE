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

  ### Grupos de colocacion de EC2
- A veces quieres controlar la estrategia de colocacion de la instancia EC2.
- Esa estrategia puede definirse mediante grupos de colocacion.
- Cuando crear un grupo de colocacion, especificas una de las siguientes estrategias para el grupo:
	- Cluster: agrupa las instancias en un grupo de baja latencia en una unica zona de disponibilidad.
	- Distribuida: coloca estrictamente un pequeno grupo de instancias en distintos equipos de hardware subyacentes para reducir fallos correlacionados.
	- Particion: reparte las instancias en muchas particiones diferentes dentro de una zona de disponibilidad. Escala a cientos de instancias EC2 por grupo.

#### Cluster
- Ventajas: Gran red (10 Gbps de ancho de banda entre instancias con la red mejorada activada - recomendada).
- Contras: Si el rack falla, todas las intancias fallan al mismo tiempo.
- Caso de uso: 
	- Trabajo de Big Data que necesita completarse rapidamente.
	- Aplicacion con requerimientos de latencia minima extremadamente baja y un alto rendimiento de la red.

#### Distribuida
- Ventajas:
	- Puede abarcar varias AZ.
	- Se reduce el riesgo de fallos simultaneos.
	- Las instancias EC2 estan en hardware fisico diferente.

- Contras:
	- Limitado a 7 instancias por AZ por grupo de colocacion.

- Caso de uso:
	- Aplicacion que necesita maximizar la alta disponibilidad.
	- Aplicaciones criticas en las que cada instancia debe estar asilada de los fallos de las demas.

#### Particion
- Hasta 7 particiones por AZ.
- Puede abarcar varias AZ en la misma region.
- Hasta 100 instanicas EC2.
- Las instancias de una particion no comparten racks con las instancias de las otras particiones,
- Un fallo en la particion puede afectar a muchos EC2 pero no afectara a otras particiones.
- Las instancias EC2 tienen acceso a la informacion de la particion como metadatos.
- Caso de uso: HDFS, HBase, Cassandra, Kafka.

### Elastic Network Interfaces (ENI)
- Componente logico de una VPC que representa una tarjeta de red virtual.
- La ENI puede tener los siguientes atributos:
  - IPv4 privada primaria, una o mas IPv4 secundarias.
  - Una IP elastica (IPv4) por IPv4 privada.
  - Una IPv4 publica.
  - Uno o mas grupos de seguridad.
  - Una direccion MAC.
- Puedes crear ENI independientes y adjuntarlas sobre la marcha (moverlas) en instancias EC2 para la conmutacion por error.
- Vinculadas a una zona de disponibilidad (AZ) especifica.