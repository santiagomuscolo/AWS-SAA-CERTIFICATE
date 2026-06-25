## Vision general de disaster recovery

>[!info] Disaster se le dice a cualquier acontecimiento que genere un impacto negativo en la continuidad de la actividad o en las finanzas de una empresa

Aqui nace el termino **Disaster Recovery** adoptado para la preparacion y recuperacion de dicha catastrofe.

**Que tipo de recuperacion ante desastres existen?**
- En las instalaciones => En las instalaciones: DR tradicional y muy caro
- En las instalaciones => Cloud de AWS: recuperacion hibrida
- Region A de AWS Cloud => Region B de AWS Cloud

Durante el transcurso de esta explicacion se dara uso a dos terminos:
- RPO: Recovery Point Objective (Objetivo de punto de recuperacion)
- RTO: Recovery Time Objective (Objetivo de tiempo de recuperacion)

**RTO y RPO**
![[Pasted image 20260617142537.png]]
- RTO: Es el tiempo maximo que podes estar caido despues de un incidente, por ejemplo: tenes que recuperar el sistema en menos de 1 hora si o si.
	“¿Cuánto tiempo puedo tener el sistema offline sin que sea un desastre?”

- RPO: Es la cantidad maxima de datos que podes perder, por ejemplo: poder perder como mucho los ultimos 10 minutos de datos.
	“¿Hasta qué punto en el pasado puedo volver sin que sea crítico?”

**Estrategias de recuperacion en caso de catastrofe**
- Copia de seguridad y restauracion
- Luz piloto
- Espera caliente
- Enfoque Hot site / Multi site

**Copia de seguridad y restauracion (RPO alto)**
![[Pasted image 20260618134606.png]]

- Columna 1: donde viven los datos (on-premise & AWS Cloud)
- Columna 2: donde se guardan (backup)
- Columna 3: como los re-utilizas (restore/deploy)

**Pilot light**
![[Pasted image 20260618140625.png]]

la luz piloto consta de replicar los datos que tenemos on-premise en el cloud siendo consumidos por una pequena version de nuestra aplicacion que siempre se ejecuta, es mas rapido que la restauracion tipica ya que los sistemas criticos se encuentran en marcha.

**Espera caliente**
![[Pasted image 20260618141030.png]]

A diferencia de la luz piloto donde solo lo escencial (critico) esta prendido (sin autoscaling, se escala lo minimo a full) en la espera caliente tenemos un mini sistema listo para soportar la carga productiva ya corriendo, esto se basa en el failover + autoscaling, aumentando su costo pero obteniendo un RPO mucho mas rapido.

**Multi-site/ hot site**
![[Pasted image 20260618141606.png]]

El enfoque multi-site es el mas costoso de todos, en el mismo tenemos activo tanto la aplicacion productiva en las instalaciones on-premise y una replica exacta en el cloud en la que se replican los datos de forma constante, permitiendonos breves minutos mediante conmutacion por error para switchear entre una y otra.

**Consejos para la recuperacion en caso de catastrofes**
- Copias de seguridad
	- Los snapshots de EBS, copias de seguridad automaticas de RDS / Snapshots, etc...
	- Envios regulares a S3 / S3 IA / Glacier, Politica de ciclo de vida, replicacion entre regiones
	- Desde las instalaciones: Snowball o Storage Gateway
- Alta disponibilidad
	- Utiliza Route53 para migrar DNS de una region a otra
	- RDS Multi AZ, ElastiCache Multi AZ, EFS, S3
	- VPN site-to-site como recuperacion de Direct Connect
- Replicacion
	- Replicacion RDS (entre regiones), AWS Aurora + bases de datos globales
	- Replicacion de bases de datos entre las instalaciones de AWS
	- Gateway de almacenamiento
- Automatizacion
	- CloudFormation / Elastic Beanstalk para volver a crear un entorno completamente nuevo
	- Recuperar / Reiniciar instancias EC2 con CloudWatch si fallan las alarmas
	- Funciones AWS Lambda para automatizaciones personalizadas
- Caos
	- Netflix tiene un "ejercito de monos" que termina EC2 aleatoriamente (lo hace una lib llamada chaosMonkey)

## DMS - Servicio de migracion de bases de datos
DMS es un servicio de AWS que permite migrar bases de datos a AWS de forma segura y autorreparable sin inhibir la base de datos de origen durante el proceso, soportando:
- Migraciones homogeneas: por ejemplo, de Oracle a Oracle
- Migraciones heterogeneas: ex microsoft SQL server a Aurora
![[Pasted image 20260619130844.png]]

**Fuentes y objetivos de DMS**
- Fuentes
	- Bases de datos locales e instancias EC2: Oracle, MS SQL Server, MySQL, MariaDB, PostgreSQL, MongoDB, SAP, DB2
	- Azure: base de datos Azure SQL
	- Amazon RDS: todos, incluido aurora
	- S3
- Objetivos:
	 - Bases de datos locales e instancias EC2: Oracle, MS SQL Server, MySQL, MariaDB, PostgreSQL, SAP
	 - RDS
	 - Redshift
	 - DynamoDB
	 - S3
	 - OpenSeach
	 - Flujos de datos kinesis

**Herramients de conversion de esquemas de AWS (SCT)**
SCT convierte el esquema de tu base de datos de un motor a otro por ejemplo:
- OLTP (SQL server u Oracle): a MySQL, Postgre o Aurora
- OLAP (Teradata u Oracle): a redshift

## Migraciones RDS y Aurora

**MySQL**
- RDS MySQL a aurora MySQL
	- Opcion 1: Snapshots de BD de MySQL RDS restaurados como BD Aurora MySQL
	- Opcion 2: Crea una replica de lectura Aurora a partir de tu RDS MySQL, y cuando el retardo de replicacion sea 0, promuevela como su propio Cluster de BD (puede llevar tiempo y costar $)
- MySQL externo a MySQL Aurora
	- Opcion 1: Utilizar Percona XtraBackup para crear una copia de seguridad de archivos en Amazon S3, luego crear una BD Aurora MySQL desde Amazon S3
	- Opcion 2: Crear una BD MySQL de Aurora y utilizad la utilidad mysqldump para migrar MySQL a Aurora (mas lento que el metodo S3)
- Utilizar DMS si ambas bases estan en funcionamiento
- ![[Pasted image 20260622205917.png]]

**PostgreSQL**
Dentro de AWS la migracion es identica a la de MySQL pero con una Postgres externa es ligeramente diferente:
- Se puede crear una copia de seguridad y ponerla en Amazon S3 para luego importarla utilizando la extension aws_s3 de Aurora
Y por ultimo podemos usar DMS como en MySQL
![[Pasted image 20260622210115.png]]
## Estrategia on-premise con AWS
- Posibilidad de descargar Amazon Linux 2 AMI como VM (formato .iso)
- Importacion / Exportacion de VM
	- Migrar aplicaciones existentes a EC2
	- Crea una estrategia de repositorio DR para tus VM locales
	- Puedes volver a exportar las maquinas virtuales de EC2 a las locales
- Servicio de descubrimiento de aplicaciones de AWS
	- Recopila informacion sobre tus servidores locales para planificar una migracion
	- Utilizacion de servidores y asignaciones de dependencias
	- Realizar un seguimiento con AWS Migration Hub
- Servicio de Migracion de Bases de datos AWS (DMS)
	- Replica On-premise => AWS, AWS => AWS, AWS => On-premise
	- Funciona con varias tecnologias de bases de datos (Oracle, MySQL, DynamoDB, etc...)
- Servicio de migracion de servidores de AWS (SMS)
	- Replicacion incremental de servidores activos locales a AWS

## AWS Backup
Backup es un servicio que nos permitira centralizar y gestionar backups en AWS, soportando servicios como:
- EC2 / EBS
- S3
- RDS / Aurora / DynamoDB
- DocumentDB / Neptune
- EFS / FSx (Lustre y servidor de archivos de Windows)
- Storage Gateway (Volume Gateway)

Un punto clave de este servicio es que soporta backups entre regiones y entre cuentas. Por otro lado, este servicio nos brinda **politicas de seguridad** conocidas tambien como **planes de copia de seguridad**
- Frecuencia de la copia de seguridad
- Ventana de la copia de seguridad
- Transicion al almacenamiento en frio
- Periodo de retencion

## AWS Application Discovery service
Este servicio nos permite planificar la migracion de proyectos a AWS recopilando informacion sobre centros de datos locales, actualmente nos ofrece dos opciones:
- Descubrimiento sin agente
	- Inventario de maquinas virtuales, configuracion e historial de rendimiento, uso de la CPU, la memoria y el disco.
- Descubrimiento basado en agentes (AWS Application Discovery Agent)
	- Configuracion del sistema, rendimiento del sistema, procesos en ejecucion y detalles de las conexiones de red entre sistemas.

Todos estos datos recopilados son visibles en AWS Migration Hub

**AWS Application Migration Service (MGN)**
Este servicio nos permite mediante una solucion lift-and-shift simplificar la migracion de aplicaciones a AWS, convirtiendo servidores fisicos, virtuales y basados en la nube para que se ejecuten de manera nativa en AWS
![[Pasted image 20260624170835.png]]

## Transferir grandes conjuntos de datos a AWS
- Ejemplo: transferir 200TB de datos en el Cloud. Teniendo una conexion a internet de 100 Mbps.
- **A traves de internet / VPN site-to-site**
	- Configuracion inmediata
	- Tardara 200TBx1000gbx1000mbx8mb/100mbs = 16.000.000s = 185d
- **Sobre direct connect 1Gbps**
	- Mucho tiempo para la configuracion unica (mas de un mes)
	- Tardara 200TBx1000gbx8gb/1 gbps = 1.600.000s = 18.5d
- **Sobre snowball**
	- Llevara de 2 a 3 snowballs en paralelo
	- La transferencia de extremo a extremo se podria hacer en 1 semana
	- Puede combinarse con Database Migration Service
- **Para replicacion/transferencias en curso**
	- VPN site-to-site o DX con DMS o DataSync

## VMware Cloud en AWS
Para empezar debemos saber que es una VMware:
VMware es un software que permite crear maquinas virtuales dentro de tu propia computadora, algo asi como tener muchas submaquinas dentro de la tuya cada una con su propio OS.
Ahora bien en AWS frente a la migracion de VMware desde las instalaciones al cloud se nos presenta VMware cloud permitiendo seguir utilizando el software aun en la infrestructura basada en la nube de Amazon
![[Pasted image 20260624174159.png]]

Casos practicos:
- Migrar capas de trabajo basadas en VMware vSphere a AWS.
- Ejecuta tus cargas de trabajo de produccion en entornos de nube privada, publica e hibrida basados en VMware vSphere.
- Tener una estrategia de recuperacion de desastres.

## Quiz

Question 1:

Como parte de tu plan de recuperación de desastres, te gustaría tener sólo la infraestructura crítica en funcionamiento en AWS. No te importa un Objetivo de Tiempo de Recuperación (RTO) más largo. ¿Qué estrategia de RD recomiendas?
- Luz piloto, este maneja lo minimo e indispensable para sobrevivir

Question 2:

Te gustaría obtener la estrategia de Recuperación de Desastres con el menor Objetivo de Tiempo de Recuperación (RTO) y Objetivo de Punto de Recuperación (RPO), independientemente del coste. ¿Qué DR deberías elegir?
- Estrategia multisitio, es una replica funcional exactamente identica de la app principal

Question 3:

¿Cuál de las siguientes estrategias de Recuperación de Desastres tiene un Objetivo de Punto de Recuperación (RPO) y un Objetivo de Tiempo de Recuperación (RTO) potencialmente altos?
- Copia de seguridad y restauracion (backup)

Question 4:

Quieres hacer un plan de Recuperación de Desastres en el que tengas una versión reducida de tu sistema en funcionamiento, y cuando ocurra un desastre, se amplíe rápidamente. ¿Qué estrategia de RD debes elegir?
- Espera caliente, es la unica version reducida que puede escalar

Question 5:

Tienes una base de datos Oracle local que quieres migrar a AWS, concretamente a Amazon Aurora. ¿Cómo harías la migración?
- Primero se utiliza SCT (schema conversion tool) para convertir los esquemas y luego DMS (database migration service) para migrar los datos.

Question 6:

Tienes archivos y documentos sensibles on-premise que quieres sincronizar regularmente a AWS para mantener otra copia. ¿Qué servicio de AWS puede ayudarte con eso?
- AWS DataSync es ideal para esto ya que se especializa en la transferencia de datos en linea con una velocidad decente.

Question 7:

AWS DataSync soporta las siguientes ubicaciones, EXCEPTO ....................
- ==AWS EBS... solamente soporta EFS, FSx y S3==

Por que?
EBS funciona a nivel bloque (maneja bloques de disco a bajo nivel) y es montado dentro de una EC2 por lo que no es directamente accesible por internet, su contraparte EFS si es aceptada por que es accesible por internet y funciona a nivel Path que es lo que DataSync necesita.

Question 8:

Estás ejecutando muchos recursos en AWS, como instancias EC2, volúmenes EBS, tablas DynamoDB... Quieres una forma fácil de gestionar las copias de seguridad de todos estos servicios de AWS desde un único lugar. ¿Qué oferta de AWS facilita este proceso?
- AWS Backup

Question 9:

Una empresa planea migrar sus sitios web, aplicaciones, servidores, máquinas virtuales y datos existentes a AWS. Quieren hacer una migración con un tiempo de inactividad mínimo y costes reducidos. ¿Qué servicio de AWS puede ayudar en este escenario?
- AWS Application Migration Service

Question 10:

Una empresa utiliza VMware en su centro de datos local para gestionar su infraestructura. Se necesita ampliar su centro de datos y su infraestructura a AWS, pero seguir utilizando el stack tecnológico que usan, que es VMware. ¿Qué servicio de AWS pueden utilizar?
- VMware cloud AWS

Question 11:

Una empresa utiliza RDS para MySQL como base de datos principal, pero últimamente se enfrenta a problemas de gestión de la base de datos, de rendimiento y de escalabilidad. Y han decidido utilizar Aurora para MySQL en su lugar para obtener un mejor rendimiento, menos complejidad y menos tareas administrativas necesarias. ¿Cuál es la mejor manera y la más rentable de migrar de RDS para MySQL a Aurora para MySQL?
- Se puede crear una snaphost de RDS y restaurarla en Aurora MySQL

Question 12:

¿Qué servicio de AWS puedes utilizar para automatizar la copia de seguridad en diferentes servicios de AWS como RDS, DynamoDB, Aurora y los sistemas de archivos EFS y volúmenes EBS?
- Copia de seguridad de AWS (AWS Backup)