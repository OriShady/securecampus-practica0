# Mapa de Seguridad a lo largo del SDLC

**Laboratorio:** SC-LAB-002

**Equipop:** 
- _Orion Sara Hernandez_
- _Monserrat Guadaalupe Vega Vilchis_

**Fecha:** 18 AGO 2026


## Actividad guiada 

> **Caso:** un estudiante autenticado puede cambiar /calificaciones/125 por /calificaciones/126 y consultar calificaciones ajenas 

| Fase | ¿Qué debería hacerse? | Control/evidencia |
|---|---|---|
| **Requisitos** | Escribir claramente desde el inicio que cada estudiante solo puede ver sus propias calificaciones no las de otro como regla | Control de historial que sirva como la evidencia de autenticacion y acceso |
| **Diseño** | Decidir como se va a comprobar que el estudiante es dueño de esas calificaciones, es decir, validar correctamente las credenciales |Dentro de la documentación agregar esta regla de verificación antes de programarla. |
| **Desarrollo** | En la parte de la programacion revisar que el código verifique del lado del servidor si el usuario que pide los datos es realmente el dueño. | El código con esa validación y que alguien más del equipo lo revise |
| **Pruebas** | Iniciar sesión como el estudiante 125 e intentar ver las calificaciones del 126. El sistema no debe mostrar los datos. | Una prueba  que confirme que esto falla si se intenta acceder a datos ajenos. |
| **Despliegue** | Asegurarse de que la seguridad sea correcta antes de desplegarse | Configurar el proceso (CI/CD) para que bloquee la publicación si esta prueba no pasa. |
| **Operación/Mantenimiento** | Una vez el sistema ya está funcionando seguir vigilando acciones malisiosas si alguien intenta muchas veces acceder a calificaciones que no son suyas, el sistema debería registrar eso y avisar. | Tener registros de accesos denegados y un plan de qué hacer si se detecta un intento sospechoso. |

---

## Reto por equipo 
> Analicen los cuatro escenarios. Para cada uno, propongan al menos un control temprano y un control posterior.

| Escenario | Situacion | Control temprano | Control posterior   |
|---|---|---|---|
| **A · Documentos** | Un estudiante intenta descargar el documento de otro usuario modificando un identificador. | Desde el diseño y el código revisar siempre si la informacion que se a pedido pertenece a quien lo pide antes de darlo | Tener pruebas automáticas que intenten entrar erroneamente  y avisos si alguien intenta ver documentacion ajena |
| **B · Token** | Un desarrollador intenta incluir un token dentro de un commit. | Evitar escribir la clave directamente en el código, guardarla aparte y usar una herramienta que revise antes de subir el código si hay alguna clave escrita por error. | Que el sistema revise automáticamente y bloquee si detecta una clave, si ya se subió, cambiarla de inmediato. |
| **C · Profesor** | Un profesor intenta modificar calificaciones de un grupo no asignado. | Diseñar el sistema para que sepa qué grupos le pertenecen a cada profesor y no dejarlo modificar los demás aunque tenga el rol profesor | Revisar los registros de quién cambió qué notas y avisar si alguien edita algo fuera de su grupo asignado. |
| **D · Login** | Una cuenta registra 100 intentos fallidos de autenticación en 10 minutos. | Limitar los intentos para que después de varios intentos fallidos se bloque temporalmente o pedir un CAPTCHA. | Vigilar en tiempo real estos patrones raros y bloquear automáticamente o avisar al equipo de seguridad. |

---
---
---


| Escenario | Requisitos | Diseño | Desarrollo | Pruebas | Despliegue | Operación |
|---|---|---|---|---|---|---|
| **A** | Una regla donde solo tu puedas ver tus documentos | Cómo verificar dueño del documento | Código que verifica el dueño | Prueba que intenta ver documento ajeno | No publicar si la prueba falla | Vigilar descargas raras |
| **B** | Una regla donde no puedes incluir claves en el código | Uso de variables de entorno | Revisión antes de subir código | Verificación automática de que no hay claves | Bloquear subida si hay una clave | Cambiar la clave filtrada |
| **C** | Una regla donde cada profesor solo pueda ver a su grupo asignado | Diseño de permisos por grupo | Código que revisa el grupo | Prueba con profesor de otro grupo | Revisar permisos antes de publicar | Avisos si alguien edita fuera de su grupo |
| **D** | Una regla donde se limiten los intentos de login | Diseño del bloqueo/CAPTCHA | Código que cuenta los intentos | Simular varios intentos fallidos | Ajustar el límite en producción | Avisos y bloqueo automático |

---

## Clasificación conceptual 

| # | Decisión | Secure SDLC / By Design / By Default / Shift Left | Justificación |
|---|---|---|---|
| **1** | Establecer desde los requisitos que cada usuario solo puede ver lo suyo | Security by Design y Shift Left | Es by design porque se esta pensando en la seguridad antes de programar y es shift left porque lo hicimos en la etapa más temprana posible, en vez de esperar a que el sistema ya esté hecho. |
| **2** | Hacer que el bloqueo de cuenta tras varios intentos fallidos este activado desde el principio, sin que nadie tenga que configurarlo. | Security by Default | Es by default porque la protección ya viene puesta, el usuario no tiene que hacer nada extra para estar protegido|

---

## 5. Reflexión

**¿Qué riesgo de SC-LAB-001 necesitó controles en más fases?**
Se necesitó pensarse desde los requisitos, revisarse en el diseño y desarrollo pñara despues poderse probar y vigilarse en operación

**¿Qué habría pasado si el equipo hubiera esperado hasta Pruebas para pensar en seguridad?**
Probablemente se haya dejado pasar por alto algunas cosas y el sistema ya estaría diseñado y programado de una forma que no nos permitiera corregir el problema fácilmente

**¿Qué control depende de una regla de negocio y cuál se puede automatizar?**
- Depende de una regla de negocio saber qué profesor está asignado a qué grupo 
- Se puede automatizar la revision de que no haya claves secretas en el código y bloquear una cuenta tras muchos intentos fallidos de login
