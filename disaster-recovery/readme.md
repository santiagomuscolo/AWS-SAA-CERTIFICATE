## Vision general de disaster recovery

>[!info] Disaster se le dice a cualquier acontecimiento que genere un impacto negativo en la continuidad de la actividad o en las finanzas de una empresa

Aqui nace el termino **Disaster Recovery** adoptado para la preparacion y recuperacion de dicha catastrofe.

**Que tipo de recuperacion ante desastres existen?**
- En las instalaciones => En las instalaciones: DR tradicional y muy caro
- En las instalaciones => Cloud de AWS: recuperacion hibrida
- Region A de AWS Cloud => Region B de AWS Cloud

Durante el transcurso de esta explicacion se dara uso a dos terminos:
- RPO: Recovery Point Objective (Objetivo de punto de recuperacion)
- RTO: Recovery Time Objective (Objetivo de tiempo de recuperacion)

**RTO y RPO**
![[Pasted image 20260617142537.png]]
- RTO: Es el tiempo maximo que podes estar caido despues de un incidente, por ejemplo: tenes que recuperar el sistema en menos de 1 hora si o si.
	“¿Cuánto tiempo puedo tener el sistema offline sin que sea un desastre?”

- RPO: Es la cantidad maxima de datos que podes perder, por ejemplo: poder perder como mucho los ultimos 10 minutos de datos.
	“¿Hasta qué punto en el pasado puedo volver sin que sea crítico?”