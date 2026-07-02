## CloudFormation
CloudFormation es una forma declarativa de esbozar tu infraestructura de AWS para cualquier recurso mediante IaC (Infraestructure as code) mediante plantillas XML.

**Ventajas**
- Infraestructura como codigo
	- No se crean recursos manualmente, lo que es excelente para control
	- Los cambios en la infraestructura se revisan a traves del codigo
- Coste
	- Cada recurso dentro de la pila esta etiquetado con un identificador para que pueda revisarse cuanto cuesta la misma
	- Pueden estimarse costes de los recursos en base a plantillas de cloudformation
	- Existen estrategias de ahorro
- Productividad
	- Posibilidad de destruir y volver a crear infraestructura en el cloud sobre la marcha
	- Generacion automatizada de diagramas para las plantillas
	- Programacion declarativa (no es necesario averiguar el orden y la orquestacion)
- No es necesario crear de 0 plantillas
	- Existen muchas plantillas en la web y bien documentadas

**Stack designer**
CloudFormation nos permite diseñar nuestra pila y ver el diagrama y las relaciones entre los componentes, por ejemplo:
![[Pasted image 20260702202248.png]]