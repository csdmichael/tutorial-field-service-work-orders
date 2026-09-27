# Delivery plan — Tutorial Field service Work Orders

Sprints are two weeks. Each sprint closes with a demo and an approval gate.

| Sprint | Focus | Exit criteria |
| --- | --- | --- |
| Sprint 1 | Foundation: repo, pipelines, schema | CI green, API deployed |
| Sprint 2 | Core scope | Approved user stories delivered |
| Sprint 3 | Hardening and release | Tests pass, release gate approved |

## Approved scope

- Scope and lifecycle expectations draw from the Requirements Agent output, which in turn is based on the project record and the SharePoint requirements location (`tutorial-field-service-work-orders-requirements.md`). The source file was listed but its text was unavailable, so this proposal treats the requirements summary as authoritative and flags gaps as assumptions for review.[1]
- No UX mockups were provided; screens, navigation, and layout will follow the “UX Mockups (Derived)” section of the requirements artifact once it is accessible. Pending visibility, the plan assumes a mobile-first Ionic experience with the flows enumerated in the requirements summary.[1]
- **Screens (per derived UX):** Work Order List, Work Order Detail (asset + tasks), Diagnosis Entry, Parts Log, Labour Log, Review & Sign-off, and Completion Confirmation. Navigation uses Ionic tabs or a side menu to match the derived UX flow.
- **State management:** Angular services hold the active work order, diagnosis draft, and offline cache metadata; data hydration happens via FastAPI endpoints.
- **Offline-first hooks:** Introduce a syncing service that reads tunable retry/timeout values from `/config/mobile.json` and queues create/update payloads.
- **Validation & accessibility:** Reuse Ionic components with form-level and field-level validation messages derived from config so copy can be localized later.
- **Modules:** `work_orders`, `diagnostics`, `parts`, `labour`, `signoff`, `sync`. Each module exposes CRUD-ish endpoints that align with the UI workflow.
- **Integration layer boundary:** Upstream dispatch/EAM data is fetched or posted via adapter classes reading endpoint URLs, API versions, and timeouts from `/config/api.json`—no literals embedded.
- **Domain services:** Implement transaction-safe services encapsulating PostgreSQL access via SQLAlchemy, enforcing idempotent updates on work-order completion.
- **Security & auth:** Token verification middleware (exact mechanism deferred to integration spec) with role checks scoped to technician permissions; secrets are pulled from environment variables only.
- **Schema:** Tables for `work_order`, `asset_snapshot`, `diagnosis_note`, `part_usage`, `labour_entry`, `signoff_record`, and `sync_checkpoint`. All table names derive from entity names in the requirements summary to keep terminology aligned.[1]
- **Auditing:** Include `created_at`, `updated_at`, and `source` fields for traceability.
