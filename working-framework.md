# Delivery Framework – Developer- New Project and Centred BAU after release with Compliance Squad

This document captures the operating model for new-project delivery within a BAU cadence. It balances developer autonomy, stakeholder transparency, and embedded compliance.

## 1. Phased Delivery Overview

```mermaid
flowchart LR
    A[Phase 0
    Inception & Governance] --> B[Phase 1
    Discovery Sprints]
    B --> C[Phase 2
    Build Iterations]
    C --> D[Phase 3
    Hardening & Release]
    D --> E[Phase 4
    BAU Operations]
    E -->|Continuous Feedback| C
```

**Highlights**
- Each phase ends with tangible artefacts (architecture pack, refined backlog, release candidate, runbook).
- Feedback loops ensure we iterate quickly without sacrificing compliance.

## 2. Squad Composition & Responsibilities

```mermaid
graph TD
    PO(Product Owner)
    DL(Delivery Lead / Scrum Master)
    TL-Tech(Tech Lead Frontend)
    TL-Back(Tech Lead Backend)
    Dev(Developers)
    QA(QA / Test Automation)
    DevOps(DevOps / Platform)
    Comp(Compliance Squad)
    UX(UX / UI Designer)

    PO -->|Backlog & Priorities| DL
    DL -->|Sprint Cadence| Dev
    TL-Tech --> Dev
    TL-Back --> Dev
    QA --> Dev
    DevOps --> Dev
    Comp --> DL
    Comp --> TL-Tech
    Comp --> TL-Back
    UX --> PO
    UX --> Dev

    Dev --- QA
    Dev --- DevOps
    Dev --- Comp
```

**Operating Notes**
- Compliance squad participates in planning, reviews, and definition-of-done checks.
- Tech leads provide architectural guidance while developers own implementation decisions.

## 3. Iteration Workflow

```mermaid
sequenceDiagram
    participant PO as Product Owner
    participant Comp as Compliance Squad
    participant Dev as Dev Team
    participant QA as QA/Automation
    participant Stake as Stakeholders

    PO->>Comp: Review upcoming backlog
    PO->>Dev: Sprint planning with compliance criteria
    loop Daily
        Dev->>Dev: Stand-up & progress
        Comp-->>Dev: Compliance check-ins (twice weekly)
    end
    Dev->>QA: Feature ready for testing
    QA->>Dev: Automated & exploratory test results
    Dev->>Stake: Sprint review demo
    Comp->>Stake: Compliance sign-off summary
    Dev->>Dev: Retrospective & process improvements
```

**Key Practices**
- Definition of Done includes automated tests, security scan, compliance checklist.
- Compliance updates are lightweight but frequent—no surprises at release.

## 4. Artefacts & Tooling per Phase

```mermaid
mindmap
  root((Artefacts))
    Phase 0
      Governance Charter
      Architecture Sketches
      Working Agreements
    Phase 1
      Refined Backlog
      UX Prototypes
      Risk Assessment
    Phase 2
      Incremental Features
      Automated Tests
      Compliance Checklist Updates
    Phase 3
      Release Notes
      Runbook & Playbooks
      Security & Performance Reports
    Phase 4
      Monitoring Dashboards
      BAU Backlog
      Continuous Improvement Logs
```

**Tooling Suggestions**
- Jira for backlog/epics, including compliance tasks.
- GitHub/GitLab (CI pipelines, code reviews).
- Terraform for IaC, SonarQube/Snyk for quality/security, Cypress/Playwright for e2e.

## 5. Adoption Checklist

- [ ] Workshop to confirm working agreement and cadence.
- [ ] Embed compliance squad members in sprint rituals (planning, reviews, periodic stand-ups).
- [ ] Codify Definition of Done (tests, docs, compliance checks, pipeline green).
- [ ] Automate quality gates early (linting, unit/integration tests, dependency scans).
- [ ] Maintain shared documentation repo (`.docs/`) for architecture decisions, release notes, and runbooks.
- [ ] Establish BAU backlog grooming and quarterly release planning.

---

**References**
- `.docs/version-2026.01.00-plan.md`
- `.docs/version-2026.01.00-tasks.md`
- `frontend/`, `paia-backend-service/`, `nexus-case-stream/` repositories

