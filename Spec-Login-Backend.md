---
id: Spec-Login-Backend
type: spec
title: "Implementación backend del login OAuth"
status: in_progress
owner: "@anderson"
tags: [auth, backend]
relations:
  depends_on: [Spec-AuthAPI]
  references: [Dict-Session]
updated_at: 2026-05-18
---

# Spec-Login-Backend

## Componentes
- `OAuthController`: maneja `/auth/oauth/callback`.
- `GoogleTokenVerifier`: valida el `id_token` contra los JWKS de Google.
- `SessionService`: emite access + refresh tokens.

## Flujo
1. Frontend redirige a Google con `client_id`, `redirect_uri`, `code_challenge`.
2. Google redirige al frontend con `code`.
3. Frontend hace `POST /auth/oauth/callback` con `code` + `code_verifier`.
4. Backend intercambia `code` por `id_token` y crea/recupera el usuario.
5. Backend devuelve par `(access, refresh)`.

## Persistencia
- Tabla `oauth_links(user_id, provider, provider_user_id, linked_at)`.
- Tabla `sessions(refresh_token_hash, user_id, created_at, last_used_at, revoked_at)`.
