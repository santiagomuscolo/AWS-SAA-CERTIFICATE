## Amazon Rekognition
- Encuentra objetos, personas, texto, escenas en imagenes y videos utilizando ML
- Analisis facial y busqueda facial para hacer verificacion de usuarios, recuento de personas
- Crear una base de datos de "caras conocidas" o comparar con famosos
- Casos de uso:
	- Etiquetado
	- Moderacion de contenidos
	- Deteccion de texto
	- Deteccion y Analisis de Caras (sexo, rango de edad, emociones...)
	- Busqueda y verificacion de caras
	- Reconocimiento de famosos
	- Trazado de trayectorias (por ejemplo, para analisis de partidos deportivo)

**La caracteristica mas importante que posee es la moderacion de contenidos**
- Detectar contenido inapropiado, no deseado y ofensivo
- Se utiliza en redes sociales, medios de difusion, publicidad y situaciones de comercio electronico pra crear una UX mas segura
- Establece un Umbral minimo de confianza para los elementos que se marcaran
- Marcar contenido sensible para su revision manual en la IA aumentada de Amazon (A2I)
- ![[Pasted image 20260421121705.png]]

## Transcribe
- Convierte automaticamente el habla a texto
- Utiliza deep learning llamado reconocimiento automatico del habla para convertir el habla en texto de forma rapida y precisa
- posee soporte multilingue
- Elimina automaticamente la informacion de identificacion personal (PII)
- Casos de uso:
	- Transcribir llamadas
	- Automatizar el subtitulado 
	- Generar metadatos para los activos de los medios de comunicacion para crear un archivo con todas las posibilidades de busqueda

## Polly
- Convierte el texto a voz utilizando deep learning
- Permite crear aplicaciones que hablan

**Lexico y SSML**
- Personaliza la pronunciacion de las palabras con los lexicos de pronunciacion
	- Palabras estilizadas: jo7n => "joan"
	- Aconimos: AWS => Amazon Web Services
- Sube los lexicos en un fichero y la conversion se raliza automaticamente
- Genera voz a partir de texto plano o de documentos marcados con el lenguaje de marcado de sintesis de voz (SSML): permite una mayor personalizacion
	- Enfatiza palabras o frases concretas
	- Utilizando pronunciacion fonetica
	- Incluyendo sonidos respiratorios, susurros