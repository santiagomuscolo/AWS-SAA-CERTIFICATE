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

### RDS personalizado para Oracle y Microsoft SQL
- Base de datos gestionada de Oracle y Microsoft SQL server con personalizacion del sistema operativo y de la base de datos.
- RDS: automatiza la configuracion, el funcionamiento y el escalado de la base de datos de AWS
- Personalizada: acceso a la base de datos subyacente y al SO para que puedas:
	- Configurar los ajustes
	- Instalar parches
	- Habilitar las funciones nativas
	- Acceder a la instancia EC2 subyacente mediante SSH o SSM Session Manager
- Desactivar el modo automatizacion permite realizar la personalizacion.
- RDS vd RDS Personalizada:
	- RDS: Toda la base de datos y el SO son gestionados por AWS
	- RDS pers.: Acceso administrativo completo al SO subyacente y a la base de datos.

### Amazon Aurora
 Aurora es una tecnologica propietaria de AWS que soporta MySQL y Postgres como bases de datos, la misma esta optimizada para el cloud de AWS y su rendimiento es 5 veces superor al de MySQL en RDS y mas de 3 veces superior al rendimiento de Postgres en RDS.
 El escalado del almacenamiento es automatico y el mismo va de 10 en 10 gb hasta los 128TB.
 - Puede tener hasta 15 replicas.
 - La conmutacion por error es instantanea, nativa de la Alta Disponibilidad.

**Alta disponibilidad y escalado de lectura de Aurora**
- 6 copias de tus datos en 3 AZ:
	- 4 copias de las 6 necesarias para las escrituras
	- 3 copias de las 6 necesarias para las lecturas
	- Autoreparacion con replicacion entre pares
	- El almacenamiento esta dividido en 100 volumenes
- Una instancia de Aurora se encarga de las escrituras (maestra)
- Recuperacion automatica del maestro en menos de 30 segundos
- El maestro + hasta 15 replicas de lectura de Aurora realizan lecturas.
- Soporta para la replicacion entre regiones