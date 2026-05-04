## AWS Organizations
Organizations es un servicio global de aws que permite crear organizaciones con usuarios dentro, dentro de los roles de los usuarios podemos percibir:
- La cuenta principal (administrador)
- Las demas cuentas (miembros, disclaimer solo pueden formar parte de una organizacion)

Este servicio favorece el uso de recursos compartidos, facturacion consolidada con unico medio de pago, etc...

![[Pasted image 20260429200800.png]]

**Ventajas**
- Multi cuenta vs unica cuenta con multi VPC
- Utiliza normas de etiquetado con fines de facturacion
- Activar cloudtrail por cuenta y guardar en un bucket s3 central
- enviar logs de cloudwatch a la cuenta central de logs
- Establecer roles entre cuentas con fines administrativos

**Seguridad: Politicas de control de servicios (SCP)**
- Politicas IAM aplicadas a OU o cuentas para restringir usuarios y roles
- No se aplican a la cuenta de gestion (Plenos poderes de administracion)
- Deben tener un permiso explicito (no permiten nada por defecto - como IAM)

![[Pasted image 20260429201447.png]]

## Politicas avanzadas

**Condiciones IAM**
Validaciones realizables dentro de la politica, como por ejemplo:
![[Pasted image 20260429202522.png]]![[Pasted image 20260429202814.png]]


**aws:PrincipalOrgId**
Puede utilizarse en cualquier politica de recursos para restringir el acceso a cuentas que sean miembros de una Organizacion de AWS
![[Pasted image 20260430120800.png]]

## Politicas basadas en recursos frente a roles IAM

**Roles de IAM vs politicas basadas en recursos**
- Cuenta cruzada:
	- adjuntando una politica basada en recursos a un recurso (ejemplo: politica de bucket S3)
	- O utilizando un rol como proxy
	- ![[Pasted image 20260430121021.png]]
- Cuando asumes un rol (usuario, aplicacion o servicio), renuncias a tus permisos originales y tomas los permisos asignados al rol
- Cuando se utiliza una politica basada en recursos, el principal no tiene que renunciar a sus permisos
- Ejemplo: El usuario de la cuenta A necesita escanear una tabla DynamoDB de la cuenta A y volcarla en un bucket S3 de la cuenta B.
- Soportado por Amazon S3 buckets, SNS Topic, SQS queues, etc...

**E.G EventBridge**
- Cuando se ejecuta una regla eventbridge necesita los permisos del target.
- Politica basada en recursos: Lambda, SQS, SNS, CloudWatch Logs, API Gateway...
- Politica basada en rol IAM: Kinesis, Systems run command, ECS task...

## IAM - Logica de evaluacion de politicas

**Limites de permisos IAM**
- Los limites de permisos IAM se soportan para usuarios y roles (no para grupos)
- Funcion avanzada para utilizar una politica gestionada para establecer los permisos maximos que puede obtener una entidad IAM
- ![[Pasted image 20260430122221.png]]
- Puede utilizarse en combinaciones de AWS organizations SCP
- ![[Pasted image 20260430122838.png]]

**Casos practicos**
- Delegar responsabilidades a no administradores dentro de sus limites de permisos, por ejemplo crear nuevos usuarios IAM
- Permitir que los desarrolladores se autoasignen politicas y gestionen sus propios permisos sin permitir que se vuelvan admins
- Util para restringir a un usuario concreto (en lugar de a toda unac uenta mediante organizaciones y SCP)

## Cognito
Servicio de autenticacion y autorizacion para apps web y mobiles

**Grupos de usuarios cognito**
- Funcionalidad de inicio de sesion para usuarios de aplicaciones
- Integracion con API Gateway y Application Load Balancer

**Cognito identity pools (identidad federada)**
- Proporciona credenciales AWS a los usuarios para que puedan acceder directamente a los recursos de AWS
- Integrar con Cognito User Pools como proveedor de identidades

**User pools**
Crea una base de datos para usuarios serverless ofreciendo un inicio de sesion simple con posibilidad de recuperacion de contrasena, multi factor e integraciones de identidades federadas

Integraciones
- Api gateway (serverless)
- Application load balancer
- ![[Pasted image 20260504202046.png]]

**Cognito identity pools**
Permite obtener identidades para "usuarios" para que obtengan credenciales temporales en AWS, el origen de los mismos puede variar (Cognito User Pools, inicios de sesion de terceros, etc...).
Trae Roles IAM por defecto y permite que los usuarios puedan acceder a servicios de AWS directamente o a traves de API gateway
![[Pasted image 20260504202436.png]]

## Centro de identidades de AWS IAM
Este servicio es el sucesor de AWS single sign on y busca centralizar el login para todas tus cuentas de forma unica
- AWS Organizations
- Aplicaciones empresariales en el cloud (Salesforce, microsoft)
- Aplicaciones habilitadas para SAML 2.0
- Instancias de windows EC2
![[Pasted image 20260504203141.png]]

**Permisos y asignaciones en detalle**
- Permisos multicuenta
	- Gestiona el acceso a traves de las cuentas de AWS en tu organizacion AWS
	- Conjuntos de permisos - una coleccion de una o mas politicas IAM asignadas a usuarios y grupos apra definir el acceso a AWS
- Asginaciones de aplicaciones
	- Acceso SSO a muchas aplicaciones empresariales
	- Proporciona las URL, certificados y metadatos necesarios
- Control de acceso basado en atributos (ABAC)
	- Permisos detallados basados en los atributos de los usuarios almacenados en el almacen de identidades del centro de identidades IAM
	- Ejemplo: centro de costes, cargo, config regional
	- Caso practico: define los permisos una vez y luego modificar el acceso AWS cambiando los atributos
	- ![[Pasted image 20260504203920.png]]