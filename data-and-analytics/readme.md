## Athena
- Servicio de consulta serverless para analizar datos almacenados en S3
- Utiliza el lenguaje SQL estandar para consultar los archivos
- Admite CSV, JSON, ORC, Avro y Parquet
- Precio: 5 dolares por TB analizado
- Se utiliza habitualmente con Quicksight para la elaboracion de informes y dashboards
- Caso de uso: inteligencia empresarial / analisis / informes, analizar y consultar registros de flujo de VPC, registros de ELB, Cloudtrail trails, etc...

**Mejora de rendimiento**
- Utiliza datos en columnas para ahorrar costes
	- Se recomienda Apache Parquet o ORC
	- Utiliza GLUE para convertir los datos en estos formatos
- Comprime los datos para recuperaciones mas chicas (bzip2, gzip, etc...)
- Particionar conjuntos de datos en S3 para facilitar la consulta en columnas verticales
	- ej: s3://athena-examples//flight/parquet/year=1991/month=1/day=1
- Utilizar archivos de mayor size (> 128 MB) para minimizar sobrecarga 

**Consulta federada**
- Permite ejecutar consultas SQL en fuentes de datos relacionales, no relacionales, de objetos y personalizadas (AWS o en las instalaciones)
- Utiliza conectores de fuentes de datos que se ejecutan en AWS Lambda para ejecutar consultas federadas (por ejemplo, CloudWatch logs, DynamoDB, RDS, ...)
- Almacena los resultados de nuevo en Amazon S3'

Una consulta federada hace referencia a que AWS Athena hace una sola consulta y obtiene los datos de multiples recursos como si fueran una sola base.

## RedShift
- RedShift se basa en PostgreSQL, pero no utiliza OLTP, este utiliza OLAP (enfocado en analiticas) - procesamiento analitico en linea
- 10 veces mejor rendimiento que otros almacenes de datos, escala a PBs de datos
- Almacenamiento de datos en columnas (en lugar de filas) y motor de consulta paralelo
- Pago por uso en funcion de las instancias aprovisionadas
- Dispone de una interfaz SQL para realizar consultas
- Se integra con herramientas de BI como Amazon Quicksight o Tableau
- vs Athena: consultas / uniones / agregaciones mas rapidas gracias a los indices

**Cluster Redshift**
- Nodo lider: es un orquestador que planifica las consultas y agrega los resultados.
- Nodo de calculo: realiza las consultas y envia los resultados al lider.
- Se aprovisiona el size del nodo por adelantado
- ![[Pasted image 20260416193827.png]]
- Redshift no posee caracteristica multi AZ
- las instantaneas son copias de seguridad puntuales de un cluster, almacenadas internamente en S3
- Las instantaneas son incrementales (solo se guarda lo que ha cambiado)
- Se puede restaurar una instantanea en un nuevo cluster (caracteristica poderosa)
- Automatizado: cada 8 horas, cada 5gb, cada cierto horario. retencion de 1 a 35 dias
- manual: la snapshot se conserva hasta que se elimina

**carga de datos en Redshift**
- Amazon Kinesis Data Firehose
- S3 (con enrutamiento mejorado por VPC o sin el)
- Instancia EC2

**Redshift spectrum**
- Consultar datos que ya estan en S3 sin cargarlos
- Debe haber un cluster Redshift disponible para la consulta
- A continuacion, la consulta se envia a miles de nodos Redshift spectrum
- ![[Pasted image 20260416194715.png]]

## Amazon OpenSearch
- En algunas bases de datos como DynamoDB las consultas solo existen por clave primaria o indices...
- OpenSearch permite buscar en cualquier campo incluso con coincidencias parciales.
- Es un complemento para bases de datos
- Requiere de un cluster de instancias (no serverless)
- No soporta SQL (tiene su propio lenguaje de consulta)
- Ingestion de Kinesis data firehose, AWS IoT, y CloudWatch Logs
- Seguridad mediante Cognito & IA, cifrado KMS, TLS
- Viene con un panel de control (opensearch dashboards)

**Patrones de opensearch con DynamoDB**
![[Pasted image 20260416195350.png]]

**Patrones de OpenSearch con CloudWatch Logs**
![[Pasted image 20260416195459.png]]

**Patrones de OpenSearch con Kinesis Data Streams y Kinesis Data Firehose**
![[Pasted image 20260416195618.png]]
