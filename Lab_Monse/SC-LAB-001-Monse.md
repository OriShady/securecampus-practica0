
# SC-LAB-001  Identificación inicial de activos, amenazas, vulnerabilidades, ataques, impactos, riesgos y controles

**Proyecto:** SecureCampus

**Curso:** Desarrollo Seguro

**Integrantes:** 
- Orion Sara Hernandez
- Monserrat Guadalupe Vega Vilchis 

**Fecha:** 11/09/2026


## Actividad 
### Consulta de perfiles
> Caso: María inicia sesión con el perfil 125. Al observar la URL, cambia manualmente /perfil/125 por /perfil/126. El sistema devuelve información de otro estudiante.

| Elemento | Respuesta del equipo | Justificación |
|---|---|---|
| **Activo** | Perfil de otro estudiante, sus datos personales | El perfil del usuario y sus datos personales son el objeto que se quiere proteger. 
| **Amenaza** | Un usuario que intenta ver otros perfiles alterando la URL | La amenza existe cuando un usuario legítimo (estudiante) actúa con curiosidad o intención maliciosa para evadir las restricciones del sistema.
| **Vulnerabilidad** | Que no se valida que la URL (`125`) sea del  usuario autenticado | El servidor está confiando en el dato de una URL sin consultar o autenticar los permisos del usuario que esta activo.
| **Ataque** | Que se cambia manualmente perfil/125 por perfil/126 | Ocurre cuando el usuario actual se aprovecha de la vulnerabilidad y manipula el navegador para consultar información que no le pertenece.
| **Impacto** | Que se pueden ver los datos personales de otro estudiante ya que se está accediendo a su perfil | Se pierde por completo la confiabilidad y la confidencialidad del usuario dejando expuestos sus datos.
| **Control** | Validar en el servidor que lo solicitado pertenece al usuario autenticado antes de responder | La seguridad no le corresponde al navegador, sino que el servidor debe verificar si el usuario activo coincide con el ID solicitado.  
| **Riesgo** | Alta probabilidad de que se puedan alterar los datos | Es un riesgo alto, pues cambiar una URL es una acción demasiado fácil, si el sistema no protege la lectura puede que tampoco proteja la modificación de datos. 

## Escenarios

| Escenario | Activo | Amenaza | Vulnerabilidad | Ataque | Impacto | Control |
|---|---|---|---|---|---|---|
| **1. Calificaciones** | Privacidad de las calificaciones de los estudiantes | Estudiante o profesor que no tengan autorización intente ver o modificar calificaciones que no le corresponden | Que no se tenga una validación de autorización en el servidor al resolver | Al modificar la URL se pueden ver las calififcaciones | Que se puedan ver los datos académicos privados y sea posible cambiar las calificaciones | Verificación de autorización en el servidor por cada solicitud|
| **2. Documentos** | Documentos académicos de los estudiantes | Usuario que intenta acceder, ver o posiblemente descargar documentos de otros estudiantes | Enlaces de descarga sin proteccion o falra de control en el acceso a los documentos | Se podria modificar el identificador del archivo en la URL, sin sesión con permiso sobre ese documento | Filtración de información, riesgo de robo de docuementacion | Autorización por documento, enlaces de un solo uso por tiempo de expiración y registro de accesos |
| **3. Autenticación** | Identidad y credenciales de todos los usuarios | Persona que quiera autenticarse con credenciales que no sean suyas| Políticas débiles de contraseña, sin límite de intentos, o recuperación de acceso inseguro | Que se puede accedar cal los perfiles ajenos con las credenciales | Suplantación del usuario y mal uso de las credenciales | Bloqueo tras intentos fallidos, validación en recuperación de contraseña, alertas ante inicios de sesión sospechosos |
| **4. Perfiles**| Usuario | que un usuario intenta ver el perfil de alguien más | Que no sea validado a quien pertenece | que se realicen modificaciones del perfil, connfidencialidad de los datos| que puede ver los datos peerosnales del otro perfil | uso indebido de los datos personales obtenidos | Validar credenciales |

## Preguntas

**1. ¿Una amenaza y una vulnerabilidad son lo mismo? Explica con un ejemplo de SecureCampus.**
No. La amenaza es cuando la situación o el actor que podría causar daño y la vulnerabilidad es como el punto debil del sistema, permite que pase. Por ejemplo la *amenaza* es cuando alguien que intenta ver calificaciones que no son suyas y la *vunerabilidad* es que el servidor no valide a quién pertenece lo que se esta solicitando.

**2. ¿Puede existir una vulnerabilidad aunque todavía nadie la haya explotado?**
Sí, una vulnerabilidad siempre es una debilidad presente independientemente si alguien la ha desscubierto o usado

**3. ¿Un usuario autenticado está automáticamente autorizado para cualquier recurso?**
No, autenticacion y auotrizacion son 2 cosas distintas, la autenticación quien inicia sesion, es decir, quien eres y la autorizacion define que puede ver o hacer el usuario dentro del sistema.

**4. ¿Qué control de los propuestos debería definirse desde requisitos o diseño? ¿Por qué?**
Que el servidor valide que lo solicitado pertenece al usuario autenticado antes de responder o realizar una acción, porque es un principio de seguridad que es complicado resolver despues 

**5. ¿Qué activo consideran más crítico y por qué?**
Los datos pesonales y los documentos personales o academicos ya que es informacion sensible y confidencial, estos quedan expuestos y violan la privacidad de los usuarios, tiene un impacto legal para la institución 

