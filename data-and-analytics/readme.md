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