# Canvas (canvas)

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
