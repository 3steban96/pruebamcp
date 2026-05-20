---
id: Dict-User
type: dictionary
title: "Diccionario: User"
status: approved
owner: "@data"
tags: [dictionary, model]
relations: {}
updated_at: 2026-02-01
---

# Dict-User

| Campo            | Tipo       | Notas                                                  |
|------------------|------------|--------------------------------------------------------|
| `id`             | UUID v4    | PK; generado en backend al alta.                       |
| `email`          | string     | Único, lowercase. Validado con regex RFC 5322.         |
| `password_hash`  | string     | Argon2id. `NULL` si el usuario solo usa OAuth.         |
| `display_name`   | string     | Opcional.                                              |
| `email_verified` | boolean    | Pasa a `true` tras click en correo de verificación.    |
| `created_at`     | timestamp  | UTC.                                                   |
| `disabled`       | boolean    | Soft-delete; bloquea login si `true`.                  |
