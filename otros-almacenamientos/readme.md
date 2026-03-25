### Familia AWS Snow al detalle
La familia Snow son un conjunto de dispositivos de alta seguridad para **recopilar, procesar datos, y migrar datos hacia y desde AWS**
- Migracion de datos - Snowcone, Snowball Edge, Snowmobile
- Edge computing: Snowcone, Snowball Edge

![[Pasted image 20260324155318.png]]

#### Migraciones con AWS Snow
![[Pasted image 20260324153132.png]]

**Desafios**
- Conectividad limitada
- Ancho de banda limitado
- Alto coste de la red
- Ancho de banda compartido (no es puede maximizar la linea)
- Estabilidad de la conexion

La familia AWS Snow: provee dispositivos sin conexion para realizar migraciones de datos (si la transferencia a traves de la red tarda mas de una semana es donde entran en juego)

![[Pasted image 20260324153419.png]]

#### Snowball Edge (para la transferencia de datos)
- Solucion de transporte fisico de datos: mover TBs o PBs de datos dentro o fuera de AWS
- Alternativa a mover datos a traves de la red (y pagar tarifas de red)
- Paga por trabajo de transferencia de datos
- Proporciona almacenamiento de bloques y almacenamiento de objetos compatible con Amazon S3
- **Almacenamiento optimizado Snowball Edge**
	- 80 TB de capacidad HDD para volumen de bloques y almacenamiento compatible de objetos con S3
- **Computacion optimizada de Snowball Edge**
	- 42 TB de capacidad HDD o 28 TB de capacidad NVMe para volumen de bloques y almacenamiento de objetos compatible con S3
- Caso de uso: Migraciones al Cloud de grandes volumenes de datos, recuperacion ante desastres.

#### AWS Snowcone
- Pequeno y portatil, en cualquier lugar, robusto y seguro, resiste entornos dificiles
- Ligero (4.5 libras o 2.1 kg)
- Dispositivo utilizado para edge computing, almacenamiento y transferencia de datos
- Snowcone - 8 TB de almacenamiento HDD
- Snowcone SSD - 14 TB de almacenamiento SSD
- Utiliza Snowcone donde no quepa Snowball (entorno con limitaciones de espacio)
- Debes proporcionar tu propia bateria/cables
- Se puede enviar a AWS sin conexion, o conectarlo a internet y utilizar AWS DataSync para enviar datos

#### AWS Snowmobile
Snowmobile es un camion brindado por AWS para transferir datos
- Transfiere exabytes de datos (1 EB = 1.000 PB = 1.000.000 TBs)
- Cada Snowmobile tiene 100PB de capacidad (utiliza varias en paralelo)
- Alta seguridad: temperatura controlada, GPS, videovigilancia 24/7
- Mejor que la Snowball si transfieres mas de 10 PB


#### Proceso
1. Solicita la entrega de dispositivos snowball desde la consola de AWS
2. Instala el cliente Snowball / AWS OpsHub en tus servidores
3. Conecta el Snowball a tus servidores y copia los archivos utilizando el cliente
4. Devuelve el dispositivo cuando hayas terminado (va a la instalacion de AWS adecuada)
5. Los datos se cargaran en un bucket S3 (de tu preferencia)
6. La snowball se borra por completo

#### Que es Edge Computing?
Edge computing es el procesamiento de datos lo mas cerca posible de donde se generan en lugar de mandarlos a un servidor lejano, por ejemplo:
- En lugar de hacer dispositivo - internet - servidor en la nube - resultado - dispositivo
- Hacemos: dispositivo - procesamiento local o cercano (edge) - resultado

- Procesa los datos mientras se crean en una edge location
	- Un camion en la carretera, un barco en el mar, una mina bajo tierra... (lugares donde la conectividad es limitada)
- Configuramos un dispositivo snowball edge / snowcone para realizar edge computing
- Casos de uso de edge computing:
	- pre-procesamiento de datos
	- machine learning
	- transcodificacion de flujos multimedia

### AWS FSx

#### Vision general
- Lanzar sistemas de archivos de alto rendimiento de terceros en AWS
- Servicio totalmente gestionado
- Principales:
	- FSx para Lustre
	- FSx para windows file server
	- FSx para ONTAP de NetApp
	- FSx para OpenZFS

**Amazon FSx para windows (servidor de archivos)**
- FSx para windows es una unidad compartida del sistema de archivos de windows totalmente gestionada
- Soporta el protocolo SMB y el NTFS de windows
- Integracion con microsoft active directory, acls, cuota de usuario
- Se puede montar en instancias ec2 de linux
- Soporta los espacios de nombres del sistema de archivos distribuido (DFS) de microsoft (agrupa archivos en varios FS)
- Escala hasta 10s de GB/s, millones de IOPS 100PS, 100s PB de datos
- Opciones de almacenamiento:
	- SSD -  cargas de trabajo sensibles a la latencia (bases de datos, procesamiento de medios, analisis de datos)
	- HDD - amplio espectro de cargas de trabajo (directorio personal, cms, ...)
- Se puede acceder desde tu infraestructura local (VPN o Direct Connect)
- Puede configurarse para ser Multi-AZ (alta disponibilidad)

**Amazon FSx para Lustre**
- Lustre es un tipo de sistema de archivos distribuido en paralelo, para la informatica a gran escala.
- Su nombre deriva de linux y cluster
- Se utiliza para el aprendizaje (machine learning) continuo en la high performance computing (HPC).
- Procesamiento de video, modelado financiero, automatizacion de design electronico
- Escala hasta 100s GB/s, millones de IOPS, latencia sub-ms
- Opciones de almacenamiento:
	- SSD - baja latencia, cargas de trabajo intensivas en IOPS, operaciones de archivos pequenos y aleatorios.
	- HDD - cargas de trabajo intensivas en rendimiento, operaciones de archivos grandes y secuenciales
- Perfecta integracion con S3
	- Puede leer S3 como un sistema de archivos (a traves de FSx)
	- Puede escribir la salida de los calculos de vuelta a S3 (a traves de FSx)
- Puede utilizarse desde servidores locales (VPN o Direct Connect)

**Opciones del despliegue del sistema de archivos**
- Sistema de archivos en memoria
	- Orientado al almacenamiento a corto plazo, ahoro de costes.
	- Los datos no se replican (si falla el servidor no persisten)
	- Alta velocidad (6 veces mas rapido que el persistente, 200MBps por TiB)
	- ![[Pasted image 20260324164128.png]]
- Sistema de archivos persistente
	- Orientado al almacenamiento a largo plazo
	- Los datos se replican dentro de la misma AZ
	- Reemplaza los archivos fallidos en cuestion de segundos
	- ![[Pasted image 20260324164138.png]]

**Amazon FSx para NetApp ONTAP
- NetApp ONTAP gestionado en AWS
- Sistema de archivos compatible con el protocolo NFS, SMB, iSCSI
- Mueve las cargas de trabajo que se ejecutan en ONTAP o NAS a AWS
- Funciona con:
	- Linux
	- Windows
	- MaxOS
	- VMware Cloud en AWS
	- Amazon workspaces y AppStream 2.0
	- Amazon EC2, ECS y EKS
- El almacenamiento se reduce o crece automaticamente
- Snapshots, replicacion, bajo coste, compresion y desduplicacion de datos
- Clonacion instantanea puntual (util para probar nuevas cargas de trabajo)

![[Pasted image 20260324164604.png]]

**Amazon FSx para OpenZFS
- Sistema de archivos OpenZFS gestionado en AWS
- Sistema de archivos compatible con NFS (v3, v4, v4.1, v4.2)
- Mueve las cargas de trabajo que se ejecutan en ZFS a AWS
- Funciona con:
	- Linux
	- Windows
	- MaxOS
	- VMware Cloud en AWS
	- Amazon workspaces y AppStream 2.0
	- Hasta 1.000.000 de IOPS con una latencia de < 0.5ms
-  Snapshots, compresion y bajo coste (pero no posee desduplicacion)
- Clonacion instantanea puntual (util para probar nuevas cargas de trabajo)
- ![[Pasted image 20260324164947.png]]

### Storage Gateway
- AWS esta impuslando el concepto de "nube hibrida"
	- Parte de la infraestructura esta en el Cloud
	- Parte de la infraestructura esta en las instalaciones
- Esto puede deberse a:
	- Largas migraciones a el Cloud
	- Requisitos de seguridad
	- Requisitos de normativa
	- Estrategia de IT
- S3 es una tecnologia de almacenamiento propia (a diferencia de EFS/NFS), asi que para exponer los datos de S3 en las instalaciones se usara Storage Gateway

**Opciones nativas de la nuBe de almacenamiento de AWS**
- Bloque
	- EBS (Elastic Block Storage)
	- Almacen de instancias EC2
- Fichero
	- EFS (Elastic File System)
	- FSx
- Objeto
	- S3
	- Glacier

	### Storage Gateway
- AWS esta impuslando el concepto de "nube hibrida"
	- Parte de la infraestructura esta en el Cloud
	- Parte de la infraestructura esta en las instalaciones
- Esto puede deberse a:
	- Largas migraciones a el Cloud
	- Requisitos de seguridad
	- Requisitos de normativa
	- Estrategia de IT
- S3 es una tecnologia de almacenamiento propia (a diferencia de EFS/NFS), asi que para exponer los datos de S3 en las instalaciones se usara Storage Gateway

**Opciones nativas de la nube de almacenamiento de AWS**
- Bloque
	- EBS (Elastic Block Storage)
	- Almacen de instancias EC2
- Fichero
	- EFS (Elastic File System)
	- FSx
- Objeto
	- S3
	- Glacier

**AWS Storage Gateway**
- Puente entre los datos locales y los de el cloud
- Casos de uso:
	- recuperacion de desastres
	- copias de seguridad y restauracion
	- almacenamiento por niveles
	- cache local y acceso a archivos de baja latencia
- Tipos de gateway de almacenamiento:
	- S3 file gateway
	- gateway de archivos FSx
	- gateway de volumen
	- Tape gateway

**S3 Storage Gateway**
- Los buckets S3 configurados son accesibles mediante protocolos NFS Y SMB
- Los datos utilizados mas recientemente se almacenan en cache en el File Gateway
- Soporta Estandar S3, Estandar S3 IA, S3 One Zone A, S3 Intelligent Tiering
- Transicion a S3 Glacier mediante una politica de ciclo de vida
- Acceso a buckets mediante roles IAM para cada Gateway de archivos
- El protocolo SMB tiene integracion con Active Directory (AD) para la autenticacion de usuarios
- ![[Pasted image 20260325161150.png]]

**FSx File Gateway**
- Acceso nativo a Amazon FSx para windows file server
- Cache local para los datos a los que se accede con frecuencia
- Compatibilidad nativa con Windows (SMB, NTFS, Active Directory, ...)
- Util para grupos de archivos compartidos y directorios personales
- ![[Pasted image 20260325161345.png]]

**Volume Gateway**
- Almacenamiento en bloque con protocolo iSCSI respaldado por S3
- Respaldado por Snapshots de EBS que pueden ayudar a restaurar los volumenes locales
- **Volumenes de cache**: acceso de baja latencia a los datos mas recientes
- **Volumenes almacenados**: todo el conjunto de datos esta en las instalaciones, copias de seguridad programadas en S3
- ![[Pasted image 20260325161643.png]]

**Tape Gateway**
- Algunas empresas tienen procesos de copia de seguridad que utilizan cintas fisicas
- Con Tape Gateway, las empresas utilizan los mismos procesos pero en el cloud
- Biblioteca virtual de cintas (VTL) respaldada por Amazon S3 y Glacier
- Realiza copias de seguridad de los datos utilizando los procesos existentes basados en cintas (y la interfaz iSCSI)
- Funciona con los principales proveedores de sogtware de copia de seguridad
- ![[Pasted image 20260325162012.png]]

**Dispositivo de hardware**
- Utilizar Storage Gateway significa que necesitas virtualizacion in situ
- Si no, puedes utilizar un dispositivo de hardware Storage Gateway (vendidos por amazon)
- Funciona con File Gateway, Volume Gateway, Tape Gateway
- Tiene lso recursos necesarios de CPU, memoria, red y cache SSD
- Util para copias de seguridad NFS diarias en centros de datos pequenos

![[Pasted image 20260325162458.png]]

**Protocolos nombrados**
NFS, SMB, iSCSI/VTL son protocolos de acceso a almacenamiento, que trabajan en diferentes niveles:
- File level (nivel de archivos):
	- NFS (Network File System): protocolo para compartir archivos en red muy usado en linux/unix.
	- SMB (Server Message Block): Protocolo de comparticion de archivos en windows
- Block level (nivel disco)
	- iSCSI (Internet Small Computer Systems Interface): protocolo que permite usar almacenamiento remoto como si fuera un disco local.
	- iSCSI VTL (Virtual Tape Library): una simulacion de una libreria de cintas (tapes) usando el protocolo Internet Small Computer Systems interface

### Familia de transferencia de AWS
- Un servicio totalmente gestionado para la transferencia de archivos hacia y desde amazon S3 o Amazon EFS mediante el protocolo FTP
- Protocolos soportados:
	- AWS Transfer para FTP (protocolo de transferencia de archivos)
	- AWS transfer para FTPS (protocolo de transferencia de archivos con SSL)
	- AWS transfer para SFTP (protocolo de transferencia de archivos seguro)
- Infraestructura administrada, escalable, fiable, altamente disponible (multi-AZ)
- Paga por endpoint aprovisionado por hora + transferencias de datos en GB
- Almacena y gestiona las credenciales de los usuarios dentro del servicio
- Se integra con los sistemas de autenticacion existentes (Microsoft Advice Directory, LDAP, Okta, Amazon Cognito, personalizado)
- Uso: compartir archivos, conjuntos de datos publicos, CRM, ERP
- ![[Pasted image 20260325204033.png]]

### Vision general de DataSync
- Mover grandes cantidades de datos hacia y desde
	- En las instalaciones/otra nube a AWS (NFS, SMB, HDFS, API S3...) - necesita agente
	- De AWS a AWS (diferentes servicios de almacenamiento) - no necesita agente
- Puedes sincronizar a:
	- Amazon S3 (Cuaqluier clase de almacenamiento - incluido Glacier)
	- Amazon EFS
	- Amazon FSx
- Las tareas de replicacion son programables por hora, dia, semana
- Se conservan los permisos y metadatos de los archivos (NFS POSIX, SMB...)
- Una tarea de agente puede utilizar 10GBps, se puede configurar un limite de ancho de banda
- ![[Pasted image 20260325204659.png]]
- ![[Pasted image 20260325204617.png]]

### Comparacion
![[Pasted image 20260325205056.png]]

Definiciones que no conocia:
- POSIX: es un estandar (Portable Operating System Interface) que define como deberia comportarse un sistema operativo tipo unix, incluyendo: gestion se archivos, permisos, procesos y APIs del sistema. Cada archivo tiene permisos como lectura, escritura, ejecucion y presenta una jerarquizacion y disponibilizacion de funciones.
- Sistema HDFS: Es un sistema de archivos distribuido, disenado para manejar grandes volumenes de datos forma parte del ecosistema "Apache Hadoop"
- Termino "sistema de archivos distribuido": el termino distribuido quiere decir que los datos no se guardan en un solo "nodo" sino que estos estan repartidos en varios nodos que trabajan en conjunto como si fueran un solo sistema