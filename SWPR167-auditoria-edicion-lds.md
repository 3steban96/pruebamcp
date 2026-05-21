---
id: SWPR167-auditoria-edicion-lds
status: IN_PROGRESS
feature: auditoria-edicion-lds
created: '2026-05-19'
updated: '2026-05-20'
author: spec-generator
reviewer: copilot-analysis
version: '1.2'
related-specs: []
microservicios:
- ms-audit
- ms-admin-wellnesspath
- ms-command-experience
- ms-experiences
dependencias-readonly:
- ms-customers
type: spec
relations: {}
---

---
id: SPEC-033
status: IN_PROGRESS
feature: auditoria-edicion-lds
created: 2026-05-19
updated: 2026-05-20
author: spec-generator
reviewer: copilot-analysis
version: "1.2"
related-specs: []
microservicios: [ms-audit, ms-admin-wellnesspath, ms-command-experience, ms-experiences]
dependencias-readonly: [ms-customers]
---

# Spec: Auditoría de Edición de Líneas de Solución (LDS)

> **Estado:** `DRAFT` → aprobar con `status: APPROVED` antes de iniciar implementación.
> **Ciclo de vida:** DRAFT → APPROVED → IN_PROGRESS → IMPLEMENTED → DEPRECATED
>
> ⚠️ **Versión 1.1 — Correcciones post-análisis de código:**
> Se identificaron inconsistencias entre el diseño original y el estado real del codebase.
> Ver sección **5. HALLAZGOS DE ANÁLISIS** antes de implementar.

---

## 1. REQUERIMIENTOS

### Descripción

Implementar un sistema de auditoría integral que registre automáticamente todas las modificaciones realizadas a las Líneas de Solución (LDS) por parte de usuarios administradores. El sistema debe capturar los valores anteriores y actuales de cada campo modificado, almacenándolos en formato JSON para garantizar la trazabilidad completa de los cambios y cumplir con los requisitos de compliance.

### Requerimiento de Negocio

**HU-33237**: Como administrador, quiero que el sistema registre los logs de edición de las LDS, para poder consultar el historial de cambios y mantener trazabilidad de todas las modificaciones.

Dado que el usuario administrador que tiene permiso de edición cuando edita una LDS, entonces se deberá guardar un registro de log en audit que contenga los siguientes datos:
- Id LDS
- Nombre LDS
- Fecha y hora de edición
- Nombres y apellidos usuario que realizó la edición
- Estado del cambio: edición
- Detalle del cambio para edición que se deberá mostrar en JSON para visualizar (valor anterior y valor actual) incluyendo el estado para identificar el ciclo de vida de esta.

**Campos a auditar:**

**Tab Información General:**
- nombre
- descripción
- estratega de la línea de solución (solutionLineStrategist)

**Tab Visualización:**
- nombre de la línea de solución
- autenticación - Acceso sin autenticación (check)
- visualización - Activar visualización (check actionDisplay)
- criterios de visualización (displayCriteria):
  - Grupo predeterminado: check + selección
  - Por asociación de empresas: check
  - Por estado de afiliación a caja: check + selección
  - Por estado de afiliación a plan básico de salud: check + selección
  - Por estado de afiliación a plan complementario: check + selección
  - Por transacción: check + selección
  - Usuarios activos: check
- Icono de botón de la línea de solución (URL)

**Tab Asignación:**
- Asignar a (Todas las empresas / Empresas específicas)
- Selección (empresas específicas seleccionadas)
- Nombre de la línea de solución (si se ejecutan cambios en la selección)
- Imagen línea de solución (URL si se ejecutan cambios en la selección)

**Tab Descripción:**
- Título
- Descripción
- Imagen de descripción (URL)
- Videos (URLs)

**Tab Parametrización:**
- Contiene Ruta: check + selección (nombre de ruta)

**Reglas:**
- Los atributos que no registren cambios NO estarán relacionados en el log.
- No requiere diseño en Figma.

### Historias de Usuario

#### HU-01: Registro automático de auditoría en edición de LDS

```
Como:        Sistema (consumido por Administrador)
Quiero:      Registrar automáticamente todos los cambios realizados en una LDS
Para:        Mantener trazabilidad completa, compliance y facilitar la resolución de incidentes

Prioridad:   Alta
Estimación:  L
Dependencias: Ninguna
Capa:        Backend (ms-audit, ms-command-experience, ms-admin-wellnesspath)
```

#### Criterios de Aceptación — HU-01

**Happy Path**

```gherkin
CRITERIO-1.1: Registro exitoso de auditoría en edición completa de LDS
  Dado que:  un administrador autenticado con permisos de edición
         Y:  una LDS existente con id "lds-001"
  Cuando:    el administrador edita múltiples campos de la LDS (nombre, descripción, estratega, visualización, asignación)
         Y:  confirma los cambios
  Entonces:  el sistema guarda un registro en ms-audit con:
         -  id del log (UUID generado)
         -  solutionLineId = "lds-001"
         -  solutionLineName (valor actual)
         -  changeType = "UPDATE"
         -  modificationUser: userId del admin (sub de Cognito)
         -  userName: nombre completo (resuelto vía ms-customers)
         -  modificationDate con timestamp UTC en millis
         -  changeDetail en JSON con estructura:
            {
              "nombre": { "previousValue": "Salud Física", "currentValue": "Salud y Bienestar Físico" },
              "descripcion": { "previousValue": "...", "currentValue": "..." },
              ...
            }
         Y:  el log incluye SOLO los campos modificados (no los sin cambios)
         Y:  retorna HTTP 200 al frontend
```

```gherkin
CRITERIO-1.2: Registro cuando se edita solo un campo de un tab específico
  Dado que:  un administrador autenticado
         Y:  una LDS existente con id "lds-002"
  Cuando:    el administrador edita solo el campo "nombre" en el Tab Información General
         Y:  guarda sin modificar otros campos
  Entonces:  el sistema guarda un log en ms-audit con:
         -  changeDetail JSON conteniendo SOLO el campo "nombre":
            { "nombre": { "previousValue": "Deporte y Recreación", "currentValue": "Actividad Física" } }
         Y:  NO incluye otros campos en changeDetail
```

```gherkin
CRITERIO-1.3: Registro de cambios en criterios de visualización (checkboxes + selección)
  Dado que:  un administrador autenticado
         Y:  una LDS existente con displayCriteria = ["CORPORATE"]
  Cuando:    el administrador activa "Grupo predeterminado" (check) y selecciona grupo "GRP-001"
         Y:  activa "Por transacción" (check) y selecciona experiencia "EXP-123"
  Entonces:  el sistema guarda un log con changeDetail incluyendo los campos modificados
```

```gherkin
CRITERIO-1.4: Registro de cambios en asignación empresas específicas
  Dado que:  una LDS con assignmentToCorporates = "ALL"
  Cuando:    el admin cambia a "SPECIFIC" y selecciona empresas ["EMP-001", "EMP-002"]
  Entonces:  el log incluye los cambios de asignación con previousValue y currentValue
```

**Error Path**

```gherkin
CRITERIO-1.5: Rechazo de edición sin permisos
  Dado que:  un usuario autenticado SIN permisos de administración
  Cuando:    intenta editar una LDS
  Entonces:  el sistema retorna HTTP 403 Forbidden
         Y:  NO se guarda ningún log de auditoría
         Y:  mensaje: "Usuario no autorizado para editar LDS"
```

```gherkin
CRITERIO-1.6: Manejo de error si ms-audit no está disponible
  Dado que:  un administrador edita una LDS correctamente
         Y:  ms-audit no responde o está caído
  Cuando:    el sistema intenta enviar el evento de auditoría vía RabbitMQ
  Entonces:  el mensaje de auditoría se deriva a la Dead Letter Queue (DLQ)
         Y:  la edición de la LDS se completa exitosamente (HTTP 200)
         Y:  el sistema registra un log de error indicando falla en auditoría
```

```gherkin
CRITERIO-1.7: Validación cuando la LDS no existe
  Dado que:  un administrador autenticado
  Cuando:    intenta editar una LDS con id inexistente
  Entonces:  el sistema retorna HTTP 404 Not Found
         Y:  NO se guarda log de auditoría
```

**Edge Cases**

```gherkin
CRITERIO-1.8: Sin cambios detectados (usuario guarda sin modificar)
  Dado que:  un administrador abre el formulario de edición de LDS
         Y:  NO modifica ningún campo
  Cuando:    presiona el botón "Guardar"
  Entonces:  el sistema detecta que changeDetail está vacío
         Y:  NO genera log de auditoría
         Y:  retorna HTTP 200 con mensaje "Sin cambios detectados"
```

```gherkin
CRITERIO-1.9: Cambios en URLs de imágenes y videos
  Dado que:  una LDS con imagen existente
  Cuando:    el admin cambia la imagen
  Entonces:  el log incluye el cambio de URL con previousValue y currentValue
```

### Reglas de Negocio

1. **Permiso obligatorio**: Solo usuarios con rol `ADMIN` o permisos explícitos de edición de LDS pueden modificar una LDS.
2. **Auditoría completa**: Todos los cambios en campos auditables deben quedar registrados sin excepción.
3. **Solo cambios reales**: El log debe contener ÚNICAMENTE los campos que cambiaron de valor.
4. **Timestamp UTC**: Todas las fechas se almacenan en UTC millis usando `Clock.systemUTC().millis()` (consistente con código existente).
5. **Formato de campo**: Usar `FieldChange` existente con `previousValue` y `currentValue` (ya definido en `model/bussiness/FieldChange.java`).
6. **Valores nulos**: Si un campo anterior no existía, `previousValue` = `null`. Si se elimina, `currentValue` = `null`.
7. **Nombre de usuario**: Se resuelve en ms-audit invocando `ms-customers` via REST (patrón ya existente en `SolutionLineDataCommandUseCase`).
8. **Asincronía**: El envío del log de auditoría se realiza de forma asíncrona vía RabbitMQ para no bloquear la operación de edición.
9. **Canal MQ**: Reutilizar el canal existente `queueSolutionLineLog` con routing key `compensar.audit.1.command.solutionLineLogData` — NO crear un canal nuevo. `LdsEditionChangeLogListener` (actualmente vacío) se descarta en favor del `SolutionLineDataListener` ya operativo.
10. **Idempotencia**: Responsabilidad del consumer en ms-audit — si ya existe un log con mismo `solutionLineId`, `modificationUser` y `modificationDate`, descartar silenciosamente.
11. **Estado del ciclo de vida**: Incluir campo `status` de la LDS en el changeDetail si cambia.
12. **Patrón Strategy**: Implementar `UpdateSolutionLinesAuditStrategy` (archivo vacío existente) siguiendo el mismo patrón de `CreationSolutionLinesAuditStrategy`. NO crear clase utilitaria `ChangeDetector` separada.

---

## 2. DISEÑO

### Modelos de Datos

#### Entidades afectadas

| Entidad | Almacén | Cambios | Microservicio | Descripción |
|---------|---------|---------|---------------|-------------|
| `SolutionLineChangeLogDocument` | MongoDB `solutionLineChangeLogDocument` | sin cambios en esquema | ms-audit | Documento ya existente para logs de LDS. Añadir campo `solutionLineName` si no existe |
| `SolutionLineAuditContext` | (DTO en memoria) | **ampliar** | ms-command-experience | Añadir campos de Visualización/Asignación |
| `SolutionLineFieldsChangeEnum` | (Enum) | **ampliar** | ms-command-experience | Añadir campos de todos los tabs |
| `SolutionLineDisplay` | MongoDB `solution_line_display` | sin cambios | ms-command-experience | Modelo existente de configuración visual |
| `SolutionLines` | MongoDB `solution_lines` | sin cambios | ms-command-experience | Modelo existente de LDS |
| `SolutionLineDescription` | MongoDB | sin cambios | ms-admin-wellnesspath | Modelo de Tab Descripción (ya auditado parcialmente) |

---
