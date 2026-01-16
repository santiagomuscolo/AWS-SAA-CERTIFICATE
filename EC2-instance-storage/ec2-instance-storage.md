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