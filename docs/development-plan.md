### Development Plan with Phases and Man-Hour Estimates (v0.1)

Assumptions:
- Stack: FastAPI + PostgreSQL + pgvector + Redis + S3, React + MUI + Tailwind, JWT auth, email via provider (SES/Sendgrid).
- MVP scope as defined in SRS v0.1.
- Estimates include engineering + QA; add 10–20% contingency depending on team maturity.

## Phase 0 — Foundations and Scaffolding
Scope:
- Repos, environments, Docker Compose, FastAPI + React scaffolds, CI/CD, code quality, base theming, routing, DB migrations, seed data.

Man-hours:
- Backend: 30
- Frontend: 30
- DevOps/Infra: 20
- QA/Release: 10
- Total: 90

Deliverables:
- Running local/dev/stage envs, CI pipeline, base app shells.

## Phase 1 — MVP Core (Feature Complete)
Scope:
- Auth + RBAC + companies/invites
- Job CRUD + publish/close + skills extraction
- Profile builder (stepper) + resume upload/parse + S3
- Matching v1 (hybrid: skills + embeddings + basic scoring/explanations)
- Applications + shortlisting + HR notes/tags
- Basic notifications (email + in-app)
- Admin read-only screens

Man-hours (by area):
- Auth + RBAC + company/invites: BE 96, FE 48, QA 18 = 162
- Jobs CRUD + publish/close + skills extraction: BE 60, FE 56, QA 12 = 128
- Profile + resume parsing (PDF/DOCX) + S3: BE 70, FE 80, QA 16 = 166
- Matching v1 (pgvector, embeddings, scoring, explanations): BE 80, FE 24, QA 12 = 116
- Applications + shortlisting: BE 40, FE 40, QA 10 = 90
- Notifications (provider integration + settings): BE 24, FE 12, Infra 16, QA 8 = 60
- Admin read-only: BE 24, FE 24, QA 6 = 54
- Integration fixes/buffer (~15%): 116
- Total: 892

Deliverables:
- End-to-end flows for HR and Job Seeker with ranking and explanations; admin visibility.

## Phase 2 — UX, Search, and Quality Upgrades
Scope:
- Faceted filters, semantic/keyword search, shortlist export (CSV/PDF), re-run matching on edits, empty states, skeletons, helpful inline validation, accessibility AA baseline.

Man-hours:
- Backend: 90
- Frontend: 130
- QA/Accessibility: 30
- Total: 250
- Contingency (6%): 14
- Total: 264

Deliverables:
- Polished, accessible flows with strong discoverability and exports.

## Phase 3 — Observability, Security, Performance
Scope:
- Structured logging + metrics + tracing, rate limiting, audit logs, malware scan on uploads, caching (Redis), indexes and pgvector tuning, background workers for parsing/embeddings.

Man-hours:
- Backend: 110
- Infra/DevOps: 60
- QA/Sec review: 30
- Total: 200

Deliverables:
- Stable, secure, observable system ready for pilot.

## Phase 4 — Beta Pilot, Analytics, Docs
Scope:
- Product analytics events, funnels, user guides, support SOPs, bug triage/fixes from pilot feedback, performance tuning passes.

Man-hours:
- Engineering: 90
- QA/UAT: 30
- Docs/PM: 20
- Total: 140

Deliverables:
- Beta-ready release with instrumentation and documentation.

## Phase 5 — Optional Extensions (Post-MVP)
- Google OAuth SSO: 40
- External sourcing (Exa) import flow + consent: 100
- Messaging (HR ↔ candidate): 120
- Interview scheduling + calendar: 80
- i18n enablement (en baseline + framework): 60
- Total optional: 400

---

### Roll-up Man-Hours (Core Phases 0–4)
- Phase 0: 90
- Phase 1: 892
- Phase 2: 264
- Phase 3: 200
- Phase 4: 140
- Total (0–4): 1,586
- Suggested contingency: +15% = ~1,824

### Team Capacity and Calendar Time

Option A: Small team (2 BE, 2 FE, 0.5 QA, 0.25 Design, 0.25 PM)
- Weekly capacity ≈ 185 h/week
- Core 0–4 (1,586 h) ≈ 8.6 weeks; with contingency ≈ 9.9–10.5 weeks
- Practical plan: 11–12 weeks including context switching, reviews, holidays

Option B: Solo developer (full-stack)
- 40 h/week
- Core 0–4 (1,586 h) ≈ 40 weeks; with contingency ≈ 46 weeks

### Milestones and Gates
- M0 (end Phase 0, Week 1): CI/CD, deployable “Hello World” FE/BE, DB migrations running.
- M1 (mid Phase 1, Weeks 3–4): Auth/RBAC + Jobs CRUD published + Profile builder skeleton.
- M2 (end Phase 1, Week 6): Matching v1 + Applications + Admin read-only; demoable E2E.
- M3 (end Phase 2, Week 8): UX polish, filters/search, exports, AA accessibility checks pass.
- M4 (end Phase 3, Weeks 9–10): Logs/metrics/traces, rate limits, audit logs, malware scan, performance baseline.
- M5 (end Phase 4, Weeks 11–12): Beta pilot complete, analytics, docs, prioritized backlog for GA.

### High-Level Work Breakdown by Role (Core 0–4)
- Backend: ~556–620 h
- Frontend: ~370–420 h
- Infra/DevOps: ~96–120 h
- QA/Accessibility: ~120–150 h
- Product/Design/Docs: ~80–110 h
- Buffer/Integration: ~250–300 h

### Risks and Mitigations
- Resume parsing accuracy: start simple (PDF/DOCX text + rule-based skills), add ML later.
- Matching performance at scale: use pgvector IVF + prefilters; background embedding jobs.
- UX complexity for non-technical users: usability tests after Phase 2; iterate copy and flows.
- Vendor dependency (embeddings/email): wrap behind interface; enable swap.
- Data privacy: consent gating, encryption at rest, audit logs before pilot.

### Next Steps
- Confirm team composition and desired timeline.
- Approve the plan and contingency.
- Translate into sprint backlog (2-week sprints) with DOR/DoD.