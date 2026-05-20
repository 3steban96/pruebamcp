---
id: Dict-Session
type: dictionary
title: "Diccionario: Session"
status: approved
owner: "@data"
tags: [dictionary, model]
relations: {}
updated_at: 2026-04-22
---

# Dict-Session

| Campo                 | Tipo       | Notas                                                |
|-----------------------|------------|------------------------------------------------------|
| `id`                  | UUID v4    | PK.                                                  |
| `user_id`             | UUID v4    | FK → User.                                           |
| `refresh_token_hash`  | string     | SHA-256 del refresh token. Nunca se guarda el token. |
| `created_at`          | timestamp  | UTC.                                                 |
| `last_used_at`        | timestamp  | UTC; actualizado en cada refresh.                    |
| `revoked_at`          | timestamp  | `NULL` mientras la sesión esté activa.               |
| `user_agent`          | string     | Para auditoría.                                      |
| `ip`                  | string     | Para auditoría.                                      |
