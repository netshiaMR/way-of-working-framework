# User Story Task Breakdown — Release 2026.01.00

**User Story:** As the PAIA programme delivery team, we need to deliver the 2026.01.00 release (target March 2026) so that ABSA stakeholders receive a production-ready PAIA/POPIA management platform aligned with brand, compliance, and operational standards.

## Epic: Architecture & Governance
- [ ] Finalise AWS solution overview deck (context, component diagrams, data flows, security posture).
- [ ] Complete ABSA cloud governance checklist (logging, monitoring, DR, compliance mapping).
- [ ] Prepare AWS account request documentation (cost estimates, tagging, owner roles, landing zone alignment).
- [ ] Draft operations runbook (incident response, on-call rotation, deployment/rollback procedures).
- [ ] Schedule and obtain Architecture Review Board approval.

## Epic: Backend Foundations (Java / Spring Boot)
- [ ] Define domain model updates (cases, SLA, requester, business unit) with audit columns.
- [ ] Implement CaseService CRUD, status transitions, and SLA calculations.
- [ ] Implement WorkflowService for templated steps, escalations, and approvals.
- [ ] Implement IAMService with roles (Ops Admin, BU Officer, Reviewer, Viewer) and SSO integration hook.
- [ ] Build NotificationService for email + in-app feeds; integrate with messaging layer.
- [ ] Build ReportingService endpoints and export utilities.
- [ ] Configure PostgreSQL schema, migrations, and CDC hooks for analytics.
- [ ] Add event publishing (EventBridge/SNS) for case lifecycle events.
- [ ] Implement async workers (SQS/Lambda or Spring Cloud Stream) for reminders and document generation.
- [ ] Harden security: OAuth2/OIDC, encryption, audit logging, secrets management.
- [ ] Expand automated testing: unit, integration, contract tests; performance test harness.

## Epic: Frontend Delivery (Angular 20)
- [ ] Finalise ABSA design tokens (colours, typography, spacing) and roll out across Tailwind + Angular Material overrides.
- [ ] Implement dashboard with live data bindings and activity feeds.
- [ ] Complete cases list page (filters, pagination, bulk actions, exports) connected to backend APIs.
- [ ] Build case creation wizard with validation and backend integration.
- [ ] Enhance case detail page (timeline, attachments, comments, workflow history tabs).
- [ ] Implement queue and workflow guide pages with dynamic data.
- [ ] Build IAM management UI for user/role administration.
- [ ] Implement notifications centre (list, detail, mark-as-read) synced with backend.
- [ ] Build reports page with interactive charts and export triggers.
- [ ] Integrate authentication flow (login, logout, SSO handoff, error states).
- [ ] Add shared state management (Signals/NgRx) and caching for core entities.
- [ ] Write unit/component tests (Jest), e2e tests (Cypress/Playwright), and accessibility audits.

## Epic: Infrastructure & Terraform
- [ ] Create Terraform module for VPC (subnets, gateways, routing, security groups).
- [ ] Create module for RDS PostgreSQL (multi-AZ, backups, encryption).
- [ ] Create module for container workloads (ECS Fargate or EKS) including autoscaling and service mesh decisions.
- [ ] Create module for S3 + CloudFront frontend hosting with WAF and ACM certificates.
- [ ] Create module for messaging/eventing (EventBridge, SNS/SQS) and notifications (SES).
- [ ] Create IAM baseline module (roles, policies, KMS keys, Secrets Manager policies).
- [ ] Compose environment stacks (dev/test/uat/prod) with remote state and parameterisation.
- [ ] Configure CI/CD pipelines for Terraform (plan/apply with manual approvals).
- [ ] Implement monitoring/observability stack (CloudWatch dashboards, alarms, X-Ray, GuardDuty, Security Hub).

## Epic: SMTP / Email Service Enablement
- [ ] Provision Amazon SES domains (sandbox + production) via Terraform.
- [ ] Configure DKIM, SPF, DMARC records and verify domains.
- [ ] Store SMTP credentials in AWS Secrets Manager with rotation policy.
- [ ] Integrate backend NotificationService with SES (templated emails, throttling strategy).
- [ ] Document email runbook (bounce handling, compliance, escalation paths).

## Epic: Quality Assurance & Release Readiness
- [ ] Define acceptance criteria per feature and align with QA team.
- [ ] Execute regression suites (frontend + backend) for UAT.
- [ ] Conduct load/performance tests and document results.
- [ ] Run security scans (Snyk, OWASP dependency check) and remediate findings.
- [ ] Coordinate penetration test and incorporate recommendations.
- [ ] Prepare training materials and release notes for stakeholders.
- [ ] Complete go/no-go checklist ahead of production deployment.

---

**References**
- `.docs/version-2026.01.00-plan.md` — Release scope and architecture overview.
- `frontend/` — Angular 20 application.
- `paia-backend-service/` — Spring Boot backend.
- `nexus-case-stream/` — React POC reference (paia-poc-vite).

