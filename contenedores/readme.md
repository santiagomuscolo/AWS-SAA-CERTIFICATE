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