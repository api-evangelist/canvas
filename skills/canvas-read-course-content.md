---
name: Read a course's structure and content without changing anything
description: Traverse modules, pages, assignments, files and announcements read-only — the safe shape
  for an agent answering questions about a course.
api: openapi/canvas-lms-openapi.yml
operations:
- get_single_course_courses
- list_modules
- list_module_items
- list_pages_courses
- show_page_courses
- list_assignments_assignments
- list_files_courses
- list_announcements
- list_discussion_topics_courses
generated: '2026-09-05'
method: generated
source: openapi/canvas-lms-openapi.yml + conventions/canvas-conventions.yml
---

# Read a course's structure and content without changing anything

Every operation here is a `GET`. Nothing in this skill mutates Canvas, so none of the idempotency or
reversibility caveats apply — but the pagination and throttling rules very much do.

## Steps

1. **The course.** `get_single_course_courses` — `GET /api/v1/courses/{id}`.
   `include[]=syllabus_body`, `include[]=term`, `include[]=teachers`, `include[]=course_progress`.
2. **The spine.** `list_modules` — `GET /api/v1/courses/{course_id}/modules`, then
   `list_module_items` — `GET /api/v1/courses/{course_id}/modules/{module_id}/items`
   with `include[]=content_details`. Module items are the ordered, student-facing path through the course;
   prefer them over a flat listing when a human asks "what do I do next".
3. **Pages.** `list_pages_courses` — `GET /api/v1/courses/{course_id}/pages`, then
   `show_page_courses` — `GET /api/v1/courses/{course_id}/pages/{url_or_id}` for `body`.
4. **Assignments.** `list_assignments_assignments` — `GET /api/v1/courses/{course_id}/assignments`,
   `order_by=due_at`, `bucket=upcoming` for what is live.
5. **Files and announcements.** `list_files_courses` — `GET /api/v1/courses/{course_id}/files`;
   `list_announcements` — `GET /api/v1/announcements?context_codes[]=course_<id>`;
   `list_discussion_topics_courses` — `GET /api/v1/courses/{course_id}/discussion_topics`.

## Reading HTML bodies

Canvas annotates links inside returned HTML with `data-api-endpoint` (the API URL of the linked object) and
`data-api-returntype` (its type, wrapped in `[...]` for a list). Follow those attributes instead of guessing a
URL from the human-facing href.

## Cost

Rich `include[]` values are the expensive part — `X-Request-Cost` rises with them. Ask for `include[]` values you
will actually use; there is no way to ask for *fewer* fields on REST. If you need narrow field selection across
many objects, the GraphQL endpoint at `POST /api/graphql` exists for exactly that.

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
