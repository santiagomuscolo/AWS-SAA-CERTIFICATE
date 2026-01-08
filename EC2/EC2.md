**Amazon EC2**

- EC2 es una de las ofertas mas populares de AWS.
- EC2 = Elastic Compute Cloud = Infraestructura como servicio (IaaS)
- Consiste principalmente en la capacidad de:
    - Alquilar maquinas virtuales (EC2)
    - Almacenar datos en unidades virtuales (EBS)
    - Distribuir la carga entre maquinas (ELB)
    - Escalar los servicios mediante Auto Scaling Group (ASG)

**Opciones de size y configuration de EC2**

- Sistema operativo (OS): Linux, Windows o Mac OS.
- Cuanta potencia de calculo y nucleos (CPU)
- Cuanta memoria de acceso aleatorio (RAM)
- Cuanto espacio de almacenamiento:
    - Conectado a la red (EBS y EFS)
    - Hardware (EC2 instance Store)
- Tarjeta de red: velocidad de la tarjeta, direccion IP publica.
- Reglas de firewall: grupo de seguridad.
- Script de arranque (configurar el primer lanzamiento): Datos de usuario de EC2

**Datos del usuario de EC2**

- Es posible arrancar nuestras instancias utilizando un script de datos de usuario de EC2.
- bootstrapping significa lanzar comandos cuando una maquina se inicia.
- Ese script solo se ejecuta una vez en el primer arranque de la instancia.
- Los datos de usuario EC2 se utilizan para automatizar tareas de arranque como:
    - Instalar actualizaciones.
    - Instalacion de software.
    - Descarga de archivos de internet.
    - etc…

**Tipos basicos de instancias EC2**
- Puedes utilizar diferentes tipos de instancias de EC2 optimizadas para diferentes casos de uso.
- Aws tiene la siguiente convencion de nombres -> m5.2xlarge donde:
	- m: Es la clase de instancia
	- 5: Generacion (AWS los mejora con el tiempo)
	- 2xlarge: Tamaño dentro de la clase de la instancia

**Tipos de instancias de EC2 - Proposito general**
- Ecelente para una diversidad de cargas de trabajo, como servidores web o repositorios de codigo.
- Equilibrio entre:
  - Computacion
  - Memoria
  - Red
	

**Tipos de instancias EC3 - Computacion optimizada**
- Ideal para tareas de calculo intensivo que requieren procesadores de alto rendimiento:
  - Cargas de trabajo de procesamiento por lotes
  - Transcodificacion de medios
  - Servidores web de alto rendimiento
  - Computacion de alto rendimiento (HPC)
  - Modelado cientifico y aprendizaje automatico
  - Servidores dedicados a juegos
	

**Tipos de instancias EC2 - Memoria optimizada**
- Rapido rendimiento en cargas de trabajo que procesan grandes conjuntos de datos en memoria
	- Alto rendimiento, bases de datos relacionales/no relacionales.
	- Almacenes de cache distribuidos a escala web
	- Bases de datos en memoria optimizadas para BI (Business Intelligence)
	- Aplicaciones que realizan el procesamiento en tiempo real de grandes datos no estructurados.

**Tipos de instancias EC2 - Almacenamiento optimizado**
- Ideal para tareas de almacenamiento intensivo que requieran un acceso alto y secuencial de lectura y escritura a grandes conjuntos de datos en el almacenamiento local.
	- Sistemas de procesamiento de transacciones en linea (OTLP) de alta frecuencia.
	- Bases de datos relacionales y NoSQL.
	- cache para bases de datos en memoria (por ejemplo, Redis)
	- Aplicaciones de almacenamiento de datos
	- Sistema de archivos distribuidos


Ejemplo

| Instancia   | vCPU | Mem (GiB) | Almacenamiento   | Rendimiento de la red | Ancho de banda de EBS (Mbps) |
| ----------- | ---- | --------- | ---------------- | --------------------- | ---------------------------- |
| t2.micro    | 1    | 1         | solo EBS         | Bajo a moderado       |                              |
| t2.xlarge   | 4    | 16        | solo EBS         | Moderado              |                              |
| c5d.4xlarge | 16   | 32        | 1 x 400 NVMe SSD | Hasta 10 Gbps         | 4.750                        |
| r5.16xlarge | 64   | 512       | solo EBS         | 20 Gbps               | 13.600                       |
| m5.8xlarge  | 32   | 128       | Solo EBS         | 10 Gbps               | 6.800                        |
