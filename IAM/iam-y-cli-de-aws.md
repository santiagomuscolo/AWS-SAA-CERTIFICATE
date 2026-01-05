**IAM: Usuarios y Grupos**

- IAM = Identity and Access Management, servicio global.
- Cuenta raiz / root creada por defecto, no debe ser utilizada ni compartida.
- Los usuarios son personas dentro de tu organizacion, y pueden ser agrupados.
- Los grupos solo contienen usuarios, no otros grupos.
- Los usuarios no tienen que pertenecer a un grupo, y el usuario puede pertenecer a varios grupos.

**IAM: Permisos**

- A los usuarios o grupos se les puede asignar documentos JSON llamados politicas.
- Estas politicas definen los permisos de los usuarios.
- En AWS se aplica el principio de minimo privilegio: no dar mas permisos de los que un usuario necesita.

![image.png](attachment:4463512c-6838-4fe4-8e49-3ca16752db71:image.png)

**Politicas de IAM**

**Herencia de politicas en IAM**

Los usuarios pueden tener asignadas politicas en linea de forma individual, sin embargo, los grupos pueden tener asignadas politicas cuyos usuarios heredaran.

![image.png](attachment:c4f7ea0e-b3d3-4f8b-af9a-3b3e03c5975d:image.png)

**Estructura de las politicas IAM**

![image.png](attachment:2239448b-b9e8-4fa4-ad07-9d9d0bfcd3a0:image.png)

- Consta de:
    - Version: version del lenguaje de la politica, siempre incluye “2012-10-17”.
    - Id: un identificador para la politica (opcional).
    - Statement: una o mas delcaraciones individuales (obligatorio).

- Las declaraciones constan de:
    - Sid: un identificador para la declaracion (opcional).
    - Effect: si la sentencia permite o deniega el acceso (Permitir, Denegar).
    - Principal: cuenta/usuario/rol al que se le aplica esta politica.
    - Action: lista de acciones que esta politica permite o deniega.
    - Resource: lista de recursos a los que se aplican las acciones.
    - Condition: condiciones para cuando esta politica esta en efecto (opcional).

**IAM - Politica de contraseñas**

- Passwords fuertes = mayor seguridad para tu cuenta.
- En AWS, puedes configurar una politica de passwords:
    - Establecer una longitud minima
    - Requerir tipos de caracteres especificos:
        - Incluyendo letras mayusculas
        - letras minusculas
        - numeros
        - caracteres no alfanumericos
    - Permitir a todos los usuarios de IAM cambiar sus propias passwords
    - Requerir a los usuarios que cambien su password despues de un tiempo
    - Impedir la reutilizacion de la password.

**Multi Factor Authentication - MFA**

- Los usuarios tienen acceso a tu cuenta y posiblemente pueden cambiar configuraciones o eliminar recursos en tu cuenta de AWS.
- Quieres proteger tus cuentas root y los usuario de IAM.
- MFA = password que conoces + dispositivo de seguridad que posees.
- Principal beneficio de MFA: si una password es robada o hackeada, la cuenta no se ve comprometida.

**Opciones de dispositivos MFA en AWS**

![image.png](attachment:6710a39e-2814-420e-91af-12516d437c48:image.png)

![image.png](attachment:3d044065-0010-4f70-9181-0ec40def8d20:image.png)

Como pueden los usuarios acceder a AWS?

- Para acceder a AWS, tienes tres opciones:
    - Consolda de administracion de AWS: protegida por password + MFA.
    - Interfaz de linea de comandos de AWS (CLI): protegida por claves de acceso.
    - AWS Software Developer Kit (SDK) - para el codigo: protegido por claves de acceso.
    - Las claves de acceso se generan a traves de la consola de AWS
- Los usuarios gestionan sus propias claves de acceso.
- Las claves de acceso son secretas, como una password no se comparten.
- Id de la clave de acceso = nombre de usuario.
- Clave de acceso secreta  = password.

![image.png](attachment:324b40c8-11dc-48df-a0ec-20cb673d3e8c:image.png)

Que es la CLI de AWS?

- Una herramienta que permite interactuar con los servicios de AWS mediante comandos en tu shell de linea de comandos.
- Acceso directo a las API publicas de los servicios de AWS.
- Puedes desarrollar scripts para gestionar tus recursos.
- Es de codigo abierto
- Alternativa al uso de la consola de administracion de AWS.

![image.png](attachment:6700576f-0246-4289-9411-f9d31bf488f1:image.png)

Que es el SDK de AWS?

- Kit de desarrollo de software de AWS (AWS SDK).
- APIs especificas para cada lenguaje (conjunto de bibliotecas).
- Permite acceder y administrar los servicios de AWS mediante programacion.
- Integrado en la aplicacion.
- Admite:
    - SDKs (Javascript, Python, PHP, .NET, Ruby, Java, Go, Node.js, C++).
    - SDKs para moviles (Android, iOS).
    - SDKs para dispotivos loT (Embedded C, Arduino, …).

**Roles de IAM para los servicios de AWS**

- Algun servicio de AWS tendra que realizar acciones en tu nombre.
- Para ello, asignaremos permisos a los servicios de AWS con Roles IAM.
- Roles comunes:
    - Roles de instancia EC2
    - Roles de la funcion Lambda
    - Roles para CloudFormation.

![image.png](attachment:61eacf28-af9a-474f-bf5b-0a88f62f6df4:image.png)

**Herramientas de seguridad de IAM**

- IAM Credentials Report / Informe de credenciales de IAM (a nivel de cuenta)
    - Un informe que enumera todos los usuarios de tu cuenta y el estado de tus diversas credenciales.
- IAM Access Advisor / Asesor de acceso de IAM (a nivel de usuario)
    - Muestra todos los permisos de servicio concedidos a un usuario y cuando se accedio a estos por ultima vez.
    - Puedes utilizar esta informacion para revisar tus politicas.

**Directrices y buenas practicas de IAM**

- No utilices la cuenta root excepto para la configuracion de la cuenta AWS
- Un usuario fisico = Un usuario AWS
- Asignar usuarios a grupos y asignar permisos a grupos.
- Crear una politica de passwords fuertes.
- Utilizar y reforzar el uso de la autenticacion multifactor (MFA)
- Crear y utilizar Roles para dar permisos a los servicios de AWS.
- Utilizar claves de acceso para el acceso programatico (CLI/SDK)
- Revisar los permisos de tu cuenta con el informa de credenciales de IAM o access advisor de IAM.
- No compartir nunca los usuarios de IAM ni las claves de acceso.