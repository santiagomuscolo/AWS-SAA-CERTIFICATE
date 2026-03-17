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

### Seguridad S3 - Politica del bucket

**Basada en usuario**
- Politicas IAM - que llamadas a la API deben permitirse a un usuario concreto desde IAM

**Basada en recursos**
- Politicas de bucket - reglas para todo el bucket desde la consola de S3 - permite cuentas cruzadas.
- Lista de control de acceso a objetos (ACL) - nivel de  detalle profundo 
- Lista de control de acceso a bucket (ACL) - menos comun

- Nota: un usuario IAM puede acceder a un objeto S3 si:
  - Los permisos de IAM del usuario LO PERMITEN O la politica de recursos LO PERMITE
  - Y no hay una DENEGACION explicita.

**Cifrado**
Cifra los objetos en Amazon S3 utilizando claves de cifrado

**Politicas de bucket S3**
- Politicas basadas en JSON
	- Resource: buckets y objetos
	- Effect: permitir (Allow) o denegar (Deny)
	- Action: conjunto de API a permitir o denegar
	- Principal: la cuenta o usuario al que aplicar la politica.

Estas politicas son utilizadas para:
- Conceder acceso publico al bucket
- Forzar que los objetos se cifren al subirlos
- Conceder acceso a otra cuenta (cuenta cruzada)

### Vision general de S3

**Alojamiento de sitios web estaticos**
- S3 puede alojar sitios web estaticos y hacerlos accesibles en internet.
- La URL del sitio web sera (dependiendo de la region)
	- http://bucket-name.s3-website-aws-region.amazonaws.com
	- http://bucket-name.s3-website.aws-region.amazonaws.com
- Si recibes un error 403 forbidden deberas chequear las politicas del bucket que habilitan lecturas publicas.

### Versionado S3
- Puedes versionar tus archivos en Amazon S3
- Se activa a nivel de bucket
- La misma clave de sobrescritura cambiara la "version"
- Es una buena practica versionar tus buckets
	- Protege contra borrados involuntarios
	- Rollin facil a la version anterior
- Nota:
	- Cualquier archivo no versionado antes de activar versionado tendra version nula
	- Suspender el versionado no elimina las versiones anteriores

### Replicacion S3
- Debes activar el versionado en los buckets de origen y destino
- Replicacion entre regiones (CRR) 
- Replicacion en la misma region (SSR)
- Los buckets pueden estar en diferentes cuentas de AWS
- La copia es asincrona
- Debes dar los permisos de IAM adecuados a S3

**Casos de uso**
- CRR - normativa, acceso de menor latencia, replicacion entre cuentas.
- SSR - agregacion de logs, replicacion en vivo entre cuentas de produccion y de test

### Notas de replicacion S3
- Despues de activar la replicacion, solo se replican los objetos nuevos.
- Opcionalmente, puedes replicar los objetos existentes utilizando la replicacion por lotes de S3
	- Replica los objetos existentes y los objetos que fallaron en la replicacion
- Para las operaciones de borrado
	- Puede replicar los marcadores de borrado del origen al destino
	- Los borrados con un ID de version no se replican (para evitar borrados maliciosos)
- No hay "encadenamiento" de la replicacion

### Vision general de clases de almacenamiento S3

**Familia S3 standard**
- Amazon S3 standard - Proposito general
- Amazon S3 standard-infrequent-access (IA)
- Amazon S3 One Zone-infrequent-access

**Familia S3 glacier**
- Amazon S3 Glacier Instant retrieval
- Amazon S3 Glacier Flexible Retrieval
- Amazon S3 Glacier Deep Archivo

**OTROS**
- Amazon S3 intelligent Tiering

#### S3 durabilidad y disponibilidad

**Durabilidad**
- Alta durabilidad (99,99999999999%) de los objetos a traves de multiples AZ
- Si almacenas 10.000.000 de objetos con S3, puedes esperar una media de perdida de un solo objeto una vez cada 10.000 anos.
- Lo mismo para todas las clases de almacenamiento.

**Disponibilidad**
- Mide la disponibilidad de un servicio.
- Varia en funcion de la clase de almacenamiento.
- Ejemplo: El estandar S3 tiene una disponibilidad del 99,99% = no esta disponible 53 minutos al ano.

**Standard S3 - Uso general**
- Disponibilidad del 99,99%
- Se utiliza para datos de acceso frecuente
- Baja latencia y alto rendimiento
- Soporta 2 fallos concurrentes de la instalacion
- Casos de uso: Analisis de big data, aplicaciones moviles y de juegos, distribucion de contenidos...

**Clases de almacenamiento S3 - Infrequent Access**
- Clase de almacenamiento en S3
- Coste inferior al de S3 estandar
- Amazon S3 Standard-Infrequent Access (S3 standard-IA)
	- Disponibilidad del 99,9%
	- Casos de uso: Recuperacion de desastres, copias de seguridad.
- Amazon S3 One Zone-Infrequent Access (S3 One Zone IA)
	- Alta durabilidad en una sola AZ; los datos se pierden cuando se destruye la AZ
	- Disponibilidad del 99,5%
	- Casos de uso: Almacenamiento de copias de seguridad secundarias de datos locales o de datos que puedes recrear.


**Clases de almacenamiento S3 - Amazon S3 Glacier**
- Almacenamiento de objetos de bajo coste pensado para archivar / hacer copias de seguridad.
- Precio: precio de almacenamiento + coste de recuperacion del objeto.
- Amazon S3 Glacier Instant Retrieval
	- Recuperacion en milisegundos, ideal para los datos a los que se accede una vez al trimestre
	- Duracion minima de almacenamiento de 90 dias
- Amazon S3 Glacier Flexible Retrieval
	- Acelerada (1 a 5 minutos), Estandar (de 3 a 5 horas), Masiva (de 5 a 12 horas) - gratis
	- Duracion minima de almacenamiento de 90 dias.
- Amazon S3 Glacier Deep Archive - para almacenamiento a largo plazo:
	- Estandar (12 horas), Masiva (48 horas)
	- Duracion minima de almacenamiento de 180 dias

**S3 Intelligent-tiering**
- Pequena cuota mensual de monitorizacion y jerarquizacion automatica.
- Mueve los objetos automaticamente entre los niveles de acceso en funcion del uso.
- No hay cargos por recuperacion en S3 intelligent-tiering.
- Frequent access tier: nivel por defecto
- infrequent access tier: objetos no accedidos durante 30 dias
- archive instant accesss: objetos no accedidos de 90 a mas de 700 dias
- archive access tier: configurable de 90 a mas de 700 dias
- deep archive access tier: configurable objetos no accedidos de 180 a mas de 700 dias

### Quiz

Question 1:
Tienes un archivo de 25 GB que estás intentando subir a S3 pero te da errores. ¿Cuál es una posible solución para esto?
- Utiliza la subida de varias partes cuando subas archivos mas grandes que 5GB, se sugiere a partir de los 100 MB.

Question 2:
Obtienes errores al intentar crear un nuevo bucket de S3 llamado "**dev**". Estás utilizando una nueva cuenta de AWS sin haber creado antes ningún bucket de S3. ¿Cuál es la posible causa de esto?
- Los nombres de los buckets se definen globalmente por lo que puede ya estar ocupado.

Question 3:
Has activado el control de versiones en tu bucket de S3 que ya contiene muchos archivos. ¿Qué versión tendrán los archivos existentes?
- Null, solo los nuevos archivos tomaran el versionado.

Question 4:
Has actualizado una política de bucket S3 para permitir a los usuarios de IAM leer/escribir archivos en el bucket S3, pero uno de los usuarios se queja de que no puede realizar una llamada a la API `PutObject`. ¿Cuál es la posible causa de esto?
- El usuario IAM debe tener un DENY explicito en la politica de IAM adjunta.

Question 5:
Quieres que el contenido de un bucket de S3 esté totalmente disponible en diferentes regiones de AWS. Eso ayudará a tu equipo a realizar análisis de datos con la menor latencia y coste posibles. ¿Qué función de S3 debes utilizar?
- Replicacion en S3, esto permite replicar en otra/misma region de AWS.

Question 6:
Tienes 3 buckets de S3. Un bucket de origen A, y dos buckets de destino B y C en diferentes regiones de AWS. Quieres replicar objetos del bucket A a los dos buckets B y C. ¿Cómo lo conseguirías?
- Configuraria una replicacion del bucket a al b y otra del bucket a al c

Question 7:
¿Cuál de los siguientes **NO** es un modo de recuperación de Glacier Deep Archive?
- La opcion puede ser estandar o masiva/a granel ya que acelerado no existe en este tipo de storage.

Question 8:
¿Cuál de los siguientes **NO** es un modo de recuperación flexible de Glacier?
- Instantanea no existe, solamente se nos ofrece acelerada, estandar y a granel.