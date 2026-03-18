### Reglas del ciclo de vida de S3 (con S3 analytics)

**Movimiento entre clases de almacenamiento**
- Puedes pasar los objetos entre las clases de almacenamiento
- Para los objetos a los que se accede con poca frecuencia, muevelos a IA estandar
- Para los objetos de archivo a los que no necesitas acceder rapidamente, muevelos a Glacier o Glacier Deep Archive
- El movimiento de los objetos puede automatizarse mediante las reglas del ciclo de vida

**Reglas de ciclo de vida**
- Acciones de transicion: configura los objetos para que pasen a otra clase de almacenamiento
	- Mover los objetos a la clase IA estandar 60 dias despues de su creacion
	- Mover a Glacier para archivar despues de 6 meses
- Acciones de expiracion: configura los objetos para que caduquen (se eliminen) despues de un tiempo
	- Los archivos de logs de acceso pueden configurarse para que se eliminen despues de 365 dias
	- Se puede utilizar para eliminar versiones antiguas de archivos
	- Se puede utilizar para eliminar subidas incompletas de Multipartes
- Se pueden crear reglas para un determinado prefijo (ejemplo: s3://mybucket/mp3/*)
- Se pueden crear reglas para determinados objetos Etiquetas (ejemplo: Departamento: Finanzas)

**S3 Analytics - analisis de clase de almacenamiento**
- Te ayuda a decidir cuando pasar los objetos a la clase de almacenamiento adecuada.
- Recomendaciones para IA estandar y estandar.
	- No sirve para IA one zone o Glacier
- El informe se actualiza diariamente
- de 24 a 48 horas para empezar a ver el analisis de los datos.
- Buen primer paso para elaborar las reglas del ciclo de vida.

### El solicitante paga
- En general, los propietarios de los buckets pagan todos los costes de almacenamiento y transferencia de datos de Amazon S3 asociados a su bucket.
- Con los buckets donde el solicitante paga, el solicitante en lugar del propietario del bucket, paga el coste de la peticion y la descarga de datos del bucket.
- Es util cuandoq uieres compartir grandes conjuntos de datos con otras cuentas.
- El solicitante debe estar autenticado en AWS (no puede ser anonimo)

### Notifiaciones de eventos
- s3:ObjectCrated, S3:ObjectRemoved, ...
- Posibilidad de filtrar por nombre de objeto (*.jpg)
- Caso de uso: generar miniaturas de imagenes subidas a S3
- Se pueden crear tantos "eventos S3" como se desee
- Las notificaciones suelen ser entregadas en segundos, pero a veces pueden tardar un minuto o mas

**EventBridge**
eventos -> S3 -> EventBridge -> +18 servicios de AWS

- Opciones avanzadas de filtrado con reglas JSON (metadatos, size del objeto, nombre...)
- Multiples destinos - step functions, kinesis streams / firehose
- Capacidad de EventBridge - repeticion de eventos, entrega fiable

### Rendimiento S3

**Rendimiento basico**
- Amazon S3 escala automaticamente a altas tasas de peticion, latencia 100 - 200 ms
- Tu aplicacion puede alcanzar al menos 3500 peticiones PUT/COPY/POST/DELETE y 5.500 GET/HEAD por segundo por prefijo en un bucket
- No hay limites en el numero de prefijos de un bucket
- Ejemplo (ruta de objeto => prefijo):
	- bucket/carpeta/1/sub1/fichero =>/carpeta/sub1/

**performance**
- Carga de varias partes (multipartes)
	- Recomendado para archivos > 100mb y obligatorio > 5gb
	- Puede ayudar a paralelizar las subidas (acelera las transferencias)
- Aceleracion de la transferencia en S3:
	- Aumenta la velocidad de transferencia transfiriendo el archivo a un EDGE location de AWS que reenviara los datos al bucket de S3 en la region de destino.
	- Compatible con multipart
- Paralelizar los GETs solicitando rangos de bytes especificos, lo que produce una mejor resiliencia en caso de fallos, tambien puede utilizarse para recuperar datos parciales como la cabecera (primeros xx bytes)

### S3 select y GLACIER select
- Recupera menos datos mediante SQL realizando un filtrado del lado del servidor
- Puede filtrar por filas y columnas (simples sentencias SQL)
- Menos transferencia de red, menos coste de CPU en el lado del cliente

### Operaciones por lotes de S3 (batch operations)
- Realiza operaciones masivas en objetos S3 existentes con una sola peticion, por ejemplo:
	- Modificar los metadatos y las propiedades de los objetos
	- Copiar objetos entre buckets S3
	- cifrar objetos no cifrados
	- Modificar ACLs, etiquetas
	- Restaurar objetos de S3 glacier
	- invocar una funcion Lambda para realizar una accion personalizada en cada objeto
- Un trabajo consiste en una lista de objetos, la accion a realizar y parametros opcionales
- S3 Batch operations gestiona los reintentos, sigue el progreso, envia notificaciones de finalizacion, genera informes...
- Puedes utilizar el inventario de S3 para obtener la lista de objetos y utilizar S3 select para filtrarlos.

### Quiz

Question 1:
¿Cómo puedes recibir una notificación cuando se sube un objeto a tu bucket de S3?
- Notificaciones de eventos de S3

Question 2:
Tienes un bucket de S3 que tiene activado el control de versiones de S3. Este bucket de S3 tiene muchos objetos, y te gustaría eliminar las versiones antiguas de los objetos para reducir costes. ¿Cuál es el mejor enfoque para automatizar la eliminación de estas versiones antiguas de los objetos?
- Con una regla de ciclo de vida de S3 - acciones de caducidad

Question 3:
¿Cómo puedes automatizar la transición de los objetos de S3 entre sus diferentes niveles?
- Con una regla de ciclo de vida de S3


Question 4:
Mientras subes archivos grandes a un bucket de S3 utilizando la Carga Multiparte, hay muchas partes inacabadas almacenadas en el bucket de S3 debido a problemas de red. No estás utilizando estas partes inacabadas y te cuestan dinero. ¿Cuál es el mejor enfoque para eliminar estas partes inacabadas?
- Utilizar una politica de ciclo de vida de S3 para automatizar la eliminacion de piezas antiguas/no terminadas.

Question 5:
Quieres obtener recomendaciones para las reglas del ciclo de vida de S3. ¿Cómo puedes analizar el número óptimo de días para mover objetos entre los distintos niveles de almacenamiento?
- Con S3 analytics

Question 6:
Estás buscando construir un índice de tus archivos en S3, utilizando Amazon RDS PostgreSQL. Para construir este índice, es necesario leer los primeros 250 bytes de cada objeto en S3, que contienen algunos metadatos sobre el contenido del propio archivo. Hay más de 100.000 archivos en tu bucket de S3, lo que supone 50 TB de datos. ¿Cómo puedes construir este índice de forma eficiente?
- Crear una aplicacion que recorra el bucket y realice un byte range fetch para los primeros 250 bytes y almacene esa informacion en RDS


Question 7:
Tienes un gran conjunto de datos almacenado en las instalaciones que quieres subir al bucket de S3. El conjunto de datos está dividido en archivos de 10 GB. Tienes un buen ancho de banda, pero tu conexión a Internet no es estable. ¿Cuál es la mejor manera de subir este conjunto de datos a S3 y garantizar que el proceso sea rápido y evitar cualquier problema con la conexión a Internet?
- Hacer uso de subida multi part y S3 transfer acceleration para enviar este multipart a una edge location.

Question 8:
Te gustaría recuperar un subconjunto de tu conjunto de datos almacenado en S3 con el formato .csv. Te gustaría recuperar un mes de datos y sólo 3 columnas de 10, para minimizar los costes de cálculo y de red. ¿Qué deberías utilizar?
- Se puede hacer uso de S3 Select y filtrar mediante sentencias SQL

Question 9:
Una empresa se está preparando para una revisión normativa en su infraestructura en AWS. Actualmente, tienen sus archivos almacenados en buckets de S3 que no están encriptados, y que deben ser encriptados como se requiere para la revisión normativa y de cumplimiento. ¿Qué función de S3 les permite cifrar todos los archivos de sus buckets de S3 de la forma más eficiente y rentable?
- Con batch operations post filtrado mediante S3 select al S3 inventory