---
id: Spec-Login-Frontend
type: spec
title: "Implementación frontend del login OAuth"
status: in_progress
owner: "@frontend-team"
tags: [auth, frontend]
relations:
  depends_on: [Spec-AuthAPI]
  references: [Dict-Session]
updated_at: 2026-05-18
---

# Spec-Login-Frontend

## Pantallas
- `/login`: muestra los dos botones — "Continuar con email" y "Continuar con Google".
- `/auth/callback`: pantalla intermedia que canjea el `code` con el backend y redirige al destino.

## PKCE
- Genera `code_verifier` aleatorio de 64 chars al iniciar el flujo.
- Guarda `code_verifier` en `sessionStorage`; tras éxito, lo borra.
- Envía `code_challenge` (SHA-256, base64url) a Google.

## Manejo de tokens
- Access token en memoria (`useAuthStore`), nunca en `localStorage`.
- Refresh token en cookie HttpOnly + SameSite=Strict.
