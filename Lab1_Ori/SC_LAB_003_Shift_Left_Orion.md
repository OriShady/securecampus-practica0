# SC-LAB-003 · Costo de Corrección y Shift Left

**RF-010:** «SecureCampus debe permitir al usuario recuperar su contraseña». El enlace generado tendra una duracion de 7 días y se podra reutilizar varias veces.

| Pregunta | Respuesta del equipo |
| :--- | :--- |
| **¿Dónde se originó principalmente la omisión?** | En la fase de Requisitos y Diseño, pues se omitieron las restricciones y reglas de negocio enfocadas en seguridad. |
| **¿Dónde podría descubrirse?** | En la fase de Pruebas o peor, en Producción si un atacante aprovecha el enlace. |
| **¿Qué artefactos habría que cambiar si se descubre en pruebas?** | Los documentos de requisitos, diagramas de arquitectura/flujo, el esquema de base de datos, el código del backend, y los casos de prueba de QA. |
| **¿Qué requisitos/criterios de seguridad faltaron?** | Expiración de tiempo con un plazo corto de 15 a 30 minutos e invalidación del token tras su primer uso, es decir, un token de uso único. |
| **¿Qué moverían a la izquierda?** | Definir los Criterios de Seguridad directamente durante la fase de requisitos y diseño.  |

---

### 4. Reto integral · tres situaciones

| Caso | Origen | Descubrimiento | Retrabajo/impacto | Actividad Shift Left | Control posterior |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **A** | Requisitos | Pruebas / Producción | **Alto:** Implica refactorizar la lógica de autorización y la base de datos en múltiples módulos. | Definir matrices de roles y permisos explícitos en las historias de usuario. | Monitoreo y auditoría de los logs de acceso. |
| **B** | Diseño / Requisitos | Pruebas / Producción | **Medio-Alto:** Implementar librerías de validación de archivos, ajustar almacenamiento y reescribir APIs. | Realizar modelado de amenazas en la fase de diseño de la arquitectura. | Escaneo de malware en el servidor y configuración de WAF. |
| **C** | Mantenimiento / Operación | Producción | **Bajo-Medio:** Actualizar la versión de la dependencia y ejecutar las pruebas de regresión. | Integrar análisis de composición automatizado en el pipeline de CI/CD. | Alertas automatizadas de vulnerabilidades. |

---

### 5. Escalera de costo cualitativa
**Análisis basado en el Caso A (Administrador sin control de permisos definidos)**

| Momento | ¿Qué habría que corregir/revisar? | Costo/retrabajo: Bajo/Medio/Alto + por qué |
| :--- | :--- | :--- |
| **Requisitos** | Ajustar el requerimiento para especificar los permisos de consulta y modificación. | **Bajo:** Solo toma minutos editar la documentación y acordarlo con los stakeholders. |
| **Diseño** | Modificar los diagramas de clases, flujos de base de datos y diseño de API. | **Bajo/Medio:** Requiere tiempo de análisis técnico, pero aún no hay código escrito. |
| **Desarrollo** | Borrar/modificar código escrito, reestructurar controladores y base de datos. | **Medio:** Hay pérdida de horas de programación por lo tanto hay menos productividad. |
| **Pruebas** | Todo lo anterior, más invalidar pruebas pasadas, reescribir scripts y repetir el ciclo de QA. | **Alto:** Involucra a múltiples equipos, cuellos de botella e interrumpe el flujo hacia la producción. |
| **Producción** |  Parches de emergencia, gestión de incidentes, posible filtracion de calificaciones. | **Muy Alto:** Daño a la reputación, estrés del equipo, multas de cumplimiento e impacto directo en productividad. |

---

### 6. Pregunta con truco conceptual (Dependencias)
**¿Puede Shift Left ayudar con una vulnerabilidad que todavía no existía públicamente cuando desarrollamos?**

**Shift Left prepara al equipo**. Al integrar herramientas de escaneo de dependencias y mantener un inventario de software desde las primeras etapas del desarrollo, el equipo tiene visibilidad inmediata. Cuando la vulnerabilidad crítica descubre, el sistema alerta automáticamente y esto evita que pasen esos 2 meses de exposición.

---

### 7. Reflexión

* **¿Shift Left elimina la necesidad de seguridad en operación?**
  No. La seguridad operativa es obligatoria porque la infraestructura cambia, surgen nuevas amenazas y las dependencias sufren vulnerabilidades despues del despliegue.
* **¿Por qué una funcionalidad puede cumplir su requisito funcional y seguir siendo insegura?**
  Porque los requisitos funcionales solo describen el camino facil (lo que el sistema debe hacer cuando se usa correctamente). La seguridad evalúa los "casos de abuso" (lo que el sistema NO debe permitir ante un uso malicioso o inesperado).
* **¿Qué decisión de su equipo habría sido más barata de corregir antes?**
   Solucionar problemas de roles  o almacenamiento inseguro de contraseñas es muy barato en un diagrama de diseño, pero costoso de refactorizar cuando el backend, las APIs y el frontend ya están acoplados a un modelo que no es seguro.