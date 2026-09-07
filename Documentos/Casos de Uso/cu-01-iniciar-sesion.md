# Caso de Uso: Iniciar Sesión

> Especificación elaborada siguiendo la guía
> `GUIA-Especificacion-Casos-de-Uso.md` (sección 3), a partir del documento
> `BiblioGest_CasosDeUso_Limpio.docx`.
> Reglas de negocio RN-01 (usuarios registrados y activos) y RN-02 (contraseñas de
> mínimo 6 caracteres, almacenadas de forma segura) **pendientes de implementación**;
> el proyecto se encuentra en etapa de análisis, por lo que los tests listados en la
> matriz de trazabilidad son **propuestos**, no implementados aún.

| Campo | Valor |
| --- | --- |
| **ID del Caso de Uso** | CU-01 |
| **Nombre** | Iniciar Sesión |
| **Actor Principal** | Bibliotecario / Administrador |
| **Actores Secundarios** | No aplica |
| **Alcance / Nivel** | Sistema; meta de usuario |
| **Stakeholders e intereses** | Bibliotecario/Administrador → acceder de forma segura a las funcionalidades correspondientes a su rol; Administración → garantizar que solo personal autorizado opere el sistema |
| **Disparador (Trigger)** | El usuario solicita ingresar al sistema |
| **Prioridad / Frecuencia** | Alta; uso muy frecuente (primer paso de cada sesión de trabajo) |
| **Reglas de negocio relacionadas** | RN-01 (solo usuarios registrados y activos); RN-02 (contraseñas de mínimo 6 caracteres, almacenamiento seguro) |

---

### 1. BREVE DESCRIPCIÓN
Permite que el personal autorizado ingrese al sistema para acceder a las
funcionalidades según su rol.

### 2. PRECONDICIONES
- El usuario debe estar registrado en el sistema.
- El usuario debe tener una cuenta activa.

### 3. FLUJO PRINCIPAL (Camino Feliz - HTTP 200)
1. El usuario accede al inicio de sesión: el Actor envía una petición
   `POST /api/auth/login` a la **Capa de Presentación** con usuario/email y
   contraseña.
2. La **Capa de Presentación** valida que el formato de los datos (esquema) sea
   correcto.
3. La **Capa de Negocio** valida las credenciales contra la **Capa de Persistencia**,
   conforme a **RN-01** y **RN-02**.
4. El Sistema genera el token de sesión y devuelve un código **HTTP 200 OK** con el
   token y el panel correspondiente al rol del usuario.

### 4. FLUJOS ALTERNATIVOS (Caminos Tristes / Excepciones)

* **3a. Credenciales incorrectas (HTTP 401 Unauthorized):**
  1. Si en el Paso 3 la Capa de Negocio detecta que el usuario/email existe pero la
     contraseña no coincide.
  2. El Sistema informa que las credenciales son inválidas.
  3. El Sistema devuelve un código **401 Unauthorized**, permitiendo reintentar el
     ingreso. Fin del caso de uso.

* **3b. Usuario inexistente (HTTP 401 Unauthorized):**
  1. Si en el Paso 3 la Capa de Negocio detecta que no existe una cuenta asociada al
     usuario/email ingresado.
  2. El Sistema informa que el usuario no se encuentra registrado (mismo código que
     3a, para no revelar cuál dato es incorrecto).
  3. El Sistema devuelve un código **401 Unauthorized** y regresa al inicio de sesión.
     Fin del caso de uso.

### 5. SUB-VARIACIONES (opcional)
_No se identifican variaciones de datos o mecanismo adicionales a las descriptas en el
flujo principal y alternativo._

### 6. POSTCONDICIONES
- El usuario queda autenticado dentro del sistema.
- El sistema habilita las funcionalidades correspondientes a su rol.

---

## Anexo: matrices de referencia

### Códigos HTTP usados

| Código HTTP | Nombre Técnico | Contexto de Aplicación en el Caso de Uso |
| --- | --- | --- |
| `200` | OK | Autenticación exitosa; devuelve el token de sesión. |
| `401` | Unauthorized | Credenciales incorrectas (RN-02) o usuario inexistente/inactivo (RN-01). |

### Matriz de trazabilidad CU-01 → Test (propuesta)

| Paso del CU | Excepción / Código | Test unitario (propuesto) | Test integración (propuesto) |
| --- | --- | --- | --- |
| Flujo principal | `200 OK` | `LoginAsync_WithValidCredentials_ReturnsTokenAndRole` | `Login_ReturnsSuccessAndToken` |
| 3a. Credenciales incorrectas | `401 Unauthorized` | `LoginAsync_WithWrongPassword_ThrowsUnauthorizedException` | `Login_WithWrongPassword_Returns401Unauthorized` |
| 3b. Usuario inexistente | `401 Unauthorized` | `LoginAsync_WithUnknownUser_ThrowsUnauthorizedException` | `Login_WithUnknownUser_Returns401Unauthorized` |

> Regla de oro: cada flujo del caso de uso debe tener al menos un test. Al momento de
> esta especificación el sistema aún no cuenta con implementación (BiblioGest está en
> etapa de análisis), por lo que los nombres de test listados son una propuesta a
> implementar durante el desarrollo.
