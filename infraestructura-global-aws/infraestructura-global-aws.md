- Aws Regions
    - AWS Availability Zones
        - AWS Data Centers
            - AWS Edge Locations / Points of Presence

Aws tiene Regiones en todo el mundo, estas estan nomencladas como por ejemplo us-east-1, eu-west-3, etc…

**Que es una region?**

Una region es un conjunto de availability zones fisicamente separadas dentro de un area geografica

- La mayoria de los servicios de AWS son de ambito regional

**Como elegir una region de AWS?**

Si necesitas lanzar una nueva aplicacion, donde debemos hacerlo?
Bueno esta pregunta a grandes rasgos se para sobre 4 puntos escenciales:

- Cumplimiento de los requisitos legales y de gobernanza de datos: los datos nunca salen de una region sin tu permiso explicito.
- Proximidad a los clientes: latencia reducida.
- Servicios disponibles en una region: los nuevos servicios y las nuevas funciones no estan disponibles en todas las regiones.
- Precios: los prescios varian de una region a otra y son transparentes en la pagina de precios del servicio.

**Zonas de disponibilidad de AWS**

- Cada region tiene muchas zonas de disponibilidad (generalmente 3 o mas) Ejemplo:
    - ap-southeast-2a (la letra a, b y c es relativa a cada cuenta no global)
    - ap-southeast-2b
    - ap-southeast-3c
- Cada zona de disponibilidad (AZ) es uno o varios centros de datos discretos, con alimentacion, red y conectividad redundantes.
- Estan separadas una de otras, de modo que estan aisladas de catastrofes.
- Estan conectadas con redes de alto ancho de banda y latencia ultrabaja.

**Puntos de presencia de AWS (Edge Locations)**

- Amazon tiene +450 puntos de presencia (+10 caches regionales) en +90 ciudades de +40 paises.
- El contenido se entrega a los usuarios finales con menor latencia

**Tour por la consola de AWS**

- AWS cuenta con servicios globales:
    - Identity and Access Management (IAM)
    - Route 53 (Servicio de Domain Network System DNS)
    - CloudFront (Red de entrega de contenido CDN)
    - WAF (Firewall de aplicaciones web)
- La mayoria de los servicios de AWS son de ambito regional (no estan todos detallados):
    - Amazon EC2 (Infraestructura como servicio)
    - Elastic Beanstalk (Plataforma como servicio)
    - Lambda (Funcion como servicio)
    - Rekognition (Software como servicio)