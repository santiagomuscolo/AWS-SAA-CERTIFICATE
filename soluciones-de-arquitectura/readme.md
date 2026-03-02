Los 5 pilares de una aplicacion bien arquitecturada son:
- Costes
- Rendimiento
- Fiabilidad
- Seguridad
- Excelencia operativa

### WhatIsTheTime.com

**Arquitectura basica inicial**
![[Pasted image 20260302202116.png]]

Derrepente recibimos mas usuarios y el computo de nuestra instancia no alcanza, ahi mismo podemos escalar verticalmente:
![[Pasted image 20260302202302.png]]

Sin embargo, esto tiene un inconveniente y es la inactividad, ya que al escalar verticalmente de forma momentanea nuestra instancia dejara de estar disponible.

Aca es cuando podemos optar por escalar horizontalmente, esto nos permitira tener diferentes instancias para la distribucion del trafico pero esto no escala por que estamos haciendo un mal uso de las IPs elasticas, por lo que debemos encontrar una manera de distribuir el trafico a nuestra instancia

![[Pasted image 20260302202659.png]]

Para eso tenemos las consultas DNS con Route 53, para poder hacer una correcta gestion de las IPs de nuestras 3 instancias a travez de nuestro dominio:
![[Pasted image 20260302203247.png]]

Vease algo importante, si alguna de las instancias se cae nuestro DNS resolver no cambiara la IP a la que esta apuntando hasta que el TTL pase, por lo que debemos hacer uso de un ELB.

![[Pasted image 20260302203851.png]]

Vease que esta solucion resuelve el problema de las instancias caidas, de la redireccion del trafico, de las IPs elasticas, pero aun falta algo... el control sobre el disaster recovery y el manejo del autoescaling cuando es necesario, por lo que la siguiente solucion sera la final:

![[Pasted image 20260302204557.png]]

Lo que nos tenemos que llevar de esta arqutectura stateless es lo siguiente:
1- priorizar el escalado a medida que este se necesita, ninguna arquitectura hegemonica desde el inicio es necesaria.
2- priorizar costes, como ven no replique 3 instancias en las dos AZ, decidi hacer multi AZ y .distribuir 2 instancias podrian haber sido las 3 y distribuirlas en 3 az (mismo con el load balancer).
3- Registro alias, hacemos uso de una IP publica para que luego usemos la red privada.
4- La escalabilidad, esto nos permite escalar horizontal y verticalmente.
5- El disaster recovery, al ser multi AZ nos aseguramos la actividad frente a la baja de una instancia.