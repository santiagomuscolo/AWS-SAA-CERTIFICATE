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