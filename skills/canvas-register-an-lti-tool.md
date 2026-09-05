---
name: Register an LTI 1.3 tool in a Canvas account and deploy it
description: Create an LTI registration from a tool configuration, bind it to the account, control where
  it is available, and roll it back.
api: openapi/canvas-lms-openapi.yml
operations:
- create_lti_registration_lti_registrations
- list_lti_registrations_in_account_lti_registrations
- show_lti_registration_lti_registrations
- update_lti_registration_lti_registrations
- bind_lti_registration_to_root_account_lti_registrations
- create_lti_context_control
- bulk_create_lti_context_controls
- delete_lti_registration_lti_registrations
generated: '2026-09-05'
method: generated
source: openapi/canvas-lms-openapi.yml + conventions/canvas-conventions.yml
---

# Register an LTI 1.3 tool in a Canvas account and deploy it

Canvas is an 1EdTech LTI **platform**. This is the supported way to put a third-party tool inside a course —
not an embedded JS widget.

## Steps

1. **Create the registration.** `create_lti_registration_lti_registrations` —
   `POST /api/v1/accounts/{account_id}/lti_registrations`.
   Supply the tool's LTI 1.3 configuration (or install from a template / by client id — see
   `install_lti_registration_from_template_lti_registrations` and `show_lti_registration_via_client_id_lti_registration_by_client_id`).
2. **Inspect it.** `show_lti_registration_lti_registrations` —
   `GET /api/v1/accounts/{account_id}/lti_registrations/{id}`, and
   `list_lti_registrations_in_account_lti_registrations` to see everything already installed.
3. **Bind it to the account.** `bind_lti_registration_to_root_account_lti_registrations` —
   `POST /api/v1/accounts/{account_id}/lti_registrations/{id}/bind`. Until this happens the tool is registered
   but not available.
4. **Scope availability.** `create_lti_context_control` —
   `POST /api/v1/accounts/{account_id}/lti_registrations/{registration_id}/controls`, or
   `bulk_create_lti_context_controls` for many contexts at once. Context controls decide which sub-accounts and
   courses see the tool.
5. **Change the configuration.** `update_lti_registration_lti_registrations` —
   `PUT /api/v1/accounts/{account_id}/lti_registrations/{id}`.

## What the tool gets

Once launched, the tool talks back through LTI Advantage services rather than this REST API: Assignment and
Grade Services for grade passback, Names and Roles Provisioning Service for the roster, Deep Linking for content
selection, and the Platform Notification Service for pushed notices. Those are 1EdTech contracts, not Canvas
endpoints — see `conformance/canvas-conformance.yml`.

## Undo

`reset_lti_registration_to_defaults_lti_registrations` returns the registration's overlay to the tool's own
defaults; `delete_lti_registration_lti_registrations` removes it. Deleting a bound registration takes the tool
away from every course that was using it — check the context controls first. No restore window is published.

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
