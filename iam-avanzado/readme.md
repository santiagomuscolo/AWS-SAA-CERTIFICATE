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