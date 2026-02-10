- RDS significa servicio de Base de Datos Regional
- Es un servicio de bases de datos gestionado para que las bases de datos utilicen SQL como lenguaje de consulta.
- Permite crear bases en el cloud que son gestionadas por AWS:
	- Postgres
	- MySQL
	- MariaDB
	- Oracle
	- Microsoft SQL Server
	- Aurora (base de datos propia de AWS)

RDS es un servicio gestionado que provee lo siguiente:
- Aprovisionamiento automatizado, parcheo del SO
- Copias de seguridad continuas y restauracion a una fecha determinada
- Dashboards de monitorizacion
- Replicas de lectura para mejorar el rendimiento de lectura
- Configuracion multi AZ para DR (Disaster Recovery)
- Ventanas de mantenimiento para actualizaciones
- Capacidad de escalado vertical y horizontal.
- Almacenamiento respaldado por EBS

**Autoescalado de almacenamiento**
- Te ayuda a aumentar el almacenamiento de tu instancia de base de datos RDS de forma dinamica
- Cuando RDS detecta que te estas quedando sin almacenamiento gratuito en la base de datos, escala automaticamente.
- Evita escalar manualmente el almacenamiento de tu base de datos.
- Se debe establecer un umbral maximo de almacenamiento
- Modifica automaticamente el almacenamiento si:
	- El almacenamiento gratuito es inferior al 10% del almacenamiento asignado
	- El almacenamiento bajo dura al menos 5 minutos
	- Han pasado 6 horas desde la ultima modificacion
- Util para aplicaciones con cargas de trabajo imprevisibles
- Soporta todos los motores de bases de datos RDS

### Replicas de lectura RDS vs Multi AZ
- Hasta 5 replicas de lectura.
- Dentro de AZ, a traves de AZ o a traves de la region.
- La replicacion en ASYNC, por lo que las lecturas son finalmente consistentes.
- Las replicas pueden ser promovidas a su propia BD.
- Las aplicaciones deben actualizar la cadena de conexion para aprovechar las replicas de lectura.

**RDS Multi AZ**
- Replicacion ASYNC
- Un nombre DNS - Conmutacion automatica de la aplicacion a la espera
- Aumenta la disponibilidad
- Conmutacion por error en caso de perdida de AZ, perdida de red, fallo de instancia o de almacenamiento
- Sin intervencion manual en las apps
- No se utiliza para escalar
- La replicacion Multi-AZ es gratis
- Las replicas de lectura deben configurarse como multi AZ para la recuperacion de desastres.