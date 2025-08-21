### Team Size and Timeline Estimate (v0.1)

Assumptions:
- Core scope: Phases 0–4 = ~1,586 h + 15% buffer ≈ 1,824 h total.
- 40 h/week per FTE; 2-week sprints; emphasis on simple, accessible UX.

## Recommended Team (Balanced; Beta in 10–12 weeks)
- Backend: 2 FTE
- Frontend: 2 FTE
- Product Designer/UX: 0.5 FTE
- QA/Accessibility: 0.5 FTE
- DevOps/SRE: 0.25 FTE
- PM/Producer: 0.25 FTE

Weekly capacity ≈ 220 h/week. Duration ≈ 1,824 ÷ 220 = 8.3 weeks pure build.
Plan for 10–12 weeks including ramp-up, reviews, context switching, and buffer.

## Sprint Timeline (2-week sprints)
- Sprint 1 (Weeks 1–2): Phase 0 Foundations — M0
- Sprint 2 (Weeks 3–4): Phase 1 (Auth/RBAC, Jobs CRUD start, Profile wizard skeleton) — M1
- Sprint 3 (Weeks 5–6): Phase 1 (Resume parse/S3, Matching v1, Applications, Admin RO) — M2
- Sprint 4 (Weeks 7–8): Phase 2 (UX/search/exports/accessibility) — M3
- Sprint 5 (Weeks 9–10): Phase 3 (observability/security/perf) — M4
- Sprint 6 (Weeks 11–12): Phase 4 (analytics, docs, UAT, pilot) — M5

## Alternatives
- Minimal team (cost-sensitive, slower)
  - 1 BE, 1 FE, 0.25 UX, 0.25 QA, 0.2 DevOps, 0.2 PM
  - Capacity ≈ 116 h/week → 1,824 h ≈ 16 weeks (plan 16–18 weeks)

- Aggressive team (faster, higher coordination)
  - 3 BE, 3 FE, 1 UX, 1 QA, 0.5 DevOps, 0.5 PM
  - Capacity ≈ 360 h/week → 1,824 h ≈ 5 weeks (plan 6–8 weeks)

## Staffing Ramp (who is busiest when)
- Weeks 1–2: DevOps setup; BE/FE scaffolding; Designer design system; QA test strategy.
- Weeks 3–6: BE/FE feature throughput; Designer on key flows; QA parallel test authoring.
- Weeks 7–8: UX/accessibility polish; BE final search/matching edges; QA accessibility/cross-browser.
- Weeks 9–10: Observability/security/perf; QA security/soak tests.
- Weeks 11–12: UAT, analytics, docs; bug triage and stabilization.

## Milestones
- M0: CI/CD, deployable FE/BE shells, DB migrations.
- M1: Auth/RBAC + Jobs CRUD draft + Profile wizard scaffold.
- M2: Matching v1 + Applications + Admin RO (E2E demo).
- M3: UX/search polish, exports, AA checks.
- M4: Observability/security/perf baseline.
- M5: Beta pilot readiness with analytics and docs.

Notes:
- The balanced team is recommended to hit Beta in 10–12 weeks with strong UX.
- Choose minimal or aggressive options based on budget and risk tolerance.