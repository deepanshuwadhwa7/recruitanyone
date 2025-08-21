### HR–Job Seeker Matching Web App — SRS + Development Plan + Team & Timeline (v0.1)

- Document status: Draft
- Last updated: 2025-08-21
- Audience: Product, Engineering, Design, Compliance, Operations
- Reference: Recruiting agent example — [Exa Recruiting Agent](https://docs.exa.ai/examples/exa-recruiting-agent)

## 1. Purpose and Scope
- Build an easy-to-use platform where HR users post jobs and non-technical job seekers create profiles/resumes.
- The system reviews job postings and profiles to produce best-match recommendations with clear explanations.
- Stack: Python/FastAPI, React (Material UI + Tailwind), PostgreSQL + pgvector, Redis, S3, email provider.

## 2. Users and Roles
- HR (non-technical): Create/manage jobs, review matches, shortlist/export.
- Job Seeker (non-technical): Create profile, upload resume, apply, view recommendations.
- Admin: Oversight, moderation, configuration.

## 3. Assumptions, Constraints, Dependencies
- Email-based auth; resume uploads are PDF/DOCX; internet required.
- Single-region MVP; accessibility WCAG 2.1 AA; privacy-first (GDPR-ready).
- Prefer Postgres+pgvector over separate vector DB for MVP.

## 4. Functional Requirements (high-level)
- Auth/RBAC: Email/password, verification, reset; roles `hr`, `job_seeker`, `admin`; company membership and invites.
- Company/HR: Company profile; invite/remove HR members.
- Jobs: CRUD, publish/close, fields (title, description, requirements, skills, location, exp range, salary, type, tags), status, auto skill extraction, versioning.
- Profiles/Resumes: Structured profile, resume upload/parse (PDF/DOCX), skill normalization, visibility controls, multiple resumes.
- Matching: Automatic on job publish/profile update; ranked lists with scores/explanations; filters; semantic + keyword search.
- Applications/Shortlisting: Apply, track status; HR shortlist/reject/archive, notes/tags, exports.
- Notifications: Email and in-app alerts; digest emails.
- Admin/Moderation: Read-only admin portal, audit logs.

## 5. Non-Functional Requirements
- Performance: P95 load < 2.5s; match generation < 5s for 10k profiles.
- Scale: 100k profiles, 10k jobs; horizontal-ready.
- Security: OWASP ASVS L2, TLS-only, Argon2/bcrypt, rate limiting, CSRF, audit logs, malware scan on uploads.
- Privacy: Consent/visibility, export/delete, encryption at rest for sensitive PII.
- Accessibility: WCAG 2.1 AA.
- Observability: Structured logs, metrics, tracing.
- Internationalization: i18n-ready (Phase 2).

## 6. Data Model (conceptual)
- Core entities: User, Company, CompanyMembership, Job, Skill, JobSkill, Profile, ProfileSkill, Experience, Education, Project/Certification, ResumeFile, Application, Match, Notification, AuditLog.
- Indexes: Text search on job/profile/resume; GIN for arrays; pgvector for embeddings.

## 7. External Interfaces
- UI: React + MUI + Tailwind; HR dashboard, Job editor, Match results, Shortlist; Profile wizard, Resume parse; Admin overview.
- APIs (FastAPI, JSON, JWT): Auth (`/auth/*`), Company (`/companies/*`), Jobs (`/jobs/*`), Profiles (`/profiles/*`), Skills (`/skills`), Matching (`/jobs/{id}/matches`, `/profiles/me/recommendations`), Applications (`/applications`), Admin (`/admin/*`).
- Storage/Infra: PostgreSQL 15+ with pgvector, Redis, S3-compatible storage, email provider.

## 8. Matching Engine (MVP)
- Hybrid scoring: skills overlap (40%), semantic similarity (30%), experience alignment (15%), location (10%), compensation (5%).
- Pipeline: Parse/normalize → extract skills → embed (e.g., OpenAI or local) → prefilter → vector search → rescore with structured features → explanations (matched skills/gaps, experience fit).
- Fairness: Exclude protected attributes from features and UI.

## 9. Security, Privacy, Accessibility
- Argon2/bcrypt, JWT rotation, device revocation; RBAC with company isolation; audit logs.
- PII encryption at rest; consent-driven visibility; data export/delete.
- WCAG AA: keyboard nav, focus styles, contrast, ARIA, inline validation.

## 10. Acceptance Criteria (samples)
- Auth: Verified users can login/logout; refresh works.
- Jobs: HR can publish/close; list shows correct status.
- Profiles: Job seeker completes wizard; resume parse is editable and saved.
- Matching: ≥10 ranked candidates for test data in <5s; explanations visible.
- Applications: Apply, shortlist with notes; status updates notify users.
- Accessibility: Primary flows pass automated axe checks.

---

## 11. Development Plan (Phases, Deliverables, Man-Hours)

Assumptions: 40 h/week; estimates include engineering + QA; +15% contingency recommended.

- Phase 0 — Foundations and Scaffolding (Total 90 h)
  - Backend 30, Frontend 30, DevOps 20, QA 10
  - Deliverables: Repos, Docker Compose, CI/CD, FastAPI/React shells, DB migrations, base theme/routing.

- Phase 1 — MVP Core (Feature Complete) (Total 892 h)
  - Auth/RBAC/companies/invites: BE 96, FE 48, QA 18 = 162
  - Jobs CRUD + publish/close + skills extraction: BE 60, FE 56, QA 12 = 128
  - Profiles + resume parsing + S3: BE 70, FE 80, QA 16 = 166
  - Matching v1 (pgvector, scoring, explanations): BE 80, FE 24, QA 12 = 116
  - Applications + shortlisting: BE 40, FE 40, QA 10 = 90
  - Notifications (email/in-app): BE 24, FE 12, Infra 16, QA 8 = 60
  - Admin read-only: BE 24, FE 24, QA 6 = 54
  - Integration buffer (~15%): 116

- Phase 2 — UX, Search, Quality Upgrades (Total 264 h)
  - Backend 90, Frontend 130, QA/Accessibility 30, Contingency 14
  - Deliverables: Faceted filters, semantic/keyword search, exports (CSV/PDF), empty states, skeletons, AA baseline.

- Phase 3 — Observability, Security, Performance (Total 200 h)
  - Backend 110, Infra 60, QA/Sec 30
  - Deliverables: Logs/metrics/traces, rate limiting, audit logs, malware scan, Redis caching, index/pgvector tuning, background workers.

- Phase 4 — Beta Pilot, Analytics, Docs (Total 140 h)
  - Engineering 90, QA/UAT 30, Docs/PM 20
  - Deliverables: Product analytics, user guides, pilot bug triage/fixes, perf passes.

- Optional Phase 5 — Extensions (Post-MVP, 400 h)
  - Google OAuth SSO (40), External sourcing via Exa (100), Messaging (120), Interview scheduling (80), i18n enablement (60).

- Core Phases 0–4 Total: 1,586 h (+15% ≈ 1,824 h)

---

## 12. Team Sizing and Timeline

### Recommended team (balanced; Beta in 10–12 weeks)
- Backend: 2 FTE
- Frontend: 2 FTE
- Product Designer/UX: 0.5 FTE
- QA/Accessibility: 0.5 FTE
- DevOps/SRE: 0.25 FTE
- PM/Producer: 0.25 FTE

Weekly capacity ≈ 220 h. 1,824 h / 220 ≈ 8.3 weeks of pure build → plan 10–12 weeks including ramp, reviews, and buffer.

### Sprint timeline (2-week sprints)
- Sprint 1 (Weeks 1–2): Phase 0 Foundations — M0
- Sprint 2 (Weeks 3–4): Phase 1 (Auth/RBAC, Jobs CRUD start, Profile wizard skeleton) — M1
- Sprint 3 (Weeks 5–6): Phase 1 (Resume parse/S3, Matching v1, Applications, Admin RO) — M2
- Sprint 4 (Weeks 7–8): Phase 2 (UX/search/exports/accessibility) — M3
- Sprint 5 (Weeks 9–10): Phase 3 (observability/security/perf) — M4
- Sprint 6 (Weeks 11–12): Phase 4 (analytics, docs, UAT, pilot) — M5

### Alternatives
- Minimal team (1 BE, 1 FE, 0.25 UX, 0.25 QA, 0.2 DevOps, 0.2 PM): ~116 h/week → 16–18 weeks to Beta.
- Aggressive team (3 BE, 3 FE, 1 UX, 1 QA, 0.5 DevOps, 0.5 PM): ~360 h/week → 6–8 weeks to Beta (higher coordination risk).

### Milestones
- M0: CI/CD, deployable FE/BE shells, DB migrations.
- M1: Auth/RBAC + Jobs CRUD draft + Profile wizard scaffold.
- M2: Matching v1 + Applications + Admin RO (E2E demo).
- M3: UX/search polish, exports, AA checks.
- M4: Observability/security/perf baseline.
- M5: Beta pilot readiness with analytics and docs.

---

## 13. Technology Choices
- Backend: Python 3.11, FastAPI, SQLAlchemy/SQLModel, Pydantic v2, Alembic.
- DB: PostgreSQL 15+, pgvector.
- Cache/Queue: Redis (RQ/Celery/Arq).
- Storage: S3-compatible.
- Frontend: React 18, TypeScript, Vite, Material UI + Tailwind, React Router, React Query, Zod/react-hook-form.
- Testing: PyTest, Playwright.
- DevOps: Docker, Docker Compose, GitHub Actions, CDN.

---

## 14. Risks and Mitigations
- Resume parsing accuracy: start rule-based + manual edit UX; add ML later.
- Matching performance: pgvector IVF + prefilters; async background embeddings; caching.
- UX for non-technical users: early usability tests after Sprint 4; iterate copy and flows.
- Vendor lock-in (embeddings/email): abstractions to swap providers.
- Privacy/compliance: consent gating, encryption, audit logs prior to pilot.

---

If you’d like, we can follow this by generating API contracts and an initial DB schema migration (Alembic) aligned to the entities above.