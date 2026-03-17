# API Specification: AI-Driver Proficiency Platform

## 1. Overview
This document defines the REST and WebSocket API contracts for the AI-Driver Proficiency Platform. The backend is implemented with **FastAPI** (Python).

**Base URL (staging):** `https://api.staging.thevibenation.com/v1`  
**Base URL (prod):** `https://api.thevibenation.com/v1`

---

## 2. Authentication

### 2.1 Auth Flow
* **Login:** `POST /auth/login` → Returns JWT access token + refresh token
* **Refresh:** `POST /auth/refresh` → Returns new access token
* **Logout:** `POST /auth/logout` → Invalidates refresh token
* **Register:** `POST /auth/register` → Creates account, returns tokens

### 2.2 Token Usage
* **Header:** `Authorization: Bearer <access_token>`
* **Expiry:** Access token 15 min, refresh token 7 days

---

## 3. REST Endpoints

### 3.1 Auth

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/auth/register` | Create candidate or hiring manager account | No |
| POST | `/auth/login` | Login, return tokens | No |
| POST | `/auth/refresh` | Refresh access token | Refresh token |
| POST | `/auth/logout` | Invalidate session | Yes |
| POST | `/auth/forgot-password` | Request password reset email | No |
| POST | `/auth/reset-password` | Reset password with token | No |

#### Request/Response Examples

**POST /auth/register**
```json
// Request
{
  "email": "candidate@example.com",
  "password": "secure_password",
  "name": "Jane Doe",
  "role": "candidate",
  "role_interest": "marketing"
}

// Response 201
{
  "user_id": "usr_abc123",
  "email": "candidate@example.com",
  "access_token": "eyJ...",
  "refresh_token": "eyJ...",
  "expires_in": 900
}
```

**POST /auth/login**
```json
// Request
{
  "email": "candidate@example.com",
  "password": "secure_password"
}

// Response 200
{
  "access_token": "eyJ...",
  "refresh_token": "eyJ...",
  "expires_in": 900,
  "user": {
    "user_id": "usr_abc123",
    "email": "candidate@example.com",
    "role": "candidate"
  }
}
```

---

### 3.2 Profile

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/profile` | Get current user profile | Yes |
| PATCH | `/profile` | Update profile | Yes |

**GET /profile Response**
```json
{
  "user_id": "usr_abc123",
  "email": "candidate@example.com",
  "name": "Jane Doe",
  "role": "candidate",
  "role_interest": "marketing",
  "created_at": "2025-03-17T00:00:00Z"
}
```

---

### 3.3 Assessments (Candidate)

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/assessments` | List available assessments | Yes |
| GET | `/assessments/{id}` | Get assessment details | Yes |
| POST | `/assessments/{id}/start` | Start assessment, create session | Yes |
| GET | `/assessments/sessions/{session_id}` | Get session state (progress) | Yes |
| POST | `/assessments/sessions/{session_id}/submit` | Submit module response | Yes |
| GET | `/assessments/sessions/{session_id}/result` | Get final result (after completion) | Yes |

**GET /assessments Response**
```json
{
  "assessments": [
    {
      "assessment_id": "ast_mkt001",
      "name": "Marketing AI-Driver",
      "role": "marketing",
      "description": "Assess AI proficiency for marketing roles",
      "estimated_duration_minutes": 45,
      "modules": ["sandbox", "verification", "human_plus"]
    }
  ]
}
```

**POST /assessments/{id}/start Response**
```json
{
  "session_id": "sess_xyz789",
  "assessment_id": "ast_mkt001",
  "started_at": "2025-03-17T10:00:00Z",
  "expires_at": "2025-03-17T10:45:00Z",
  "current_module": "sandbox",
  "module_state": {}
}
```

**POST /assessments/sessions/{session_id}/submit Request**
```json
{
  "module": "sandbox",
  "payload": {
    "prompt_used": "Summarize the following market report...",
    "ai_response": "...",
    "deliverable": "Brief summary document..."
  }
}
```

---

### 3.4 Sandbox (AI Interaction)

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/sandbox/chat` | Send prompt to AI, get streaming response | Yes (session) |
| POST | `/sandbox/complete` | Mark Sandbox task complete, trigger evaluation | Yes (session) |

**POST /sandbox/chat**
* **Request:** `{ "session_id": "...", "prompt": "..." }`
* **Response:** Server-Sent Events (SSE) or streaming JSON for AI output

---

### 3.5 Certifications

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/certifications` | List user's certifications | Yes |
| GET | `/certifications/{id}` | Get certification details + download URL | Yes |
| GET | `/certifications/{id}/badge` | Get shareable badge link (public) | No (public) |

**GET /certifications/{id} Response**
```json
{
  "certification_id": "cert_abc123",
  "role": "marketing",
  "issued_at": "2025-03-17T10:45:00Z",
  "candidate_name": "Jane Doe",
  "download_url": "https://...",
  "badge_url": "https://.../badge/cert_abc123"
}
```

---

### 3.6 Hiring Manager

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/hm/assessments` | List org's assessment configs | Yes (HM) |
| POST | `/hm/assessments` | Create assessment link for job | Yes (HM) |
| GET | `/hm/candidates` | List candidates who completed assessments | Yes (HM) |
| GET | `/hm/candidates/{id}` | Get candidate detail + scores | Yes (HM) |
| GET | `/hm/analytics` | Get org analytics (pass rate, completion count) | Yes (HM) |

**POST /hm/assessments Request**
```json
{
  "job_title": "Marketing Manager",
  "assessment_id": "ast_mkt001",
  "expires_at": "2025-04-17T00:00:00Z"
}
```

**Response**
```json
{
  "link_id": "link_xyz789",
  "url": "https://app.thevibenation.com/assess/link_xyz789",
  "expires_at": "2025-04-17T00:00:00Z"
}
```

---

## 4. WebSocket Events

**Endpoint:** `wss://api.thevibenation.com/v1/ws/assessment`

### 4.1 Connection
* **Query params:** `session_id`, `token` (JWT)
* **Purpose:** Real-time feedback during Sandbox evaluation

### 4.2 Server → Client Events

| Event | Payload | Description |
|-------|---------|-------------|
| `evaluation_progress` | `{ "module": "sandbox", "status": "evaluating", "progress": 0.5 }` | Evaluation in progress |
| `evaluation_complete` | `{ "module": "sandbox", "score": 0.85 }` | Module scored |
| `session_expiring` | `{ "seconds_remaining": 300 }` | Time warning |
| `error` | `{ "code": "...", "message": "..." }` | Error notification |

### 4.3 Client → Server Events

| Event | Payload | Description |
|-------|---------|-------------|
| `ping` | `{}` | Keepalive |
| `heartbeat` | `{ "timestamp": "..." }` | Session activity |

---

## 5. Error Responses

All errors follow:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable message",
    "details": { "field": "email", "reason": "invalid_format" }
  }
}
```

| HTTP | Code | Description |
|------|------|-------------|
| 400 | `VALIDATION_ERROR` | Invalid request body or params |
| 401 | `UNAUTHORIZED` | Missing or invalid token |
| 403 | `FORBIDDEN` | Insufficient permissions |
| 404 | `NOT_FOUND` | Resource not found |
| 409 | `CONFLICT` | e.g., assessment already completed |
| 429 | `RATE_LIMITED` | Too many requests |
| 500 | `INTERNAL_ERROR` | Server error |

---

## 6. Rate Limits (V1)
* **Auth:** 10 req/min per IP
* **Assessment:** 60 req/min per user
* **Sandbox/chat:** 30 req/min per session
* **HM:** 100 req/min per org

---

## 7. Versioning
* API version in path: `/v1/...`
* Breaking changes → new version (`/v2/...`)
* Deprecation: `X-API-Deprecated` header with sunset date
