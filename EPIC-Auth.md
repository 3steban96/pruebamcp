---
id: EPIC-Auth
type: epic
title: "Plataforma de Autenticación"
status: in_progress
owner: "@product"
tags: [auth, platform]
relations:
  children: [HU-01, HU-04]
updated_at: 2026-05-01
---

# EPIC: Plataforma de Autenticación

Iniciativa transversal para unificar el inicio de sesión de todos los productos
internos bajo OAuth 2.1 + PKCE, eliminando los flujos legacy basados en cookies
de sesión persistentes en backend.

## Objetivos
- Reducir el time-to-login en 40%.
- Cumplir con los requisitos de retención de la nueva política de seguridad.
- Permitir SSO con proveedores externos (Google, Microsoft).
