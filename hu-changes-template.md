---
title: HU Changes Template
purpose: template
id: hu-changes-template
type: dictionary
relations: {}
---

# HU: [HU-ID]
**Título**: [Nombre de la HU]

---

## 📊 Resumen Ejecutivo

| Campo | Valor |
|-------|-------|
| **Status** | DRAFT | IN_PROGRESS | IMPLEMENTED | PAUSED |
| **Iniciativa** | SWPR167 | SWPR174 |
| **Creada** | YYYY-MM-DD |
| **Última actualización** | YYYY-MM-DD HH:MM |
| **Agentes involucrados** | Spec Generator, Backend Developer, ... |

---

## 🔄 Iteraciones

### Iteración 1: [Fase/Nombre]
**Fecha**: YYYY-MM-DD HH:MM  
**Agente**: [Nombre del agente]  
**Estado**: ✅ COMPLETADA | ⚙️ EN_PROGRESO | ⏸️ PAUSED

**Resumen de cambios**:
- Cambio 1
- Cambio 2
- Cambio 3

**Archivos modificados** (6 archivos):
```
src/main/java/com/compensar/memberships/MembershipsController.java [CREATED]
src/main/java/com/compensar/memberships/MembershipsService.java [CREATED]
src/main/resources/db/migration/V1_001__create_memberships_table.sql [CREATED]
src/test/java/com/compensar/memberships/MembershipsControllerTest.java [CREATED]
.github/specs/SWPR167-001.spec.md [MODIFIED]
pom.xml [MODIFIED]
```

**Link a spec/PR**: [SWPR167-001.spec.md](../specs/SWPR167-001.spec.md)

---

(…rest of file…)
