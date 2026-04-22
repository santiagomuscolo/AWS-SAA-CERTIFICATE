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

	## Translate
- Traduccion natural y precisa de idiomas
- Amazon translate te permite localizar contenidos - como sitios web y aplicaciones - para usuarios internacionales, y traducir facilmente grandes volumenes de texto de forma eficiente

## Lex + Connect
- Amazon lex (la tecnologia que impulsa a alexa)
	- Reconocimiento automatico del habla (ASR) para convertir el habla en texto
	- Compresion del lenguaje natural para reconocer la intencion del texto, de las personas que llaman
	- Ayuda a crear chatbots, bots de centros de llamadas
- Amazon connect
	- Recibe llamadas, crea flujos de contacto, centro de contacto virtual basado en la nube
	- Puede integrarse con otros sistemas CRM o AWS
	- Sin pagos iniciales, es un 80% mas barato que las soluciones tradicionales de centro de contacto
	- ![[Pasted image 20260422192042.png]]

## Comprehend
- Para el natural language processing - NLP (Procesamiento de lenguaje natural)
- Serverless
- Utiliza machine learning para encontrar ideas y relaciones en el texto
	- Lenguaje del texto
	- Extrae frases clave, lugares, personas, marcas o eventos
	- Comprende lo positivo o negativo del texto
	- Analiza el texto utilizando la tokenizacion y las partes del discurso
	- Organiza automaticamente una coleccion de archivos de texto por temas
- Ejemplos de casos de uso:
	- Analiza las interacciones con los clientes (correos electronicos) para encontrar lo que conduce a una experiencia positiva o negativa
	- Crea y agrupa articulos por temas que comprehend descubrira

## Comprehend medical
- Amazon comprehend medical detecta y devuelve informacion util en texto clinico no estructurado:
	- Notas del medico
	- Resumenes de alta
	- Resultados de pruebas
	- Notas de casos
- Utiliza NLP para detectar informacion sanitaria protegida (PHI) - API DetectPHI
- Almacena tus documentos en S3, analiza los datos en tiempo real con firehose, o utiliza transcribe para transcribir las narraciones de los pacientes en texto que pueda ser analizado por amazon comprehend medical.

## SageMaker
- Servicio totalmente gestionado para que los desarrolladores/cientificos de datos construyan modelos ML
- Normalmente, es dificil hacer todos los procesos en un solo lugar + aprovisionar servidores
- Proceso de machine learning (simplificado): predecir la nota de tu examen
- ![[Pasted image 20260422193526.png]]

## Forecast
- Servicio totalmente gestionado que utiliza ML para ofrecer predicciones muy precisas
- Ejemplo: predecir las futuras ventas de un chubasquero
- Un 50% mas de precision que simplemente mirando los datos 
- Reduce el tiempo de prevision 
- Caso de usoL planificacion de la demanda de productos, financiera, de recursosv
- ![[Pasted image 20260422193819.png]]

## Kendra
- Servicio de busqueda de documentos totalmente gestionado y potenciado por ML
- Extrae respuestas de un documento (texto, pdf, HTML, powerpoint, ms word, preguntas frecuentes, ...)
- Capacidades de busqueda en lenguaje natural
- Aprende de las interacciones/retroalimentacion de los usuarios para promover los resultados preferidos (aprendizaje incremental)
- Capacidad de afinar manualmente los resultados de la busqueda (importancia de los datos, frescura, personalizacion, ...)
- ![[Pasted image 20260422195009.png]]

## Personalize
- Servicio de ML totalmente gestionado para crear aplicaciones con recomendaciones personalizadas en tiempo real
- Ejemplo: recomendaciones/reclasificacion de productos personalizados, marketing directo personalizado
	- Ejemplo: el usuario compro herramientas de jardineria, proporciona recomendaciones sobre la proxima que debe comprar
- La misma tecnologia utiliza amazon.com
- se integra facilmente con sitios web existentes, aplicaciones sms, sistemas de marketing por correo electronico, etc...
- Se implementa en dias, no en meses (no es necesario construir, formar y desplegar soluciones de ML)
- Casos de uso: tiendas minoristas, medios de comunicacion y entretenimiento
- ![[Pasted image 20260422195503.png]]

## Textract
- Extrae automaticamente el texto, la escritura y los datos de cualquier documento escaneandolo
- Extrae datos de formularios y tablas
- Lee y procesa cualquier tipo de documento
- Casos de uso:
	- Servicios financieros (por ejemplo, facturas, informes financieros)
	- Sanidad (por ejemplo, historiales medicos, reclamaciones de seguros)
	- ![[Pasted image 20260422195855.png]]

## Quiz
Question 1:

Deberías utilizar Amazon Transcribe para convertir el texto en habla real utilizando el aprendizaje profundo.
- Amazon polly (aca yo dije transcribe confundi conceptos)

Question 2:

Una empresa quiere implementar un chatbot que convierta el habla en texto y reconozca las intenciones de los clientes. ¿Qué servicio debería utilizar?
- Amazon lex

Question 3:

¿Qué servicio totalmente gestionado puede ofrecer previsiones muy precisas?
- Forecast

Question 4:

Te gustaría encontrar objetos, personas, texto o escenas en imágenes y vídeos. ¿Qué servicio de AWS debe utilizar?
- Rekognition

Question 5:

Una empresa emergente quiere crear rápidamente experiencias de usuario personalizadas. ¿Qué servicio de AWS puede ayudar?
- AWS Personalize

Question 6:

Un equipo de investigación quiere agrupar artículos por temas utilizando el Procesamiento del Lenguaje Natural (PLN). ¿Qué servicio debería utilizar?
- Comprehend

Question 7:

Una empresa quiere convertir sus documentos a diferentes idiomas, con una redacción natural y precisa. ¿Qué deberían utilizar?
- AWS translate

Question 8:

Un desarrollador quiere construir, entrenar y desplegar rápidamente un modelo de Machine Learning. ¿Qué servicio puede utilizar?
- SageMaker

Question 9:

¿Qué servicio de AWS facilita la conversión de voz a texto?
- Transcribe

Question 10:

¿Cuál de los siguientes servicios es un servicio de búsqueda de documentos impulsado por Machine Learning?
- AWS Kendra

Question 11:

Una empresa gestiona una plataforma para compartir imágenes y vídeos que utilizan clientes de todo el mundo. La plataforma se ejecuta en AWS utilizando un bucket de S3 para alojar las imágenes y los vídeos y utilizando CloudFront como CDN para entregar el contenido a los clientes de todo el mundo con baja latencia. En los últimos dos meses, muchos clientes se han quejado de que han empezado a ver contenido inapropiado en la plataforma, lo que ha empezado a aumentar en la última semana. Sería muy costoso y llevaría mucho tiempo aprobar manualmente esas imágenes y vídeos por parte de los empleados antes de su publicación en la plataforma. Es necesario encontrar una solución que pueda detectar automáticamente las imágenes y vídeos inapropiados y ofensivos, y que te permita establecer un umbral mínimo de confianza para los elementos que se marcarán y que permita la revisión manual. ¿Qué servicio de AWS puede cumplir este requisito?
- AWS Rekognition

Question 12:

Una empresa médica online que permite reservar una cita con los médicos mediante una llamada telefónica está utilizando AWS para alojar su infraestructura. Están utilizando Amazon Connect y Amazon Lex para recibir llamadas y crear un flujo de trabajo, reservar una cita y pagar. Según la política de la empresa, todas las llamadas deben ser grabadas para su revisión. Sin embargo, es necesario eliminar cualquier información personal identificable (PII) de la llamada antes de guardarla. ¿Qué recomiendas utilizar que ayude a eliminar la IIP de las llamadas?
- AWS Transcribe (aca yo dije polly pero este punto habla de pasar habla a texto y eso lo hace solo transcribe)

Question 13:

Amazon Polly te permite convertir el texto en voz. Tiene dos características importantes. La primera es ...................., que te permite personalizar la pronunciación de las palabras (por ejemplo, "Amazon EC2" será "Amazon Elastic Compute Cloud"). La segunda es ...................., que te permite enfatizar las palabras, incluyendo los sonidos de la respiración, los susurros, etc.
- Lexicos de pronunciacion y lenguaje de marcado de sintesis del habla (SSML)

Question 14:

Una empresa médica está implementando una solución para detectar, extraer y analizar información de textos médicos no estructurados, como notas de los médicos, informes de ensayos clínicos e informes de radiología. Estos documentos se suben y almacenan en buckets S3. Según la normativa de la empresa, la solución debe diseñarse e implementarse para mantener la privacidad de los pacientes identificando la Información Sanitaria Protegida (PHI), por lo que la solución cumplirá con la HIPAA. ¿Qué servicio de AWS deberías utilizar?
- AWS Comprehend medical