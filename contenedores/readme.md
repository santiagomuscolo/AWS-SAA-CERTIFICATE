## Introduccion a Docker

**Que es docker?**
- Docker es una plataforma de desarrollo de software para desplegar apliaciones orientada a contenedores.
- Las aplicaciones se empaquetan en contenedores que pueden ejecutarse en cualquier sistema operativo.
- Las aplicaciones se ejecutan igual, independientemente de donde se ejecuten.
	- Cualquier maquina
	- Sin problemas de compatibilidad
	- Comportamiento predecible
	- Menos trabajo
	- Mas facil de mantener e implementar.
	- Funciona con cualquier lenguaje, OS y tecnologia
- Casos de uso: aquitectura de microservicios, aplicaciones lift-and-shift de on-premises a la nube

**Donde se almacenan las imagenes de docker?**
Para empezar una imagen de docker es un fichero que permite la creacion del contenedor.

- Las imagenes de docker se almacenan en repositorios Docker
	- Docker Hub: repositorio publico en el que se pueden subir imagenes propias y/o utilizar imagenes existentes de diversas tecnologias y OS.
	- AWS ECR: repositorio privado, aunque posee una caracteristica publica llamada "galeria publica de AWS ECR"

**Docker vs maquinas virtuales**
- Docker es "algo asi" como una tecnologia de virtualizacion, pero no exactamente.
- Los recursos se comparten con el host => muchos contenedores en un servidor.
- ![[Pasted image 20260402163852.png]]

**Gestion de contenedores Docker en AWS**
- Amazon Elastic Container Service (ECS)
	- Plataforma de contenedores propia de amazon
- Servicio Amazon Elastic Kubernetes (AWS EKS)
	- Kubernetes administrado por Amazon (codigo abierto)
- AWS Fargate
	- Plataforma de contenedores sin servidor propia de Amazon
	- Funciona con ECS y con EKS
- Amazon ECR
	- Almacena imagenes de contenedores

## Amazon ECS
- ECS = Elastic Container Service
- Lanzar contenedores Docker en AWS = Lanzar tareas ECS en clusteres ECS
- Tipo de lanzamiento EC2: debe aprovisionar y mantener la infraestructura (las instancias EC2)
- Cada instancia EC2 debe ejecutar el agente ECS para registrarse en el Cluster ECS
- AWS se encarga de iniciar / detener los contenedores.

**Amazon ECS - Tipo de lanzamiento Fargate**
Entiendase Fargate como un servicio donde lanzaremos contenedores, donde:
- No es necesario aprovisionar la infraestructura (no hay instancias EC2 que administrar)
- Todo es serverless (esto quiere decir que el provedor cloud manejara por nosotros la infraestructura, su mantenimiento y escala)
- Solo tienes que crear definiciones de tareas.
- AWS ejecuta las tareas ECS por vos en funcion de la CPU / RAM que necesites.
- Para escalar, basta con aumentar el numero de tareas. Simple - no mas instancias EC2.

**Roles IAM para ECS**
- Perfil de instancia EC2 (solo tipo de lanzamiento EC2):
	- Utilizado por el agente ECS
	- Realiza llamadas API al servicio ECS
	- Envia logs de contenedores a CloudWatch Logs
	- Extrae imagen de Docker de ECR
	- Hace referencia a datos sensibles en Secrets Manager o SSM Parameter Store.
- Rol de tarea ECS:
	- Permite que cada tarea tenga un rol especifico
	- Utiliza diferentes roles para los diferentes servicios ECS que ejecute
	- El rol de tarea se define en la definicion de la tarea
- ![[Pasted image 20260402171957.png]]

**Integraciones con balanceadores de carga (load balancers)**
- Application load balancer: es compatible y funciona para la mayoria de casos de uso.
- Network load balancer: recomendado solo para casos de uso de alto rendimiento o para combinarlo con AWS Private Link
- Elastic Load Balancer: es compatible pero no se recomienda (sin caracteristicas avanzadas - sin fargate)

**Volumenes de datos (EFS)**
- Montar sistema de archivos EFS en tareas ECS
- Funciona tanto para los tipos de lanzamiento EC2 como Fargate
- Las tareas que se ejecuten en cualquier AZ compartiran los mismos datos en el sistema de archivos EFS
- Fargate + EFS = serverless
- Casos de uso: almacenamiento compartido multi-AZ persistente para sus contenedores.
- Nota: Amazon S3 no se puede montar como sistema de archivos

**datos que no sabia**
Cloudformation es un sevicio IaC (infraestructura como codigo) que permite definir, modelar y aprovisionar recursos de AWS usando templates JSON y YAML.

## Amazon ECS - Auto Scaling
- Aumentar/disminuir automaticamente el numero deseado de tareas ECS.
- Amazon ECS Auto Scaling utiliza **AWS Application Auto Scaling**
	- Utilizacion media de la CPU del servicio ECS.
	- Utilizacion media de memoria del servicio ECS - Escalado RAM.
	- Recuento de solicitudes ALB por objetivo - metrica procedente del ALB
- **Seguimiento de objetivo** - escala basada en el valor objetivo para una metrica especifica de CloudWatch.
- **Escalado por pasos** - escalado basado en una alarma CloudWatch especifica.
- **Escalado programado** - escalado basado en una fecha/hora especificada (cambios predecibles)
- Autoescalado del servicio ECS (nivel de tarea) != Autoescalado de EC2 (nivel de instanica de EC2)
- Fargate Auto Scaling es mucho mas facil de configurar ya que es serverless

**Tipo de lanzamiento EC2 - Escalado automatico de instancias EC2**
- Acomodar el escalado de servicios ECS agregando instancias EC2 subyacentes.
- **Escalado automatico de grupos (Auto scaling group scaling)**
	- Escala el ASG en funcion de la utilizacion de la CPU
	- Con el tiempo se agregaran mas instancias EC2
- Proveedor de capacidad de cluster ECS
	- Se utiliza para aprovisionar y escalar automaticamente la infraestructura para tareas ECS
	- Proveedor de capacidad emparejado con un ASG.
	- Agrega instancias EC2 cuando falte capacidad (CPU, RAM)
- ![[Pasted image 20260403183557.png]]

## ECS - Soluciones de arquitectura
![[Pasted image 20260403185409.png]]
![[Pasted image 20260403185454.png]]![[Pasted image 20260403185547.png]]

## Amazon ECR
- ECR = Registro elastico de contenedores
- Sirve para almacenar y administrar imagenes de Docker en AWS
- Repositorio privado y publico (Amazon ECR Public Gallery)
- Totalmente integrado con ECS, respaldado por Amazon S3
- El acceso se controla a traves de IAM (errores de permiso => politica)
- Soporta escaneo de vulnerabilidades de imagenes, versionado, etiquetas de imagenesm ciclo de vida de las imagenes...
- ![[Pasted image 20260403190035.png]]

## Amazon EKS
- Amazon EKS = Servicio Amazon Elastic Kubernetes
- Es una forma de lanzar clusteres de kubernetes administrados en AWS
- Kubernetes es un sistema de codigo abierto que sirve para escalar, gestionar y desplegar automaticamente aplicaciones en contenedores (normalmente docker)
- Es una alternativa a ECS, objetivo similar pero API diferente.
- EKS soporta EC2 si se quieren desplegar nodos trabajadores o Fargate para despelgar contenedores sin servidor
- Casos de uso: migracion de on=premise que utilicen kubernetes a AWS
- Kubernetes es agnostico a la nube.
- ![[Pasted image 20260403190652.png]]
- Conceptos nuevos de esta imagen:
	- PODS: un POD es la unidad minima desplegable dentro de un cluster, basicamente es donde viven uno o mas containers que necesitan trabajar juntos, incluyendo: 1 o mas contenedores, una ip propia dentro del cluster, almacenamiento compartido, configuracion de red compartida.
	- En la imagen cada container es un pod por lo que dentro de 1 nodo EKS contariamos con 3 pods
	- NGW (AWS NAT Gateway): permite que recursos en subredes privadas salgan a internet pero no puedan ser accedidos desde internet.
	- 1 Cluster EKS  
		├── Nodo 1 (AZ 1)  
			│ ├── Pod  
			│ ├── Pod  
			│ └── Pod  
		├── Nodo 2 (AZ 2)  
			│ ├── Pod  
			│ ├── Pod  
			│ └── Pod  
		└── Nodo 3 (AZ 3)  
			├── Pod  
			├── Pod  
			└── Pod

**Tipos de nodos**
- Grupos de nodos gestionados
	- Crea y gestiona nodos (instancias ec2) para ti.
	- Los nodos forman parte de un ASG gestionado por EKS.
	- Admite instancias bajo demanda o puntuales.
- Nodos autogestionados
	- Nodos creados por ti y registrados en el cluster EKS y gestionados por un ASG
	- Puede utilizar una AMI preconstruida - Amazon EKS Optimized AMI
	- Admite instancias bajo demanda o puntuales.
- AWS Fargate
	- No requiere mantenimiento; no se administran nodos.

**Volumenes de datos**
- Necesidad de especificar el StorageClass en el cluster EKS.
- Aprovecha un controlador compatible con Container Storage Interface (CSI)
- Compatible con
	- EBS
	- EFS (funciona con Fargate)
	- FSx para Lustre
	- FSx para NetApp ONTAP