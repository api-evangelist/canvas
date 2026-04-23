# Canvas (canvas)
Canvas is Instructure's open-source learning management system (LMS) used by K-12, higher education, and corporate training organizations to deliver courses, assessments, and learner communication. Canvas exposes a comprehensive REST API and a GraphQL endpoint for reading and modifying courses, assignments, quizzes, grades, users, enrollments, content, and account administration, and it integrates with external tools through LTI, Caliper, and live event streams.

**URL:** [Visit APIs.json URL](https://raw.githubusercontent.com/api-evangelist/canvas/refs/heads/main/apis.yml)

## Scope

- **Type:** Index
- **x-type:** company
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

## APIs

### Canvas LMS REST API
The Canvas LMS REST API provides programmatic access to courses, assignments, quizzes, grades, users, enrollments, accounts, discussions, files, modules, rubrics, submissions, SIS imports, and account administration. It uses OAuth 2.0 access tokens and returns JSON with ISO 8601 timestamps, supporting pagination, request throttling, masquerading, and compound documents.

**Human URL:** [https://canvas.instructure.com/doc/api/](https://canvas.instructure.com/doc/api/)

#### Properties

- [Documentation](https://canvas.instructure.com/doc/api/)
- [Authentication](https://canvas.instructure.com/doc/api/file.oauth.html)
- [Pagination](https://canvas.instructure.com/doc/api/file.pagination.html)
- [GitHub Repository](https://github.com/instructure/canvas-lms)

#### Features

- OAuth 2.0 authentication with access tokens
- JSON responses with ISO 8601 timestamps
- 64-bit integer IDs and string ID support
- Pagination for large result sets
- Masquerading for acting on behalf of users
- File upload workflows
- SIS imports and roster sync
- Live event streams via Canvas Data services
- Caliper event format compatibility
- xAPI statement support

#### Use Cases

- Building custom student dashboards or mobile apps
- Automating course provisioning and enrollment via SIS imports
- Syncing grades and assignments to external gradebooks
- Analytics and learning-event data pipelines
- Integrating third-party tools through LTI
- Institutional reporting and audit-log extraction

### Canvas LMS GraphQL API
The Canvas LMS GraphQL API is an alternative to the REST API that lets clients request exactly the fields they need across Canvas resources in a single request.

**Human URL:** [https://canvas.instructure.com/doc/api/file.graphql.html](https://canvas.instructure.com/doc/api/file.graphql.html)

### Canvas LTI Integrations
Canvas supports Learning Tools Interoperability (LTI 1.1 and LTI 1.3 / Advantage) for embedding external tools, assignments, and content into courses with deep linking, grade passback, and names-and-roles service.

**Human URL:** [https://canvas.instructure.com/doc/api/file.tools_intro.html](https://canvas.instructure.com/doc/api/file.tools_intro.html)

## Common Properties

- [Website](https://www.instructure.com/canvas)
- [Documentation](https://canvas.instructure.com/doc/api/)
- [GitHub Organization](https://github.com/instructure)
- [Status](https://status.instructure.com/)
- [Community](https://community.canvaslms.com/)
- [Privacy Policy](https://www.instructure.com/policies/privacy)

## Timestamps

- **Created:** 2025-01-14
- **Modified:** 2026-04-23

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
