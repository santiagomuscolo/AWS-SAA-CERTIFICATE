**Amazon EC2**

- EC2 es una de las ofertas mas populares de AWS.
- EC2 = Elastic Compute Cloud = Infraestructura como servicio (IaaS)
- Consiste principalmente en la capacidad de:
  - Alquilar maquinas virtuales (EC2)
  - Almacenar datos en unidades virtuales (EBS)
  - Distribuir la carga entre maquinas (ELB)
  - Escalar los servicios mediante Auto Scaling Group (ASG)

**Opciones de size y configuration de EC2**

- Sistema operativo (OS): Linux, Windows o Mac OS.
- Cuanta potencia de calculo y nucleos (CPU)
- Cuanta memoria de acceso aleatorio (RAM)
- Cuanto espacio de almacenamiento:
  - Conectado a la red (EBS y EFS)
  - Hardware (EC2 instance Store)
- Tarjeta de red: velocidad de la tarjeta, direccion IP publica.
- Reglas de firewall: grupo de seguridad.
- Script de arranque (configurar el primer lanzamiento): Datos de usuario de EC2

**Datos del usuario de EC2**

- Es posible arrancar nuestras instancias utilizando un script de datos de usuario de EC2.
- bootstrapping significa lanzar comandos cuando una maquina se inicia.
- Ese script solo se ejecuta una vez en el primer arranque de la instancia.
- Los datos de usuario EC2 se utilizan para automatizar tareas de arranque como:
  - Instalar actualizaciones.
  - Instalacion de software.
  - Descarga de archivos de internet.
  - etc…

**Tipos basicos de instancias EC2**

- Puedes utilizar diferentes tipos de instancias de EC2 optimizadas para diferentes casos de uso.
- Aws tiene la siguiente convencion de nombres -> m5.2xlarge donde:
  - m: Es la clase de instancia
  - 5: Generacion (AWS los mejora con el tiempo)
  - 2xlarge: Tamaño dentro de la clase de la instancia

**Tipos de instancias de EC2 - Proposito general**

- Ecelente para una diversidad de cargas de trabajo, como servidores web o repositorios de codigo.
- Equilibrio entre:
  - Computacion
  - Memoria
  - Red

**Tipos de instancias EC3 - Computacion optimizada**

- Ideal para tareas de calculo intensivo que requieren procesadores de alto rendimiento:
  - Cargas de trabajo de procesamiento por lotes
  - Transcodificacion de medios
  - Servidores web de alto rendimiento
  - Computacion de alto rendimiento (HPC)
  - Modelado cientifico y aprendizaje automatico
  - Servidores dedicados a juegos

**Tipos de instancias EC2 - Memoria optimizada**

- Rapido rendimiento en cargas de trabajo que procesan grandes conjuntos de datos en memoria
  - Alto rendimiento, bases de datos relacionales/no relacionales.
  - Almacenes de cache distribuidos a escala web
  - Bases de datos en memoria optimizadas para BI (Business Intelligence)
  - Aplicaciones que realizan el procesamiento en tiempo real de grandes datos no estructurados.

**Tipos de instancias EC2 - Almacenamiento optimizado**

- Ideal para tareas de almacenamiento intensivo que requieran un acceso alto y secuencial de lectura y escritura a grandes conjuntos de datos en el almacenamiento local.
  - Sistemas de procesamiento de transacciones en linea (OTLP) de alta frecuencia.
  - Bases de datos relacionales y NoSQL.
  - cache para bases de datos en memoria (por ejemplo, Redis)
  - Aplicaciones de almacenamiento de datos
  - Sistema de archivos distribuidos

Ejemplo

| Instancia   | vCPU | Mem (GiB) | Almacenamiento   | Rendimiento de la red | Ancho de banda de EBS (Mbps) |
| ----------- | ---- | --------- | ---------------- | --------------------- | ---------------------------- |
| t2.micro    | 1    | 1         | solo EBS         | Bajo a moderado       |                              |
| t2.xlarge   | 4    | 16        | solo EBS         | Moderado              |                              |
| c5d.4xlarge | 16   | 32        | 1 x 400 NVMe SSD | Hasta 10 Gbps         | 4.750                        |
| r5.16xlarge | 64   | 512       | solo EBS         | 20 Gbps               | 13.600                       |
| m5.8xlarge  | 32   | 128       | Solo EBS         | 10 Gbps               | 6.800                        |

### **Grupos de seguridad y puertos clasicos**

- Los grupos de seguridad son la base de la seguridad en AWS.
- Controlan como se permite el trafico dentro o fuera de nuestras instancias EC2.
- Los grupos de seguridad solo contienen reglas de permiso.
- Las reglas de los grupos de seguridad pueden hacer referencia por IP o por grupo de seguridad.

### **Grupos de seguridad inmersion mas profunda**

- Los grupos de seguridad actuan como un "firewall" en las instancias de EC2.
- Regulan:
  - El acceso a los puertos.
  - Rangos de IP autorizados - IPv4 e IPv6.
  - Control de la red de entrada (de otros a la instancia).
  - Control de la red de salida (de la instancia hacia otra).

### **Grupos de seguridad es bueno saber**

- Puede adjuntarse a multiples instancias.
- Bloqueado a una combinacion de region/VPC.
- Vive fuera del EC2 - si el trafico esta bloqueado, la instancia EC2 no lo vera.
- Es bueno mantener un grupo de seguridad separado para el acceso SSH.
- Si tu aplicacion no es accesible (tiempo de espera), entonces es un problema de grupo de seguridad.
- Si tu aplicacion de un error de "conexion rechazada", etnonces es un error de la aplicacion o no se ha lanzado.
- Todo el trafico de entrada esta bloqueado por defecto.
- Todo el trafico de salida esta autorizado por defecto.

### **Puertos clasicos que hay que conocer**

- 22= SSH (Secure Shell) - iniciar sesion en una instancia de linux.
- 21 = FTP (File Transfer Protocol) - subir archivos a un archivo compartido.
- 22 = SFTP (Secure File Transfer Protocol) - subir archivos usando SSH.
- 80 = HTTP - acceso a sitios web no seguros.
- 443 = HTTPS - acceso a sitios web seguros.
- 3389 = RDP (Remote Desktop Protocol) - iniciar sesion en una instancia de Windows.

### **Opciones de compra de instancias EC2**

- Instancias bajo demanda: carga de trabajo corta, precio predecible, pago por segundos.
- Reservadas (1 y 3 years):
  - Instancias reservadas - cargas de trabajo largas.
  - Instancias reservadas convertibles - cargas de trabajo largas con instancias flexibles.
- Planes de ahorro (1 y 3 years) - compromiso con una cantidad de uso, carga de trabajo larga.
- Instancias Spot - cargas de trabajo cortas, baratas, pueden perder instancias (menos fiables).
- Hosts dedicados: reserve un servidor fisico completo, controle la ubicacion de las instancias.
- Instancias dedicadas - ningun otro cliente compartira tu hardware.
- Reservas de capacidad - reserva de capacidad en una AZ especifica para cualquier duracion.

  #### **EC2 bajo demanda**

  - Paga por lo que usas:
    - Linux o Windos - facturacion por segundo, despues del primer minuto.
    - El resto de sistemas operativos se facturan por hora.
  - Tiene el coste mas elevado, pero no hay que pagar por adelantado.
  - Sin compromiso a largo plazo.
  - Recomendado para cargas de trabajo ac orto plazo y sin interrupciones, cuando no se puede predecir el comportamiento de la app.

  #### **Instancias reservadas de EC2**

  - Tienen un % de descuento en comparacion con el servicio bajo demanda.
  - Reserva de atributos de instancia especificos (tipos de instancia, region, ocupacion, sistema, operativo).
  - Periodo de reserva - 1 year (+descuento) o 3 years (+++descuento).
  - Opciones de pago - sin pago inicial(+), pago parcial(++), pago total(+++).
  - Alcance de la isntancia reservada - Por region o por zona (capacidad de reserva en una AZ).
  - Recomendado para aplicaciones de uso constante (piensa en una base de datos).
  - Puedes comprar y vender en el Marketplace de instancias reservadas

  #### **Instancias reservadas convertibles de EC2**

  - Puedes cambiar el tipo de la instancia EC2, la familia de instancias, el SO, etc.
  - Tienen un % de descuento.

  #### **Planes de ahorro EC2**

  - Obten un descuento basado en el uso a largo plazo.
  - Comprometete a un determinado tipo de uso.
  - El uso mas alla de los planes de ahorro de EC2 se factura al precio bajo demanda.
  - Bloqueado a una familia de instancias especificas y a una region de AWS.
  - Flexible a traves de:
    - Size de la instancia.
    - OS.
    - Tenencia.

  #### **Instancias EC2 Hot Spot**

  - Puedes obtener un descuento de hasta el 90% en comparacion con la demanda.
  - Instancias que puedes "perder" en cualquier momento si su precio maximo es inferior al precio spot actual.
  - Las instancias Mas rentables de AWS.
  - Util para las carga de trabajo que son resistentes a fallos:
    - Trabajos por lotes.
    - Analisis de datos.
    - Procesamiento de imagenes.
    - Cualquier carga de trabajo distribuida.
    - Cargas de trabajo con una hora de inicio y finalizacion flexible.
  - No es adecuado para tabajos criticos o bases de datos.

  #### **Hosts dedicados EC2**

  - Un servidor fisico con capacidad de instancia EC2 totalmente dedicado a su uso.
  - Permite abordar los requisitos de normativas y utilizar licencias de software vinculadas al servidor existentes (licencias de software por socket, por nucleo, por VM).
  - Opciones de compra:
    - Bajo demanda - pago por segundo para el host dedicado activo.
    - Reservado - 1 a 3 years.
  - La opcion mas cara.
  - Util para el software que tiene un modelo de licencia complicado (BYOL - Bring Your Own License).
  - O para empresas que tienen fuertes necesidades de regulacion o cumplimiento.

  #### **Instancias dedicadas de EC2**

  - Las instancias se ejecutan en un hardware dedicado para ti.
  - Puedes compartir el hardware con otras instancias de la misma cuenta.
  - No hay control sobre la ubicacion de las instancias (se puede mover a hardware despues de la parada/arranque).

  #### **Reservas de capacidad de EC2**

  - Reserva la capacidad de las instancias bajo demanada en una AZ especifica para cualquier duracion.
  - Siempre tendras acceso a la capacidad de EC2 cuando la necesites.
  - Sin compromiso de tiempo, sin descuentos de facturacion.
  - Combina con las instancias regionales reservadas y los planes de ahorro para beneficiarte de descuentos en la facturacion.
  - Se te cobra la tarifa bajo demanda tanto si se ejecuta instancias como si no.
  - Adecuado para cargas de trabajo ininterrumpidas a corto palzo que necesitan estar en una AZ especifica.

  ### Instancias Spot y Flota Spot

- Puedes obtener un descuento de hasta 90% en comparacion con la demanda.
- Define el precio spot maximo y obten la instancia mientras el precio spot actual sea < maximo.
  - El precio spot por hora varia en funcion de la oferta y capacidad.
  - Si el precio spot actual > tu precio maximo, puedes elegir parar o termianr tu instancia con un periodo de gracia de 2 minutos.
- Otra estrategia: Bloqueo de Spot
  - "Bloquea" la instancia Spot durante un periodo de tiempo determinado (de 1 a 6 horas) sin interrupciones.
  - En raras situaciones, la instancia puede ser reclamada.
- Se utiliza para trabajos por lotes, analisis de datos o cargas de trabajo resistentes a los fallos.
- No es ideal para trabajos criticos o bases de datos.

  \*\*Flota Spot

  - Flotas Spot = conjunto de instancias de Spot + (opcional) instancias bajo demanda.
  - La Flota Spot tratara de alcanzar la capacidad objetivo con restricciones de precio.
    - Define los posibles pools de lanzamiento: tipo de instanica, SO, AZ.
    - Puede tener varios pools de lanzamiento, para que la flota pueda elegir.
    - La Flota Spot deja de lanzar instancias cuando alcanza la capacidad o el coste maximo.
  - Estrategias para asignar instancias de Spot:
    - Bajo precio: desde el pool con el precio mas bajo (optimizacion de costes, carga de trabajo corta).
    - Diversificado: distribucion en todos los pools (gran disponibilidad, cargas de trabajo largas).
    - Capacidad optimizada: pool con la capacidad optima para el numero de instancias.
  - Las Flotas de Spot nos permites solicitar automaticamente las instancias de Spot con el precio mas bajo.
