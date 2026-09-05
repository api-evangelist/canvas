---
name: Provision a Canvas course and enrol people into it
description: Create a course in an account, add a section, enrol teachers and students, then verify the
  roster.
api: openapi/canvas-lms-openapi.yml
operations:
- create_new_course
- create_course_section
- enroll_user_courses
- list_users_in_course_users
- update_course_settings
- delete_conclude_course
generated: '2026-09-05'
method: generated
source: openapi/canvas-lms-openapi.yml + conventions/canvas-conventions.yml
---

# Provision a Canvas course and enrol people into it

## Steps

1. **Create the course.** `create_new_course` — `POST /api/v1/accounts/{account_id}/courses`.
   Send `course[name]`, `course[course_code]`, and `course[sis_course_id]` if you have one.
   **Set `course[sis_course_id]` if you possibly can** — it is the only caller-controlled key Canvas gives you,
   and it is what makes step 1 safely retryable (see the idempotency rule below).
2. **Add a section** (optional). `create_course_section` — `POST /api/v1/courses/{course_id}/sections`,
   `course_section[name]`, `course_section[sis_section_id]`.
3. **Enrol each person.** `enroll_user_courses` — `POST /api/v1/courses/{course_id}/enrollments`.
   `enrollment[user_id]`, `enrollment[type]` (`StudentEnrollment` | `TeacherEnrollment` | `TaEnrollment` |
   `ObserverEnrollment` | `DesignerEnrollment`), `enrollment[enrollment_state]` (`active` skips the invitation),
   and `enrollment[course_section_id]` when you made a section.
4. **Verify the roster.** `list_users_in_course_users` — `GET /api/v1/courses/{course_id}/users`,
   `enrollment_type[]=student`, `per_page=100`, and follow every `rel="next"` page before reporting a count.
5. **Publish.** `update_course_settings` / the course update operation with `course[event]=offer` when the
   course should become available to students.

## Retry safety

There is no idempotency key. If `create_new_course` times out, **do not blindly repeat it** — you will create a
second course. Instead re-read with `GET /api/v1/accounts/{account_id}/courses?search_term=<name>` (or address
`sis_course_id:<your id>` directly) and only create if it is genuinely absent.

## Undo

`delete_conclude_course` — `DELETE /api/v1/courses/{id}` with `event=conclude` (soft, reversible) or
`event=delete` (soft-delete). Prefer `conclude`. **Never** reach for `reset_course` as an undo: it replaces the
course with a blank shell and returns a *new* course id, and the old content is not recoverable through the API.

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
