---
name: Read submissions for an assignment and post grades and comments
description: Walk an assignment's submissions, post a score and a comment on each, and confirm what was
  written.
api: openapi/canvas-lms-openapi.yml
operations:
- list_assignments_assignments
- list_assignment_submissions_courses
- get_single_submission_courses
- grade_or_comment_on_submission_courses
- grade_or_comment_on_multiple_submissions_courses_assignments
- query_progress
generated: '2026-09-05'
method: generated
source: openapi/canvas-lms-openapi.yml + conventions/canvas-conventions.yml
---

# Read submissions for an assignment and post grades and comments

## Steps

1. **Find the assignment.** `list_assignments_assignments` — `GET /api/v1/courses/{course_id}/assignments`.
   Note `id`, `points_possible`, `grading_type` and `anonymous_grading`.
   If `anonymous_grading` is true, grade by anonymous id instead (see step 4b).
2. **List submissions.** `list_assignment_submissions_courses` —
   `GET /api/v1/courses/{course_id}/assignments/{assignment_id}/submissions`.
   Use `include[]=submission_comments`, `include[]=user`, `per_page=100`, and page through `rel="next"`.
3. **Read one.** `get_single_submission_courses` —
   `GET /api/v1/courses/{course_id}/assignments/{assignment_id}/submissions/{user_id}`.
4. **Grade one.** `grade_or_comment_on_submission_courses` —
   `PUT /api/v1/courses/{course_id}/assignments/{assignment_id}/submissions/{user_id}`.
   `submission[posted_grade]` (a number, a percentage like `"88%"`, a letter, or `pass`/`fail` depending on
   `grading_type`), `comment[text_comment]`, and `rubric_assessment[...]` when a rubric is attached.
   4b. Anonymous assignments: `grade_or_comment_on_submission_by_anonymous_id_courses`.
5. **Grade in bulk.** `grade_or_comment_on_multiple_submissions_courses_assignments` —
   `POST .../submissions/update_grades`. This returns a **Progress** object, not the graded submissions.
6. **Poll the job.** `query_progress` — `GET /api/v1/progress/{id}` until `workflow_state` is `completed` or
   `failed`. Do not assume success from the 200 on step 5.

## Retry safety

Grading is a `PUT` on a known `{assignment_id, user_id}` pair, so it is naturally safe to repeat — the same call
twice produces the same grade. **Comments are not**: `comment[text_comment]` *appends*, so a retry posts the
comment twice. Re-read with `include[]=submission_comments` before re-sending a comment.

## Undo

Re-`PUT` the previous `posted_grade` to correct a score. There is no operation to delete a submission comment
you posted through this endpoint, and no published window for anything here.

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
