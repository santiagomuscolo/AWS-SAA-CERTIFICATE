### Vision general de S3
Amazon s3 es uno de los principales bloques de construccion de AWS, el mismo se anuncia como almacenamiento de "escala infinita" y sirve para:
- Copias de seguridad y almacenamiento
- Recuperacion de desastres
- Almacenamiento cloud hibrido
- Alojamiento de aplicaciones
- Alojamiento de medios
- Data Lakes y analisis de big data
- Entrega de software 
- Sitio web estatico

**Buckets**
- Amazon S3 permite almacenar objetos (archivos) en "buckets" (directorios).
- Los buckets deben traer un nombre unico global (en todas las regiones y todas las cuentas)
- Los buckets se definen a nivel de region
- S3 parece un servicio global, pero los buckets se crean a nivel de region
- Convencion de nombres
	- Sin mayusculas ni guion bajo
	- de 3 a 63 caracteres
	- no es una ip
	- debe empezar por letra minuscula o numero
	- no debe empezar por el prefijo xn--
	- no debe terminar con el sufijo -s3alias

**Objetos**
- Los objetos (archivos) tienen una clave.
- La clave es la ruta completa:
	- s3://mi-bucket/==mi-archivo.txt==
	- s3://mi-bucket/==mi_carpeta/otra_carpeta/mi-archivo.txt==
- La clave se compone de prefijo + nombre del objeto
- No existe el concepto de "directorios" dentro de los buckets (aunque la interfaz muestre lo contrario)

**Objetos (Cont.)**
- Los valores de los objetos son el contenido del cuerpo:
	- Max. size es de 5TB
	- Si se sube un objeto de mas de 5GB, se utiliza "multi-part"
- Metadatos (lista de pares clave / valor de texto - metadatos del sistema o del usuario).
- Etiquetas (par clave / valor - hasta 10) - util para la seguridad / ciclo de vida.
- ID de version (si esta activado el versionado)