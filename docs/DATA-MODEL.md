# Data Model: AI-Driver Proficiency Platform

## 1. Overview
This document defines the core entities, relationships, and schema for the AI-Driver Proficiency Platform. Primary database: **PostgreSQL**. Session state: **Redis**.

---

## 2. Entity Relationship Diagram (Text)

```
┌─────────────┐       ┌──────────────────┐       ┌─────────────────┐
│   User      │──────<│  AssessmentSession│>──────│   Assessment    │
└─────────────┘       └──────────────────┘       └─────────────────┘
       │                         │
       │                         │
       │                ┌────────┴────────┐
       │                │                 │
       │                ▼                 ▼
       │         ┌─────────────┐   ┌─────────────┐
       │         │ModuleResponse│   │  Score      │
       │         └─────────────┘   └─────────────┘
       │
       ▼
┌─────────────────┐       ┌──────────────────┐
│  Certification  │──────<│  Organization    │
└─────────────────┘       └──────────────────┘
                                 │
                                 │
                          ┌──────┴──────┐
                          │             │
                          ▼             ▼
                   ┌─────────────┐  ┌─────────────┐
                   │HiringManager│  │AssessmentLink│
                   └─────────────┘  └─────────────┘
```

---

## 3. Core Entities

### 3.1 User
Stores both candidates and hiring managers. Role distinguishes behavior.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PK | `usr_` prefix |
| email | VARCHAR(255) | UNIQUE, NOT NULL | Login identifier |
| password_hash | VARCHAR(255) | NOT NULL | Bcrypt hash |
| name | VARCHAR(255) | | Display name |
| role | ENUM | NOT NULL | `candidate` \| `hiring_manager` |
| role_interest | VARCHAR(50) | | For candidates: `marketing`, `legal`, `engineering` |
| organization_id | UUID | FK | For hiring managers |
| created_at | TIMESTAMPTZ | NOT NULL | |
| updated_at | TIMESTAMPTZ | NOT NULL | |

---

### 3.2 Organization
B2B entity for hiring managers.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PK | `org_` prefix |
| name | VARCHAR(255) | NOT NULL | Company name |
| industry | VARCHAR(100) | | Optional |
| created_at | TIMESTAMPTZ | NOT NULL | |
| updated_at | TIMESTAMPTZ | NOT NULL | |

---

### 3.3 Assessment
Template definition for a role-based certification (e.g., Marketing AI-Driver).

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | VARCHAR(50) | PK | `ast_mkt001` |
| name | VARCHAR(255) | NOT NULL | Display name |
| role | VARCHAR(50) | NOT NULL | `marketing`, `legal`, `engineering` |
| description | TEXT | | |
| estimated_duration_minutes | INT | | |
| modules | JSONB | | `["sandbox", "verification", "human_plus"]` |
| pass_threshold | DECIMAL(3,2) | | e.g., 0.70 |
| is_active | BOOLEAN | DEFAULT true | |
| created_at | TIMESTAMPTZ | NOT NULL | |
| updated_at | TIMESTAMPTZ | NOT NULL | |

---

### 3.4 AssessmentSession
One candidate's attempt at an assessment.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PK | `sess_` prefix |
| user_id | UUID | FK, NOT NULL | Candidate |
| assessment_id | VARCHAR(50) | FK, NOT NULL | |
| link_id | UUID | FK | If started via HM link |
| status | ENUM | NOT NULL | `in_progress` \| `completed` \| `expired` \| `abandoned` |
| started_at | TIMESTAMPTZ | NOT NULL | |
| expires_at | TIMESTAMPTZ | NOT NULL | |
| completed_at | TIMESTAMPTZ | | When last module submitted |
| current_module | VARCHAR(50) | | `sandbox` \| `verification` \| `human_plus` |
| created_at | TIMESTAMPTZ | NOT NULL | |
| updated_at | TIMESTAMPTZ | NOT NULL | |

---

### 3.5 ModuleResponse
Stores candidate submissions per module.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PK | |
| session_id | UUID | FK, NOT NULL | |
| module | VARCHAR(50) | NOT NULL | `sandbox` \| `verification` \| `human_plus` |
| payload | JSONB | NOT NULL | Task-specific data |
| submitted_at | TIMESTAMPTZ | NOT NULL | |
| created_at | TIMESTAMPTZ | NOT NULL | |

**Payload examples:**
* **Sandbox:** `{ "prompt_used", "ai_response", "deliverable" }`
* **Verification:** `{ "issues_identified", "corrections" }`
* **Human-Plus:** `{ "scenario_id", "response" }`

---

### 3.6 Score
Evaluation result per module and overall.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PK | |
| session_id | UUID | FK, NOT NULL | |
| module | VARCHAR(50) | | `sandbox` \| `verification` \| `human_plus` \| `overall` |
| score | DECIMAL(4,3) | | 0.000–1.000 |
| passed | BOOLEAN | | |
| feedback | JSONB | | Rubric breakdown, grader notes |
| evaluated_at | TIMESTAMPTZ | NOT NULL | |
| created_at | TIMESTAMPTZ | NOT NULL | |

---

### 3.7 Certification
Issued when candidate passes assessment.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PK | `cert_` prefix |
| user_id | UUID | FK, NOT NULL | |
| session_id | UUID | FK, NOT NULL | |
| assessment_id | VARCHAR(50) | FK, NOT NULL | |
| role | VARCHAR(50) | NOT NULL | |
| issued_at | TIMESTAMPTZ | NOT NULL | |
| badge_url | VARCHAR(500) | | Public shareable link |
| pdf_path | VARCHAR(500) | | S3 or storage path |
| created_at | TIMESTAMPTZ | NOT NULL | |

---

### 3.8 AssessmentLink
Unique link created by hiring manager for a job.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PK | `link_` prefix |
| organization_id | UUID | FK, NOT NULL | |
| created_by | UUID | FK (User), NOT NULL | |
| assessment_id | VARCHAR(50) | FK, NOT NULL | |
| job_title | VARCHAR(255) | | |
| expires_at | TIMESTAMPTZ | | |
| is_active | BOOLEAN | DEFAULT true | |
| created_at | TIMESTAMPTZ | NOT NULL | |
| updated_at | TIMESTAMPTZ | NOT NULL | |

---

### 3.9 AuditLog
For verification that candidate "drove" the AI (prompts + responses).

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PK | |
| session_id | UUID | FK, NOT NULL | |
| event_type | VARCHAR(50) | NOT NULL | `prompt_sent` \| `ai_response` \| `submission` |
| payload | JSONB | | Redacted if needed for PII |
| created_at | TIMESTAMPTZ | NOT NULL | |

---

## 4. Redis Schema (Session State)

### 4.1 Assessment Session State
**Key:** `session:{session_id}`  
**TTL:** Match `expires_at` of session  
**Value (JSON):**
```json
{
  "current_module": "sandbox",
  "module_state": {
    "sandbox": { "prompt_count": 3, "last_activity": "..." }
  },
  "last_heartbeat": "2025-03-17T10:15:00Z"
}
```

### 4.2 Rate Limiting
**Key:** `ratelimit:{user_id}:{endpoint}`  
**TTL:** 60 seconds  
**Value:** Counter (increment on request)

---

## 5. Indexes

| Table | Index | Purpose |
|-------|-------|---------|
| User | (email) UNIQUE | Login lookup |
| User | (organization_id) | HM by org |
| AssessmentSession | (user_id, status) | User's sessions |
| AssessmentSession | (link_id, status) | Sessions by HM link |
| AssessmentSession | (expires_at) | Cleanup expired |
| ModuleResponse | (session_id, module) UNIQUE | One response per module per session |
| Score | (session_id, module) | Scores by session |
| Certification | (user_id) | User's certs |
| Certification | (id) | Badge lookup (public) |
| AssessmentLink | (id) UNIQUE | Link lookup |
| AuditLog | (session_id, created_at) | Audit trail |

---

## 6. Enums

```sql
-- User.role
CREATE TYPE user_role AS ENUM ('candidate', 'hiring_manager');

-- AssessmentSession.status
CREATE TYPE session_status AS ENUM ('in_progress', 'completed', 'expired', 'abandoned');

-- Module names
CREATE TYPE module_type AS ENUM ('sandbox', 'verification', 'human_plus');
```
