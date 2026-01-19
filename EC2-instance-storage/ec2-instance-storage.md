### Vision general de EBS

**Que es un volumen EBS**
- Un volumen EBS (Elastic Block Store) es una unidad de red que puede adjuntar a las instancias mientras se ejecutan.
- Permite que las instancias persistan los datos, incluso despues de su finalizacion.
- Solo pueden montarse en una instancia a la vez (a nivel de CCP).
- Estan vinculados a una zona de disponibilidad especifica.
- Analogia: Piensa en ellos como una "memoria USB de red".
- Nivel gratuito: 30 GB de almacenamiento EBS gratuito de tipo proposito general (SSD) o magnetico al mes.

**Volumen EBS**
- Es una unidad de red (es decir, no es una unidad fisica)
	- Utiliza la red para comunicar la instancia, lo que significa que puede llegar a haber latencia.
	- Se puede separar de una instancia EC2 y conectarla a otra rapidamente.
- Esta bloqueado por AZ
	- Un volumen EBS en us-east-1a no puede adjuntarse a us-east-1b
	- Para trasladar un volumen, primero hay que hacer un snapshot del mismo.
- Tener una capacidad aprovisionada (size en GBs, e IOPS)
	- Se facturara toda la capacidad aprovisionada.
	- Puede aumentar la capacidad de la unidad con el tiempo.

### Vision general de EBS Snapshots

**Snapshot / Instantaneas de EBS**
- Haz una copia de seguridad (snapshot) de tu volumen EBS en un momento dado.
- No es necesario separar el volumen para hacer la instantanea, pero se recomienda.
- Puedes copiar las instantaneas a traves de AZ o region.

**Caracteristicas de los snapshots de EBS**
- Archivo de snapshots de EBS
	- Mover un snapshot a un "nivel de archivo" que es un 75% mas barato.
	- La restauracion del archivo tarda entre 24 y 72 horas.
- Papelera de reciclaje para Snapshots EBS
	- Configura reglas para retener los snapshots eliminados para poder recuperarlos despues de un borrado accidental.
	- Especifica la retencion (de 1 dia a 1 ano)

	### Vision general de AMI
- AMI = Amazon Machine Image
- Las AMI son una personalizacion de una instancia EC2
	- Agregas tu propio software, configuracion, SO, monitorizacion...
	- Tiempo de arranque/configuracion mas rapido porque todo el software esta preempaquetado.
- Las AMI se construyen para una region especifica (y pueden copiarse entre regiones).
- Puedes lanzar instancias EC2 desde:
  - Una AMI publica: proporcionada por AWS.
  - Tu propia AMI: la creas y la mantenes vos mismo.
  - Una AMI de AWS Marketplace: una AMI hecha por otra persona (y potencialmente vendida)

**Proceso AMI (desde instancia EC2)**
- Iniciar una Instancia EC2 y pesronalizarla.
- Detener la instancia (para la integridad de los datos).
- Construir una AMI - esto tambien creara instantaneas de EBS.
- Lanzar instancias desde otras AMIs.
### EC2 Instance Store
- Los volumenes EBS son unidades de red con un rendimiento bueno pero limitado.
- Si necesitas un disco de hardware de alto rendimiento, utilizas EC2 instance store.
- Mejor rendimiento de E/S
- Los almacenes de instancias EC2 pierden su almacenamiento si se detienen (son efimeros)
- Bueno para el buffer/cache/datos de memoria virtual / contenido temporal
- Riesgo de perdida de datos si el hardware falla.
- Las copias de seguridad y la replicacion son responsabilidad del usuario.

### Tipos de volumenes EBS
- Los volumenes EBS vienen en 6 tipos:
  - gp2 / gp3 (SSD): Volumen SSD de uso general que equilibra el precio y el rendimiento para una amplia variedad de cargas de trabajo.
  - io1 / io2 (SSD): El volumen SSD de mayor rednimiento para cargas de trabajo de mision critica de baja latencia o alto rendimiento.
  - st 1 (HDD): Volumen de disco duro de bajo coste disenado para cargas de trabajo de acceso frecuente y alto rendimiento.
  - sc 1 (HDD): El volumen de disco duro mas barato, disenado para cargas de trabajo de acceso menos frecuente.
- Los volumenes de EBS se caracterizan en tamano | rendimiento | IOPS (I/O Ops Per Sec)
- Solo se pueden utilizar gp2/gp3 y io1/io2 como volumenes de arranque.

**SSD DE USO GENERAL**
- Almacenamiento rentable, baja latencia.
- Volumenes de arranque del sistema, escritorios virtuales, entornos de desarrollo y prueba.
- 1 GiB - 16 TiB
- gp3:
	- Linea de base de 3.000 IOPS y rendimiento de 125 MiB/s
	- Puede aumentar las IOPS hasta 16.000 y el rendimiento hasta 1000 MiB/s de forma independiente
- gp2: 
	- Los volumenes gp2 pequenos pueden reventar las IOPS hasta 3000
	- El tamano del volumen y las IOPS estan vinculados, las IOPS maximas son 16.000
	- 3 IOPS por GB, lo que significa que con 5.334 GB estamos en el maximo de IOPS.

**IOPS Provisionadas (PIOPS) SSD**
- Aplicaciones empresariales criticas con un rendimiento sostendio de IOPS.
- O aplicaciones que necesitan mas de 16.000 IOPS.
- Excelente para las cargas de trabajo de las bases de datos (sensibles al rendimiento y a la consistencia del almacenamiento).
- io1/io2 (4 GiB - 16 TiB):
	- PIOPS maximos 64000 para isntancias Nitro EC2 y 32000 para otras.
	- Puede aumentar los PIOPS independientemente del tamano del almacenamiento.
	- io2 tiene mas durabilidad y mas IOPS por GiB.
- io2 Block Express (4 GiB - 64 TiB):
	- Latencia de menos de un milisegundo.
	- PIOPS maximas 256000 con una relacion IOPS:GiB de 1000:1
- Soporta EBS Multi-attach.

**Unidades de disco duro (HDD)**
- No puede ser un volumen de arranque.
- De 125 GiB a 16 TiB
- Disco duro de rendimiento optimizado (st1)
	- Big Data, almacenes de datos, procesamiento de logs.
	- Rendimiento maximo de 500 MiB/s - IOPS maximo de 500.
- Disco duro frio (sc1):
	- Para datos a los que se accede con poca frecuencia.
	- Escenarios en los que el menor coste es importante.
	- Rendimiento maximo de 250 MiB/s - IOPS maximas de 250.