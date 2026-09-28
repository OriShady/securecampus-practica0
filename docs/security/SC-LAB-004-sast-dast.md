# SC-LAB-004: Análisis de Seguridad Estático y Dinámico (SAST + DAST)

## 4. Parte A · SAST con Semgrep

### 4.1 Analiza primero como desarrollador
| Pregunta | Respuesta del equipo |
| :--- | :--- |
| **¿Qué dato controla el usuario?** | El parámetro `nombre` de la solicitud enviada desde el formulario de búsqueda al endpoint `/buscar`. |
| **¿A dónde llega ese dato?** | Se envía a la consulta de la base de datos (SQL) en `app.py` y se incrusta en la respuesta HTML de `webapp.py`. |
| **¿Qué riesgo observas?** | Vulnerabilidad de Inyección SQL por concatenación de cadenas, y Cross Site Scripting (XSS) reflejado por falta de escape en el HTML. |
| **¿Qué control propondrías?** | Implementar consultas parametrizadas en la base de datos y un motor de plantillas con *output escaping* para el frontend. |

### 4.2 Ejecuta Semgrep
| Evidencia SAST | Registro |
| :--- | :--- |
| **Regla/hallazgo** | `python.flask.security.audit.directly-returned-format-string` |
| **Archivo/línea** | `/src/src/webapp.py` - líneas 30 y 50 |
| **¿Qué evidencia aporta?** | El código devuelve directamente un string formateado con entrada del usuario, sin usar un motor de plantillas que aplique filtros de seguridad. |
| **¿Coincide con tu análisis humano?** | Sí, coincide con la detección del XSS. Sin embargo, la herramienta automatizada no detectó la vulnerabilidad de base de datos en `app.py`. |

### 4.3 Corrige y reanaliza
Se aplicó la corrección para evitar inyecciones SQL mediante una consulta parametrizada:

```python
consulta = (
    "SELECT id, nombre, correo "
    "FROM estudiantes "
    "WHERE nombre = ?"
)
cursor.execute(consulta, (nombre,))
```

---

## 5. Parte B · DAST con OWASP ZAP

### 5.2 Baseline: observación pasiva
| Hallazgo baseline | ¿Qué observa? | ¿Requiere análisis? |
| :--- | :--- | :--- |
| Missing Anti-clickjacking Header | Cabecera HTTP | Sí |
| X-Content-Type-Options Header Missing | Cabecera HTTP | Sí |
| Content Security Policy (CSP) Header Not Set | Política de contenido | Sí |
| Server Leaks Version Information via "Server" | Cabecera HTTP | Sí |

### 5.4 Validación manual del XSS
Se introdujo el payload `<script>alert(1)</script>` en el campo de búsqueda de la aplicación web local. Se observó la ejecución de una ventana emergente en el navegador, confirmando manualmente la vulnerabilidad de XSS reflejado.

### 5.6 y 5.7 Corrige el XSS y Retesting
Se modificó el archivo `webapp.py` para usar `render_template_string`, el cual escapa la entrada del usuario de manera segura mediante variables de plantilla:

```python
resultado_html = """...<p>Estudiante buscado: {{ nombre }}</p>..."""
return render_template_string(resultado_html, nombre=nombre)
```

Tras reiniciar el servidor Flask, se realizó el retesting con el mismo payload (`<script>alert(1)</script>`). El payload se renderizó como texto plano en el navegador y el ataque fue mitigado exitosamente.

---

## 6. Comparación SAST vs DAST
| Criterio | SAST | DAST |
| :--- | :--- | :--- |
| **Objeto** | Código fuente | Aplicación en ejecución |
| **Necesita ejecutar app** | No | Sí |
| **Perspectiva** | Interna/estática | Externa/dinámica |
| **Evidencia del lab** | Construcción HTML y SQL insegura | XSS y configuración HTTP |
| **Fortaleza** | Detecta patrones/rutas en código | Observa comportamiento real expuesto |
| **Límite** | No garantiza lógica de negocio | No ve todo el código ni todas las rutas |

---

## 8. Reflexión

**1. ¿Por qué 0 findings en SAST no equivale a aplicación segura?**
Porque las herramientas automatizadas trabajan mediante reglas predefinidas y tienen puntos ciegos (falsos negativos). Durante el laboratorio, el análisis SAST no detectó la inyección SQL que nosotros sí identificamos en la revisión manual.

**2. ¿Por qué un WARN de ZAP debe validarse antes de declararlo vulnerabilidad?**
Porque muchas advertencias están relacionadas con buenas prácticas o configuraciones estándar que pueden no ser explotables en un contexto específico o podrían romper la funcionalidad si se aplican a ciegas.

**3. ¿Qué diferencia observaste entre baseline y active scan?**
El *baseline* solo observó pasivamente las respuestas HTTP para detectar configuraciones faltantes sin atacar el servidor. El *active scan*, por el contrario, inyectó cargas útiles agresivas para encontrar fallos de ejecución como el XSS.

**4. ¿Por qué 200 OK no descarta una vulnerabilidad?**
Porque el servidor web puede procesar la solicitud con éxito y devolver la página (código HTTP 200), pero incluyendo en su respuesta el código malicioso inyectado (como ocurrió con nuestro XSS). 

**5. ¿Qué aprendiste del hecho de tener que reiniciar Flask antes del retest?**
Que las aplicaciones en ejecución cargan su código en la memoria RAM. Aunque modifiques el archivo fuente en el disco, la instancia viva de la aplicación no reflejará esos cambios de seguridad hasta que se detenga y vuelva a arrancar.

**6. ¿Qué problema de autorización podría seguir existiendo aunque SAST y DAST no lo reporten?**
Problemas de lógica de negocio o permisos, como vulnerabilidades de Control de Acceso Roto (IDOR/BOLA). Una herramienta no puede saber si un usuario tiene permiso legítimo para ver la información de otro, ya que eso depende de las reglas del negocio, no de la sintaxis del código.