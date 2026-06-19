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
