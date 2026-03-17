# MVP Scope: AI-Driver Proficiency Platform

## 1. Overview
This document defines the **Minimum Viable Product (V1)** scope for the AI-Driver Proficiency Platform. It prioritizes core value delivery while deferring advanced features to later phases.

---

## 2. MVP Goals
* Validate that candidates can complete a competency-based AI assessment end-to-end
* Issue a role-based certification upon passing
* Enable hiring managers to require and verify certifications for a single role
* Establish technical foundation (auth, API, AI evaluation) for scale

---

## 3. In Scope (V1)

### 3.1 User Management
| Feature | Description | Priority |
|---------|-------------|----------|
| Candidate registration | Email + password signup | P0 |
| Candidate login | Email/password + session | P0 |
| Basic profile | Name, email, role interest | P0 |
| Hiring manager account | Separate signup flow, org association | P0 |

**Out of scope for V1:** OAuth (LinkedIn/Google), SSO, team invites, RBAC beyond candidate vs hiring manager

---

### 3.2 Assessment Experience
| Feature | Description | Priority |
|---------|-------------|----------|
| Single role certification | One role (e.g., Marketing) fully built | P0 |
| Practical AI Task Sandbox | One task type: prompt + synthesize | P0 |
| Output Verification Module | One task: identify flaws in AI-generated content | P0 |
| Human-Plus (simplified) | One scenario: ethics or critical thinking | P1 |
| Timer per module | Configurable time limits | P1 |
| Save & resume | Persist progress, allow resume within session window | P1 |

**Out of scope for V1:** Multiple roles, multiple task types per module, adaptive difficulty, real-time streaming feedback

---

### 3.3 AI & Evaluation
| Feature | Description | Priority |
|---------|-------------|----------|
| AI integration (Vercel AI SDK) | GPT-4o or Claude for Sandbox tasks | P0 |
| DSPy evaluation | Grade Sandbox outputs, Verification submissions | P0 |
| Rubric-based scoring | Defined criteria per task | P0 |
| Pass/fail threshold | Configurable per assessment | P0 |

**Out of scope for V1:** Multi-model comparison, self-improving prompts, A/B testing of rubrics

---

### 3.4 Certification
| Feature | Description | Priority |
|---------|-------------|----------|
| Certificate generation | PDF with candidate name, role, date | P0 |
| Shareable badge link | Public URL for LinkedIn/CV | P0 |
| Badge image | Downloadable asset (PNG/SVG) | P1 |

**Out of scope for V1:** LinkedIn auto-add, blockchain verification, credential wallet

---

### 3.5 Hiring Manager Experience
| Feature | Description | Priority |
|---------|-------------|----------|
| Assessment link generation | Unique URL per job/role | P0 |
| Candidate list view | See who completed, pass/fail status | P0 |
| Candidate detail view | Scores, module breakdown, audit summary | P0 |
| Basic org dashboard | Pass rate, completion count | P1 |

**Out of scope for V1:** ATS integration, bulk invite, advanced analytics, team collaboration

---

### 3.6 Infrastructure
| Feature | Description | Priority |
|---------|-------------|----------|
| PostgreSQL | Candidates, assessments, scores, certifications | P0 |
| Redis | Session state during assessments | P0 |
| FastAPI backend | REST + WebSocket (if needed for V1) | P0 |
| TanStack Start frontend | Assessment UI, dashboard | P0 |
| AWS deployment | Single environment (staging or prod) | P0 |
| Audit logging | Log prompts + AI responses for verification | P0 |

**Out of scope for V1:** Multi-region, CDN, advanced monitoring, SOC2 prep

---

## 4. Out of Scope (Deferred)

| Category | Deferred Items |
|----------|----------------|
| **Auth** | OAuth, SSO, MFA, passwordless |
| **Assessment** | Multiple roles, adaptive tests, proctoring |
| **AI** | Multi-model, self-improving evaluation |
| **Certification** | LinkedIn integration, blockchain, credential wallet |
| **Hiring Manager** | ATS integration, bulk operations, advanced analytics |
| **Infrastructure** | Multi-region, compliance certifications |
| **Content** | SME-designed content library, multiple task templates |

---

## 5. Phase Roadmap (High-Level)

| Phase | Focus | Timeline (TBD) |
|-------|-------|----------------|
| **V1 (MVP)** | Single role, core assessment, certification, basic HM dashboard | 8–12 weeks |
| **V2** | 2–3 additional roles, OAuth, improved HM analytics | +6–8 weeks |
| **V3** | ATS integration, LinkedIn badge, SME content expansion | +8–12 weeks |
| **V4** | Industry standardization alignment, enterprise features | Ongoing |

---

## 6. Success Criteria for MVP
* [ ] Candidate can complete full assessment (Sandbox + Verification + Human-Plus) in one session
* [ ] Candidate receives pass/fail and certificate upon passing
* [ ] Hiring manager can create assessment link and view candidate results
* [ ] Audit log captures prompts and AI responses for verification
* [ ] System handles 50 concurrent assessments without degradation

---

## 7. Dependencies & Risks
| Dependency | Risk | Mitigation |
|------------|------|------------|
| AI API availability | Rate limits, cost | Implement caching, fallback model |
| Content quality | Poor task design affects validity | Start with 1–2 SME-reviewed tasks |
| DSPy maturity | Evaluation instability | Fallback to rule-based scoring if needed |
