---
name: Load or update rosters in bulk with a Canvas SIS import
description: Upload SIS CSVs, poll the import to completion, read the error list, and roll back if it
  went wrong.
api: openapi/canvas-lms-openapi.yml
operations:
- import_sis_data
- get_sis_import_status
- get_sis_import_list
- get_sis_import_error_list_sis_imports
- abort_sis_import
- restore_workflow_states_of_sis_imported_items
- get_current_importing_sis_import
generated: '2026-09-05'
method: generated
source: openapi/canvas-lms-openapi.yml + conventions/canvas-conventions.yml
---

# Load or update rosters in bulk with a Canvas SIS import

SIS Import is the right tool whenever you are moving more than a handful of users, courses, sections or
enrolments. It is also the **only** part of the Canvas API with a rehearsal-and-rollback story.

## Steps

1. **Check nothing else is running.** `get_current_importing_sis_import` —
   `GET /api/v1/accounts/{account_id}/sis_imports/importing`. Overlapping imports fight each other.
2. **Upload.** `import_sis_data` — `POST /api/v1/accounts/{account_id}/sis_imports`.
   Send the CSV (or a zip of CSVs) as the request body with the right `Content-Type`, or `attachment` as a
   multipart file. Useful parameters: `import_type=instructure_csv`, `batch_mode=true` with
   `batch_mode_term_id` (anything in that term absent from the file is deleted — powerful and dangerous),
   `diffing_data_set_identifier` (Canvas diffs against the previous file with the same identifier and applies
   only the changes), and `override_sis_stickiness`.
   **Use `diffing_data_set_identifier` for any recurring sync.** It is the closest thing Canvas has to
   idempotent bulk writes.
3. **Poll.** `get_sis_import_status` — `GET /api/v1/accounts/{account_id}/sis_imports/{id}` until
   `workflow_state` is `imported`, `imported_with_messages`, `failed` or `failed_with_messages`.
   Read `progress`, `processing_warnings` and `processing_errors`.
4. **Read the errors properly.** `get_sis_import_error_list_sis_imports` —
   `GET /api/v1/accounts/{account_id}/sis_imports/{id}/errors`. Page it; the status object truncates.
5. **History.** `get_sis_import_list` — `GET /api/v1/accounts/{account_id}/sis_imports` with `created_since`.

## Abort and roll back

- **While it is running:** `abort_sis_import` — `PUT /api/v1/accounts/{account_id}/sis_imports/{id}/abort`.
- **After it finished:** `restore_workflow_states_of_sis_imported_items` —
  `PUT /api/v1/accounts/{account_id}/sis_imports/{id}/restore_states`, which puts the objects the batch touched
  back to their prior workflow states. Instructure publishes **no time limit** on this; do not tell a user one
  exists.

## Retry safety

Re-posting the same CSV without `diffing_data_set_identifier` re-applies every row. With a diffing identifier,
a repeat of an identical file is a no-op. Prefer the identifier.

## Ground rules for every Canvas call

- **Host is a variable.** Canvas is multi-tenant and self-hostable. The base is `https://<canvas-host>/api/v1`,
  where `<canvas-host>` is the institution's own domain. `canvas.instructure.com` is only Instructure's
  Free-for-Teacher instance. Never hard-code it. Beta and test siblings are `<institution>.beta.instructure.com`
  and `<institution>.test.instructure.com`.
- **Auth.** `Authorization: Bearer <token>`. Tokens from developer keys issued after Oct 2015 expire after
  1 hour — refresh with `POST /login/oauth2/token` (`grant_type=refresh_token`). A `401` **with** a
  `WWW-Authenticate` header means the token is bad or was issued on a different Canvas host; a `401`
  **without** it means the user lacks permission — do not re-run the OAuth flow for that one.
- **Scopes are endpoints.** Canvas has no named scopes. A scope is literally `url:<VERB>|/api/v1/<path>`,
  e.g. `url:GET|/api/v1/courses`. If the developer key has "Enforce Scopes" on, every operation below must be
  granted individually. See `scopes/canvas-scopes.yml`.
- **Pagination is the `Link` header, not a page number.** Default page size is 10. Set `per_page`, then follow
  `rel="next"` from the `Link` header. Treat those URLs as opaque; parse the header case-insensitively.
  Never stop at the first page.
- **Throttling is cost-based.** Every response carries `X-Request-Cost`; when throttling applies you also get
  `X-Rate-Limit-Remaining`. Exhaustion returns `429` (older/self-hosted Canvas returns `403` for the same
  condition — handle both). There is no `Retry-After`. Keep concurrency at 1: parallel requests take an extra
  pre-flight penalty.
- **THERE IS NO IDEMPOTENCY KEY.** Canvas documents no replay protection on any of its 566 mutating operations.
  A retried `POST` creates a second object. Before retrying a write, re-read to see whether the first one landed,
  or address the object by a caller-controlled SIS id (`sis_course_id:`, `sis_user_id:`, `sis_section_id:`).
- **Reversal exists; windows do not.** Canvas ships restore/conclude/cancel operations but publishes no time
  window for any of them. Do not promise a user that something can be undone "within N days" — nothing states that.
- **Bodies.** Form-encoded or JSON, with bracket nesting: `course[name]`, `assignment[submission_types][]`.
