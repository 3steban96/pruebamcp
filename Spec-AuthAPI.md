---
id: Spec-AuthAPI
type: spec
title: "Especificación de la API de autenticación"
status: approved
owner: "@anderson"
tags: [auth, backend, api]
relations:
  references: [Dict-User, Dict-Session]
updated_at: 2026-05-10
---

# Spec-AuthAPI

## Endpoints
- `POST /auth/register` — alta con email/contraseña.
- `POST /auth/login` — login tradicional.
- `POST /auth/oauth/callback` — recibe el authorization code de Google y lo intercambia por tokens.
- `POST /auth/refresh` — renueva access token vía refresh token rotativo.
- `POST /auth/logout` — invalida la sesión actual.

## Tokens
- **Access token**: JWT firmado RS256, vida 15 minutos.
- **Refresh token**: opaco, vida 30 días, rotativo (un solo uso).

## Errores
Todos los endpoints siguen el formato `{ "error": "<code>", "message": "<human>" }`
con códigos: `invalid_credentials`, `account_locked`, `oauth_failed`, `token_expired`.
