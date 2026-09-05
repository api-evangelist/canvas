# `_scaffold/` — quarantined, API Evangelist-authored specs

Files here were written by API Evangelist, **not** published by the provider. They are kept for the
audit trail and are excluded from the OpenAPI walk, so they cannot be credited to Instructure.

| File | Why it is here |
|---|---|
| `canvas-openapi-scaffold.yml` | A 25-operation, hand-authored Courses subset, modelled on 2026-07-22 from the public <https://canvas.instructure.com/doc/api/courses.html> page. It said so in its own `info.description` but carried no provenance marker, and it lived in `openapi/_original/` — the verbatim-harvest archive — so the scoring engine read it as a first-party Instructure contract. Superseded 2026-09-05. |

**What replaced it.** Instructure publishes a real, complete, machine-readable contract: Swagger 1.2
at <https://canvas.instructure.com/doc/api/api-docs.json> plus 144 per-resource documents at
`https://canvas.instructure.com/doc/api/<resource>.json` — **1,118 operations, 283 models**.
Those are archived byte-for-byte in `openapi/_original/swagger-1.2/` and converted to OpenAPI 3.1 in
`openapi/canvas-lms-openapi.yml`.
