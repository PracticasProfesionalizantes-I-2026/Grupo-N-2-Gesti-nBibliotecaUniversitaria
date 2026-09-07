# Caso de Uso: Generar Reportes

> Especificación elaborada siguiendo la guía
> `GUIA-Especificacion-Casos-de-Uso.md` (sección 3), a partir del documento
> `BiblioGest_CasosDeUso_Limpio.docx`.
> Reglas de negocio RN-17 (reportes básicos en el MVP) y RN-18 (estadísticas avanzadas
> fuera de alcance) **pendientes de implementación**; el proyecto se encuentra en
> etapa de análisis, por lo que los tests listados en la matriz de trazabilidad son
> **propuestos**, no implementados aún.

| Campo | Valor |
| --- | --- |
| **ID del Caso de Uso** | CU-07 |
| **Nombre** | Generar Reportes |
| **Actor Principal** | Bibliotecario / Administrador |
| **Actores Secundarios** | No aplica |
| **Alcance / Nivel** | Sistema; meta de usuario |
| **Stakeholders e intereses** | Bibliotecario/Administrador → contar con información resumida para el control y seguimiento de la biblioteca; Administración → tomar decisiones basadas en datos de uso del sistema |
| **Disparador (Trigger)** | El usuario necesita obtener información resumida para control o seguimiento |
| **Prioridad / Frecuencia** | Media; frecuencia periódica (consulta habitual, no en cada operación) |
| **Reglas de negocio relacionadas** | RN-17 (reportes del MVP deben ser básicos); RN-18 (gestión avanzada de reportes o estadísticas fuera del alcance inicial) |

---

### 1. BREVE DESCRIPCIÓN
Permite consultar información general sobre libros, préstamos, lectores y actividad
del sistema para facilitar el control y seguimiento de la biblioteca.

### 2. PRECONDICIONES
- El usuario debe haber iniciado sesión con un estado de autenticación activo (Token
  válido).
- Deben existir datos registrados en el sistema.

### 3. FLUJO PRINCIPAL (Camino Feliz - HTTP 200)
1. El usuario ingresa a la sección de reportes: el Actor envía una petición
   `GET /api/reportes` a la **Capa de Presentación**, que muestra las opciones de
   reportes disponibles.
2. El usuario selecciona el tipo de reporte que desea consultar
   (`GET /api/reportes/{tipo}`).
3. La **Capa de Presentación** solicita filtros si corresponde y valida su formato.
4. El usuario confirma la consulta.
5. La **Capa de Negocio** procesa la información registrada en la **Capa de
   Persistencia**, conforme a **RN-17**.
6. El Sistema devuelve el reporte solicitado con código **HTTP 200 OK**.

### 4. FLUJOS ALTERNATIVOS (Caminos Tristes / Excepciones)

* **5a. No existen datos suficientes (HTTP 200 OK con resultado vacío):**
  1. Si en el Paso 5 la Capa de Negocio detecta que no hay información registrada para
     generar el reporte.
  2. El Sistema devuelve un código **200 OK** con el reporte vacío, informando que no
     hay datos disponibles. Fin del caso de uso.

* **3a. Filtro inválido (HTTP 400 Bad Request):**
  1. Si en el Paso 3 el Sistema detecta que el filtro ingresado no es válido.
  2. La Capa de Presentación rechaza la petición por error de validación.
  3. El Sistema devuelve un código **400 Bad Request**, solicitando corregir los
     criterios de búsqueda. Fin del caso de uso.

### 5. SUB-VARIACIONES (opcional)
_No se identifican variaciones de datos o mecanismo adicionales a las descriptas en el
flujo principal y alternativo._

### 6. POSTCONDICIONES
- El usuario visualiza información útil para la toma de decisiones.
- **RN-18**: el reporte generado se limita al alcance básico del MVP, sin
  funcionalidades estadísticas avanzadas.

---

## Anexo: matrices de referencia

### Códigos HTTP usados

| Código HTTP | Nombre Técnico | Contexto de Aplicación en el Caso de Uso |
| --- | --- | --- |
| `200` | OK | Reporte generado con éxito (incluyendo el caso de resultado vacío por falta de datos). |
| `400` | Bad Request | Filtro de consulta inválido. |

### Matriz de trazabilidad CU-07 → Test (propuesta)

| Paso del CU | Excepción / Código | Test unitario (propuesto) | Test integración (propuesto) |
| --- | --- | --- | --- |
| Flujo principal | `200 OK` | `GetReporteAsync_WithValidType_ReturnsReportData` | `GetReporte_ReturnsSuccessAndData` |
| 5a. No existen datos suficientes | `200 OK` | `GetReporteAsync_WithNoData_ReturnsEmptyReport` | `GetReporte_WithNoData_Returns200OKEmpty` |
| 3a. Filtro inválido | `400 Bad Request` | `GetReporteAsync_WithInvalidFilter_ThrowsValidationException` | `GetReporte_WithInvalidFilter_Returns400BadRequest` |

> Regla de oro: cada flujo del caso de uso debe tener al menos un test. Al momento de
> esta especificación el sistema aún no cuenta con implementación (BiblioGest está en
> etapa de análisis), por lo que los nombres de test listados son una propuesta a
> implementar durante el desarrollo.
