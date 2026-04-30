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