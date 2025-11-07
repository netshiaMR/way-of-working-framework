# ABSA PAIA Management System — Release Plan 2026.01.00

**Target release window:** March 2026 (exact date TBC)

This plan captures the scope, delivery approach, and architectural alignment for the 2026.01.00 release. It extends the learnings from the `paia-poc-vite` React proof of concept into a production-ready Angular 20 experience backed by a hardened services layer, ABSA-compliant styling, and infrastructure as code that spans application, data, and messaging workloads.

---

## 1. Release Goals

- Deliver the first production iteration of the PAIA/POPIA case management platform on the Angular stack, mirroring the UX proven in the React POC while meeting ABSA design and accessibility standards.
- Stand up a secure, auditable backend services tier that supports the end-to-end case lifecycle, user management, notifications, and reporting.
- Provide Terraform-managed AWS infrastructure (network, compute, data, observability) and managed SMTP services aligned with ABSA governance controls.
- Establish an architecture blueprint and governance checklist suitable for the AWS account request process and solution design sign-off.

## 2. High-Level Timeline

| Phase | Timeframe (indicative) | Key Outcomes |
| --- | --- | --- |
| **Discovery & Architecture** | Nov–Dec 2025 | Confirm requirements, document AWS governance artefacts, finalise colour and accessibility specs. |
| **Backend Foundations** | Jan 2026 (Weeks 1–3) | Harden domain model, build core APIs, integrate persistence & messaging, complete automated testing baseline. |
| **Frontend Feature Build** | Jan–Feb 2026 (Weeks 2–6) | Port React POC flows to Angular, refine ABSA UI theme, add dashboard, cases, case detail, IAM, notifications, workflow, profile. |
| **Infrastructure & Terraform** | Jan–Feb 2026 (Weeks 3–7) | Commit Terraform modules, run environment provisioning (dev/test/UAT), wire CI/CD. |
| **Integration & Hardening** | Feb 2026 (Weeks 6–8) | End-to-end testing, performance benchmarks, security review, DR and backup validation. |
| **Release Readiness** | Late Feb–Mar 2026 | Regression, training artefacts, launch checklist, go/no-go. |

## 3. Backend Delivery Scope

Leverage the existing `paia-backend-service` Spring Boot baseline and expand into a modular service layer.

**Technologies:** Java 21, Spring Boot 3.x, Spring Data, Spring Security, Spring Cloud (OpenFeign, Config), PostgreSQL, Redis, Kafka/SNS/SQS, OpenAPI 3, Maven/Gradle, JUnit 5.

1. **Domain & Data**
   - Consolidate case, SLA, requester, business-unit entities with audit columns.
   - Implement multi-tenant data partitions where required by ABSA privacy policy.
   - Introduce change-data-capture hooks (Debezium or native CDC) for analytics.

2. **APIs & Services**
   - `CaseService`: CRUD, state transitions, SLA tracking, attachments metadata.
   - `WorkflowService`: templated workflow steps, escalations, approval gates, mirroring POC flow logic.
   - `IAMService`: role-based access control (OPS Admin, Business Unit Officer, Reviewer, Viewer), ABSA SSO integration stub.
   - `NotificationService`: email dispatch (via SMTP provider), in-app notifications feed, webhook support for future integrations.
   - `ReportingService`: aggregated dashboards, export endpoints (CSV/JSON), data feeds for BI tooling.

3. **Eventing & Automation**
   - Publish events (`case.created`, `sla.breach`, `assignment.change`) to Amazon EventBridge or SNS.
   - Process asynchronous work via AWS SQS / Lambda consumers (document generation, reminder emails).

4. **Security & Compliance**
   - OAuth2 / OpenID Connect integration (ABSA Identity). Until live, support feature flag + mock provider.
   - Full audit logging (API gateway, service-layer, DB triggers).
   - Data encryption at rest (KMS-managed) and in transit (TLS 1.2+).

5. **Quality Engineering**
   - Expand unit, integration, contract tests (Spring Cloud Contract or Pact).
   - Load/perf tests targeting critical flows (case creation, status transition, dashboard).
   - Static analysis (SonarQube), security scans (Snyk, OWASP dependency check).

## 4. Frontend Delivery Scope

Build upon the current Angular 20 application in `frontend/`, guided by the React POC layouts and component structure.

**Technologies:** Angular 20, Angular Material, Tailwind CSS 3, RxJS/Signals, Vite SSR, Jest, Cypress/Playwright, D3/Chart.js, Storybook (optional).

1. **UI & Theme Alignment**
   - Finalise ABSA design tokens (colour palette, typography, spacing) in Tailwind + global SCSS.
   - Replace temporary styling with theme-aware components (buttons, cards, tables, forms, charts).
   - Validate accessibility (contrast ratios, keyboard navigation, ARIA semantics).

2. **Feature Completeness**
   - **Authentication**: login, password reset (placeholder until backend ready), SSO handoff.
   - **Dashboard**: live stats, activity feeds, system status with real data.
   - **Cases**: filters, pagination, inline actions, case creation wizard, attachments, SLA badges, export.
   - **Case Detail**: timeline, tabs (attachments, comments, workflow history), document preview.
   - **Queue & Workflow Guide**: replicating POC flows, dynamic data from backend.
   - **IAM & Notifications**: user management, role assignment, notification centre.
   - **Reports**: charts (Angular Material + D3/Chart.js), scheduled exports integration.

3. **Shared Components & State**
   - Port reusable UI primitives from React POC (`StatusBadge`, tables, modals) into standalone Angular components.
   - Centralise app state (NgRx or Angular Signals-based store) for cases, auth, notifications.
   - Implement feature toggles for phased rollouts.

4. **Quality & Tooling**
   - Component/unit tests (Jest) and e2e (Cypress or Playwright).
   - Lighthouse accessibility and performance budgets.
   - Storybook or Angular DevTools previews for design sign-off.

## 5. ABSA Colour & Brand Alignment

- Adopt official ABSA brand guidelines: primary (#a6093d), secondary accent palette, neutral greys.
- Ensure consistent usage across Angular Material overrides, Tailwind utilities, SVG/PNG assets.
- Provide light/dark mode readiness (if required) via CSS custom properties defined in `styles.scss`.
- Create a brand QA checklist (logo usage, favicon, typography, spacing) to be signed off by design authority.

## 6. Terraform Infrastructure Stack

Split Terraform into reusable modules stored under `infrastructure/terraform` (new).

1. **Core Modules**
   - `vpc`: VPC, subnets (public/private), NAT gateways, routing, security groups.
   - `rds`: PostgreSQL (multi-AZ, automated backups, encryption).
   - `ecs_service` or `eks_service`: container orchestrations for backend (decision in architecture review).
   - `s3_static_site`: Angular frontend hosting (S3 + CloudFront) with WAF + Shield Advanced.
   - `event_bridge`: rules & targets for domain events.
   - `iam_baseline`: roles, instance profiles, least-privilege policies, KMS keys.

2. **Environment Composition**
   - `environments/dev`, `test`, `uat`, `prod` stacks referencing modules with env-specific variables.
   - Remote state via AWS S3 + DynamoDB lock, parameterised by AWS account ID.

3. **Frontend Hosting**
   - S3 buckets (versioned) + CloudFront distribution (custom domain, ACM cert, security headers).
   - Route53 records for web app (`paia.absa.co.za`, etc.).

4. **Backend Hosting**
   - Options: ECS Fargate (API services) + App Mesh, or EKS (if container standard). Document trade-offs in architecture pack.
   - Auto scaling policies, CloudWatch metrics, AWS X-Ray tracing.

5. **SMTP / Email Service**
   - Terraform module for Amazon SES (production & sandbox), domain identity verification, DKIM, DMARC records.
   - Secret management for SMTP credentials (AWS Secrets Manager) with rotation.

6. **CI/CD Integration**
   - GitHub Actions or AWS CodePipeline automation for Terraform plan/apply with manual approval gates for prod.

## 7. AWS Architecture & Governance Artefacts

Prepare the following documentation for AWS account request and Architecture Review Board (ARB):

1. **Solution Overview Deck**
   - Context, user journeys, system components, data flow, integration points.
   - High-level AWS diagram: Route53 → CloudFront → S3 (frontend), ALB/API Gateway → ECS/EKS (backend) → RDS, EventBridge, SQS, SES, CloudWatch, Secrets Manager.
   - Security posture: identity, encryption, network segmentation, logging, monitoring, DR.

2. **Governance Checklist**
   - ABSA Cloud Policy alignment (data residency, classification, third-party use).
   - Logging/monitoring (CloudTrail, GuardDuty, Security Hub, SIEM integration).
   - Backup & DR (RDS snapshots, cross-region replication, S3 versioning, infra as code restores).
   - Compliance artifacts (PCI/POPIA mapping, vulnerability management cadence).

3. **Account Request Package**
   - Business justification, cost estimates, tagging strategy, owner contacts.
   - Environment strategy (shared vs dedicated accounts) + landing zone integration.

4. **Operations Runbook**
   - Incident response flows, on-call rotation, escalation to ABSA CloudOps.
   - Deployment strategy (blue/green or canary), rollback, maintenance windows.

## 8. Non-Functional Requirements

- **Performance:** P95 API responses < 400 ms under expected load (500 concurrent users), dashboard loads < 3 s.
- **Availability:** 99.5% for production; document DR strategy (multi-AZ, RTO < 1 hr, RPO < 15 min).
- **Security:** Zero critical/high vulnerabilities prior to launch; mandatory penetration test sign-off.
- **Observability:** Structured logging (JSON), distributed tracing, metrics dashboards (CloudWatch, Grafana), alerting (PagerDuty/MS Teams).
- **Data Protection:** Encryption, data retention policies, GDPR/POPIA consent and deletion workflows.

## 9. Deliverables

1. Updated Angular frontend with ABSA-compliant theme and full case lifecycle flows.
2. Production-grade backend microservices (Spring Boot) with documented APIs (OpenAPI/Swagger).
3. Terraform repository covering networking, compute, storage, security, and SES.
4. AWS architecture presentation deck + governance checklist + operations runbook.
5. Test artefacts: unit/integration test suites, performance reports, security scan results.
6. Release notes and deployment playbook for 2026.01.00.

## 10. Dependencies & Risks

- **Identity Integration:** Awaiting ABSA IAM team confirmation for SSO endpoints; mitigate via feature flag + fallback auth.
- **Data Migration:** Legacy case data sources need mapping; plan pilot migration during UAT.
- **Third-Party Approvals:** SES sending limits, domain verification may add lead time; initiate early.
- **Resource Availability:** Cross-team coordination (backend, DevOps, security) required; maintain RACI matrix.
- **Compliance Review:** ARB scheduling & findings could adjust scope; keep contingency in timeline.

## 11. Next Steps

1. Schedule architecture & governance workshops (Nov 2025).
2. Stand up Terraform repo skeleton and environments (dev/test) in sandbox account.
3. Finalise UI design tokens with ABSA design authority; document in Figma + CSS variables.
4. Prioritise backend API contracts and produce OpenAPI specs for front-end consumption.
5. Define CI/CD pipelines (GitHub Actions) for frontend, backend, Terraform, including quality gates.
6. Prepare detailed sprint backlog for Jan–Mar 2026 aligned with this release scope.

---

**Revision history**

| Version | Date | Author | Notes |
| --- | --- | --- | --- |
| 2026.01.00-draft | 2025-11-07 | GPT-5 Codex (assistant) | Initial release plan drafted from React POC learnings and current Angular implementation. |


