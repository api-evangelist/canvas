# Canvas (canvas)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Canvas is Instructure's open-source learning management system (LMS) used by K-12, higher education, and corporate training organizations to deliver courses, assessments, and learner communication. Canvas exposes a comprehensive REST API and a GraphQL endpoint for reading and modifying courses, assignments, quizzes, grades, users, enrollments, content, and account administration, and it integrates with external tools through LTI, Caliper, and live event streams.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/canvas/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/canvas/refs/heads/main/apis.yml)

## Scope

- **Type:** Index
- **Position:** Consumer
- **Access:** 3rd-Party

## Tags

- Education
- EdTech
- GraphQL
- Learning Management System
- LMS
- LTI
- Open Source
- REST

## Timestamps

- **Created:** 2025-01-14
- **Modified:** 2026-04-23

## APIs

### Canvas LMS REST API

The Canvas LMS REST API provides programmatic access to courses, assignments, quizzes, grades, users, enrollments, accounts, discussions, files, modules, rubrics, submissions, SIS imports, and account administration. It uses OAuth 2.0 access tokens and returns JSON with ISO 8601 timestamps, supporting pagination, request throttling, masquerading, and compound documents.

- **Human URL:** [https://canvas.instructure.com/doc/api/](https://canvas.instructure.com/doc/api/)

#### Tags

- Education
- LMS
- REST

#### Properties

- [Documentation](https://canvas.instructure.com/doc/api/)
- [Authentication](https://canvas.instructure.com/doc/api/file.oauth.html)
- [Pagination](https://canvas.instructure.com/doc/api/file.pagination.html)
- [GitHub Repository](https://github.com/instructure/canvas-lms)
- [Postman Collection](collections/canvas.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/canvas.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Canvas LMS GraphQL API

The Canvas LMS GraphQL API is an alternative to the REST API that lets clients request exactly the fields they need across Canvas resources in a single request. It is well suited for dashboards and aggregated views that otherwise require many REST round-trips.

- **Human URL:** [https://canvas.instructure.com/doc/api/file.graphql.html](https://canvas.instructure.com/doc/api/file.graphql.html)

#### Tags

- Education
- GraphQL
- LMS

#### Properties

- [Documentation](https://canvas.instructure.com/doc/api/file.graphql.html)
- [Postman Collection](collections/canvas.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/canvas.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Canvas LTI Integrations

Canvas supports Learning Tools Interoperability (LTI 1.1 and LTI 1.3 / Advantage) for embedding external tools, assignments, and content into courses with deep linking, grade passback, and names-and-roles service.

- **Human URL:** [https://canvas.instructure.com/doc/api/file.tools_intro.html](https://canvas.instructure.com/doc/api/file.tools_intro.html)

#### Tags

- Education
- LMS
- LTI

#### Properties

- [Documentation](https://canvas.instructure.com/doc/api/file.tools_intro.html)
- [Postman Collection](collections/canvas.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/canvas.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [LinkedIn](https://www.linkedin.com/showcase/canvaslms)
- [Website](https://www.instructure.com/canvas)
- [Documentation](https://canvas.instructure.com/doc/api/)
- [GitHub Organization](https://github.com/instructure)
- [GitHub Repository](https://github.com/instructure/canvas-lms)
- [Status Page](https://status.instructure.com/)
- [Community](https://community.canvaslms.com/)
- [Privacy Policy](https://www.instructure.com/policies/privacy)
- [Terms of Service](https://www.instructure.com/policies/product-acceptable-use)
- [Integrations](https://www.instructure.com/partners)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
