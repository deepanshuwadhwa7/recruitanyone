### Software Requirements Specification (SRS) — HR–Job Seeker Matching Web Application (v0.1)

- Document status: Draft
- Last updated: 2025-08-21
- Audience: Product, Engineering, Design, Compliance, Operations
- Reference: Recruiting agent example using Exa — [Exa Recruiting Agent](https://docs.exa.ai/examples/exa-recruiting-agent)

## 1. Purpose
- Build an easy-to-use platform where company HR users post jobs and non-technical job seekers create profiles/resumes.
- The system reviews job postings and profiles to produce best-match recommendations with transparent explanations.

## 2. Scope
- Web application with a Python/FastAPI backend, React-based frontend (Material UI + Tailwind), and persistent data store.
- Roles: HR, Job Seeker, Admin.
- Core: job posting, profile management, automated matching, and shortlisting.
- Future: messaging, interview scheduling, analytics, external talent sourcing via Exa.

## 3. Definitions, Acronyms, Abbreviations
- HR: Hiring/Recruiter users from companies.
- Matching/Recommendation: Ranked list of candidate-job pairs with scores and explanations.
- PII: Personally Identifiable Information.
- Exa: AI search tooling; example recruiting agent linked above for inspiration.

## 4. References
- [Exa Recruiting Agent](https://docs.exa.ai/examples/exa-recruiting-agent)
- OWASP ASVS, WCAG 2.1 AA, GDPR.

## 5. Overall Description

### 5.1 User Classes and Characteristics
- HR (non-technical): Create and manage jobs, shortlist candidates, export/share shortlists.
- Job Seeker (non-technical): Create profile, upload resume, manage visibility, apply to jobs.
- Admin: Oversight, moderation, support, configuration.

### 5.2 Assumptions and Dependencies
- Users have email and can receive verification links.
- Documents: PDF/DOCX resumes commonly uploaded; resume parsing required.
- Optional external sourcing via Exa later.
- Internet connectivity required.
- Initial single-region deployment with CDN.

### 5.3 Constraints
- UX must be simple and accessible (WCAG 2.1 AA).
- Privacy-first handling of PII; opt-in sharing.
- Early-stage scale: 10–100 concurrent HR users, 1k–50k job seekers.
- Budget-conscious: PostgreSQL + pgvector preferred over separate vector DB initially.

## 6. System Features and Functional Requirements

### 6.1 Authentication and Authorization
- Email/password sign-up/login with email verification.
- Password reset via email.
- OAuth (Google) optional (Phase 2).
- Roles: `hr`, `job_seeker`, `admin`.
- Company HR membership and invite flow.
- RBAC: HR scoped to their company’s data; job seekers access only their own data; admins global.

Requirements:
- FR-Auth-1: Create account, verify email.
- FR-Auth-2: Login/logout, refresh tokens.
- FR-Auth-3: Company creation (first HR), invite/manage HR members.
- FR-Auth-4: Role-based access to APIs and UI.

### 6.2 Company and HR Management
- Create company profile, logo, domains, locations.
- Invite HR teammates via email.
- Manage billing/contact info (future).

Requirements:
- FR-Co-1: CRUD company (admin/hr-owner).
- FR-Co-2: Invite/accept/remove HR members.

### 6.3 Job Posting Management
- Job fields: title, description, requirements, skills, location (onsite/remote/hybrid), experience range, salary range, employment type, keywords, tags.
- Status: draft, published, closed.
- Auto-extraction of skills from description.
- Versioning for edits (keep change history).

Requirements:
- FR-Job-1: HR CRUD job postings.
- FR-Job-2: Publish/close jobs.
- FR-Job-3: Extract skills/metadata on save.
- FR-Job-4: List/filter jobs by status, team, tags.

### 6.4 Job Seeker Profile and Resume
- Profile sections: summary, total experience, skills, current role, work history, education, certifications, projects, location, preferences, salary.
- Resume upload (PDF/DOCX), parsing, and structured extraction.
- Control visibility: public to company HRs vs. apply-only.
- Multiple resumes support (select primary).

Requirements:
- FR-Prof-1: CRUD profile with structured fields.
- FR-Prof-2: Upload and parse resume; show parse and allow edits.
- FR-Prof-3: Manage skills (normalized list + free-form).
- FR-Prof-4: Visibility settings and consent for sharing with HR.

### 6.5 Matching and Recommendations
- Automatic matching when a job is published or profile updated.
- Rank candidates for a job and jobs for a candidate.
- Show score, top reasons, and gaps (explanations).
- Filters (location, experience, salary, skills required).
- Manual search with semantic and keyword search.

Requirements:
- FR-Match-1: Generate match list for a job with scores/explanations.
- FR-Match-2: Generate recommended jobs for a job seeker.
- FR-Match-3: Filter/sort matches; export shortlist CSV/PDF.
- FR-Match-4: Re-run matching on edits; incremental updates.

### 6.6 Applications and Shortlisting
- Job seekers can apply to published jobs (attach resume).
- HR can shortlist, reject, archive, add notes.
- Bulk actions and tagging.

Requirements:
- FR-App-1: Apply to job, track status (applied, shortlisted, rejected, hired).
- FR-App-2: HR shortlist with notes/tags.
- FR-App-3: Export shortlist; share via secure link.

### 6.7 Notifications
- Email notifications for HR (new strong matches), job seekers (status updates).
- In-app alerts and digest emails.

Requirements:
- FR-Notif-1: Configurable notifications.
- FR-Notif-2: Daily/weekly digests.

### 6.8 Admin and Moderation
- Manage users, companies, jobs.
- Metrics dashboards (signups, posts, applications).
- Content moderation pipeline for profiles/jobs.

Requirements:
- FR-Admin-1: Admin portal with RBAC.
- FR-Admin-2: Audit logs for sensitive actions.

## 7. Non-Functional Requirements (NFRs)

- NFR-Perf: P95 page load < 2.5s; match generation < 5s for 10k profiles.
- NFR-Scale: Support 100k profiles, 10k jobs; horizontal scale-ready.
- NFR-Availability: 99.5% for MVP, 99.9% later.
- NFR-Security: OWASP ASVS L2; hashed passwords (Argon2/bcrypt), TLS-only, prepared statements, CSRF protection, rate limiting, audit logs.
- NFR-Privacy: GDPR-readiness; data export/delete; consent tracking; DPA and privacy policy.
- NFR-Accessibility: WCAG 2.1 AA.
- NFR-Internationalization: i18n-ready; Phase 2 for multiple locales.
- NFR-Observability: Structured logs, metrics, traces; error budgets and alerts.
- NFR-Compliance: Age of consent, ToS, acceptable use.

## 8. Data Model (Conceptual)

Entities (key fields):
- User: id, email, password_hash, role, status, created_at.
- Company: id, name, domain, logo_url, created_by.
- CompanyMembership: id, user_id, company_id, role, invited_at, accepted_at.
- Job: id, company_id, title, description, requirements, location_type, min_exp, max_exp, salary_min, salary_max, employment_type, tags[], status, created_at, updated_at.
- JobSkill: id, job_id, skill_id, weight.
- Skill: id, name, normalized_name.
- Profile: id, user_id, summary, total_experience_years, current_role, location, salary_expectation, visibility.
- Experience: id, profile_id, company, title, start_date, end_date, description.
- Education: id, profile_id, school, degree, field, start_date, end_date.
- Certification/Project: id, profile_id, fields...
- ProfileSkill: id, profile_id, skill_id, proficiency_level, years.
- ResumeFile: id, profile_id, storage_url, file_type, extracted_text, parsed_json.
- Application: id, job_id, profile_id, status, notes, tags[], created_at.
- Match: id, job_id, profile_id, score, explanation_json, created_at.
- Notification, AuditLog, ApiToken (if needed).

Indexes:
- Text search on job.title, job.description, profile.summary, resume text.
- GIN on tags/skills arrays.
- Vector index on embeddings (pgvector).

## 9. External Interface Requirements

### 9.1 UI/UX (React + MUI + Tailwind)
- Clean, large touch targets, plain language labels, progressive disclosure.
- Key screens:
  - HR: Dashboard, Job List, Job Editor, Match Results, Shortlist, Company Settings.
  - Job Seeker: Profile Builder (stepper), Resume Upload/Parse, Job Recommendations, Applications.
  - Auth: Sign-in/Sign-up/Verification, Password reset.
  - Admin: Overview, Users, Companies, Jobs, Logs.
- Components:
  - Search bars with autosuggest skills.
  - Faceted filters.
  - Empty states and skeleton loaders.
  - Accessible forms with inline validation and help text.

### 9.2 APIs (FastAPI, JSON)
Representative endpoints:
- Auth: `POST /auth/register`, `POST /auth/login`, `POST /auth/refresh`, `POST /auth/verify`, `POST /auth/reset`
- Company: `POST /companies`, `GET /companies/{id}`, `POST /companies/{id}/invites`
- Jobs: `POST /jobs`, `GET /jobs`, `GET /jobs/{id}`, `PUT /jobs/{id}`, `POST /jobs/{id}/publish`, `POST /jobs/{id}/close`
- Profiles: `POST /profiles`, `GET /profiles/me`, `PUT /profiles/me`, `POST /profiles/me/resume`
- Skills: `GET /skills?query=...`
- Matching: `GET /jobs/{id}/matches`, `GET /profiles/me/recommendations`, `POST /matching/reindex`
- Applications: `POST /jobs/{id}/apply`, `GET /applications`, `PATCH /applications/{id}`
- Admin: `GET /admin/users`, `GET /admin/companies`, `GET /admin/jobs`

Standards:
- JWT-based auth, Bearer tokens.
- Pagination: `?page`, `?page_size` with `Link` headers.
- Errors: RFC 7807 problem+json.

### 9.3 Storage and Integrations
- Database: PostgreSQL 15+ with pgvector.
- Cache/Queues: Redis (rate limit, jobs).
- Object storage: S3-compatible for resumes.
- Email: Transactional provider (e.g., SES/Sendgrid).
- Optional: Exa API for external candidate discovery (Phase 2).

## 10. Matching Engine

### 10.1 Approach
- Hybrid matching:
  - Structured features: skills overlap (weighted), years experience, location, employment type, salary alignment.
  - Textual semantic similarity: embeddings for job description vs. profile/resume summary (pgvector).
  - Rule constraints: mandatory skills, location eligibility, work authorization (if captured).
- Scoring:
  - Weighted sum with calibration:
    - Skills exact/normalized match (40%)
    - Semantic text similarity (30%)
    - Experience alignment (15%)
    - Location/timezone/remote (10%)
    - Compensation alignment (5%)
- Explanations:
  - Top matched skills, years-experience fit, key phrases, missing skills/gaps.
- Fairness:
  - Exclude sensitive attributes (gender, age, race); do not use protected attributes in scoring.

### 10.2 Pipeline
- Ingestion: On job publish/profile update, parse and normalize.
- Extraction: NER/keyword extraction for skills (dictionary + ML fallback).
- Embeddings: e.g., OpenAI text-embedding-3-small or local model; store in pgvector.
- Candidate set: fast filter by constraints, then vector similarity, then rescoring with structured features.
- Reindexing: Nightly batch and on-change incremental.

### 10.3 Optional External Sourcing (Future)
- Use the approach from the [Exa Recruiting Agent](https://docs.exa.ai/examples/exa-recruiting-agent) to discover external profiles; import with consent.

## 11. Security and Privacy
- Password hashing (Argon2/bcrypt), JWT rotation, device revocation.
- PII encryption at rest for sensitive fields.
- Least-privilege RBAC; company data isolation.
- Consent capture for profile visibility; data export/delete.
- Audit logs for admin/HR sensitive actions.
- File scanning on upload (malware scan).
- Rate limiting and bot protection on public endpoints.

## 12. Accessibility
- WCAG 2.1 AA: keyboard nav, focus states, color contrast, ARIA labels, alt text.
- Forms: descriptive labels, error summaries, helper text, inline validation.

## 13. Performance and Scalability
- Use Postgres indexes and pgvector IVF/flat as appropriate.
- Cache common lists (skills, filters) in Redis.
- Background workers for resume parsing and embedding.
- CDN for static assets; image optimization.

## 14. Logging, Monitoring, and Analytics
- Structured logs (JSON), correlation IDs.
- Metrics: request latency, error rates, queue depths, match generation time.
- Product analytics: funnel (job post, profile completion, applications), anonymized.

## 15. Data Retention and Lifecycle
- Configurable retention for applications and resumes.
- Soft-deletes with purge after grace period.
- User-initiated account deletion with export.

## 16. Localization and Legal
- Strings externalized for i18n.
- ToS, Privacy Policy, cookie consent.
- DPA for enterprise customers.

## 17. Open Questions
- Will HR users import candidates externally? If yes, consent handling.
- Compensation range visibility across regions?
- Work authorization capture and regional compliance?
- Admin multi-tenant or single global org?

## 18. MVP Milestones and Acceptance Criteria

### 18.1 MVP Scope (Phase 1)
- Auth + Company HR invites
- Job CRUD + publish/close
- Profile builder + resume upload/parse
- Matching with scores and explanations
- Applications + shortlisting
- Basic notifications
- Admin read-only views
- Core NFRs: security baseline, accessibility, observability

### 18.2 Acceptance Criteria (samples)
- AC-Auth-1: Verified user can log in/out and receive a valid JWT; refresh works.
- AC-Job-1: HR can create a job with mandatory fields and publish it; status visible on list.
- AC-Prof-1: Job seeker completes profile wizard and uploads resume; parse results editable and saved.
- AC-Match-1: Publishing a job produces ≥10 ranked candidates (on test data) in under 5s with visible explanations.
- AC-Apply-1: Job seeker applies; HR sees application and can shortlist with a note.
- AC-Notif-1: HR gets a daily digest of new strong matches.
- AC-Access-1: Screen reader can navigate primary flows; all forms pass axe checks.

## 19. Technology Choices

- Backend: Python 3.11, FastAPI, SQLAlchemy 2.0 or SQLModel, Pydantic v2, Alembic.
- DB: PostgreSQL 15+, pgvector for embeddings.
- Cache/Queue: Redis (RQ/Celery/Arq).
- Storage: S3-compatible for resumes.
- Frontend: React 18, TypeScript, Vite, Material UI + Tailwind (utility classes), React Router, React Query, Zod/react-hook-form.
- Testing: PyTest, Playwright.
- DevOps: Docker, Docker Compose, GitHub Actions CI/CD, IaC (Terraform later), CDN.

## 20. UX Flows (High-level)

- HR:
  1) Sign up → Create/Join Company → Post Job → View Matches → Shortlist/Export
- Job Seeker:
  1) Sign up → Profile Wizard → Upload Resume → Review Parse → View Recommended Jobs → Apply
- Admin:
  1) Sign in → Overview → Users/Companies → Audit Logs

If you want, this SRS can be evolved into a living document with diagrams and API contracts in follow-up commits.