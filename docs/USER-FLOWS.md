# User Flows: AI-Driver Proficiency Platform

## 1. Overview
This document defines the primary user journeys for both personas: **Job Seekers** (candidates) and **Hiring Managers**. Each flow includes steps, decision points, and branching logic.

---

## 2. Job Seeker (Candidate) Flows

### 2.1 Discovery & Registration
| Step | Action | Outcome |
|------|--------|---------|
| 1 | Land on platform (organic, referral, or job posting link) | Views value prop and available role certifications |
| 2 | Browse available certifications (Legal, Marketing, Engineering, etc.) | Selects role that matches their career |
| 3 | Click "Take Assessment" or "Get Certified" | Redirected to signup/login |
| 4 | Sign up (email + password) or OAuth (LinkedIn/Google) | Account created, session started |
| 5 | Complete profile (name, role interest, optional LinkedIn) | Profile saved, ready for assessment |

**Branch:** If user already has account → Login → Skip to Assessment Selection

---

### 2.2 Assessment Selection & Preparation
| Step | Action | Outcome |
|------|--------|---------|
| 1 | View dashboard with available assessments | Sees role-based options and estimated duration |
| 2 | Select role certification (e.g., "Marketing AI-Driver") | Assessment details displayed |
| 3 | Review assessment structure (Sandbox + Output Verification + Human-Plus) | Informed consent |
| 4 | Accept terms / start assessment | Session created, timer begins |

**Branch:** If user has incomplete assessment → Resume or Restart option

---

### 2.3 Practical AI Task Sandbox
| Step | Action | Outcome |
|------|--------|---------|
| 1 | Enter Sandbox environment | Sees job-specific scenario (e.g., "Draft a marketing brief using AI") |
| 2 | Read task instructions and context | Understands expected deliverables |
| 3 | Use integrated AI tool to perform task (prompt, iterate, refine) | AI responses stream in real time |
| 4 | Submit deliverable (e.g., brief, summary, synthesis) | Submission recorded, evaluated asynchronously |
| 5 | Receive live feedback via WebSocket (optional) | May see progress indicators |

**Branch:** If timeout → Grace period or auto-submit with partial credit

---

### 2.4 Output Verification Module
| Step | Action | Outcome |
|------|--------|---------|
| 1 | View AI-generated content with intentional flaws | Content displayed (e.g., report, analysis) |
| 2 | Identify inaccuracies, hallucinations, or quality issues | Candidate marks/annotates flaws |
| 3 | Submit verification (list of issues found + corrections) | Submission scored against ground truth |
| 4 | See immediate or deferred score | Feedback on accuracy |

**Branch:** If candidate fails verification → May retake (policy TBD) or proceed with lower score

---

### 2.5 Human-Plus (Integrated Skill Evaluation)
| Step | Action | Outcome |
|------|--------|---------|
| 1 | Receive scenario (e.g., ethical dilemma, creative enhancement) | Scenario displayed |
| 2 | Answer questions or complete task using AI + human judgment | Response submitted |
| 3 | Evaluation of critical thinking, creativity, ethics | Score combined with other modules |

---

### 2.6 Results & Certification
| Step | Action | Outcome |
|------|--------|---------|
| 1 | View overall score and module breakdown | Pass/Fail + detailed feedback |
| 2 | If **Pass:** Download certificate, add badge to LinkedIn/CV | Certification issued, shareable link generated |
| 3 | If **Fail:** View retake policy, schedule retake (if allowed) | Option to retry after cooldown |
| 4 | Access certification dashboard | Can view, share, or re-download badge |

**Branch:** Pass → Certification flow | Fail → Retake or feedback-only

---

## 3. Hiring Manager Flows

### 3.1 Onboarding & Account Setup
| Step | Action | Outcome |
|------|--------|---------|
| 1 | Land on platform (sales, partner, or direct signup) | Views B2B value prop |
| 2 | Request demo or sign up for organization account | Contact captured or account created |
| 3 | Complete org profile (company, industry, team size) | Org settings saved |
| 4 | Add team members (optional) | Invites sent |

---

### 3.2 Job Posting & Assessment Configuration
| Step | Action | Outcome |
|------|--------|---------|
| 1 | Create job posting or open role | Role details entered |
| 2 | Select required certification(s) for the role | e.g., "Marketing AI-Driver" mandatory |
| 3 | Configure assessment link (unique URL per job) | Link generated for candidates |
| 4 | Share link in job description, ATS, or email | Candidates receive link |

---

### 3.3 Candidate Evaluation
| Step | Action | Outcome |
|------|--------|---------|
| 1 | Receive notification when candidate completes assessment | Alert in dashboard or email |
| 2 | View candidate list with certification status | Filter by pass/fail, role, date |
| 3 | Drill into candidate report | See scores, process data, audit log |
| 4 | Compare candidates (optional) | Side-by-side or ranked view |
| 5 | Export or share report with hiring team | PDF/CSV export |

**Branch:** Certified → Proceed to interview | Not certified → Reject or request retake

---

### 3.4 Reporting & Analytics
| Step | Action | Outcome |
|------|--------|---------|
| 1 | Access org analytics dashboard | Aggregate metrics visible |
| 2 | View certification adoption, pass rates, role breakdown | Data for hiring strategy |
| 3 | Export data for compliance or reporting | CSV/API access |

---

## 4. Cross-Cutting Flows

### 4.1 Authentication
- **Login:** Email/password or OAuth → Session token (JWT)
- **Logout:** Invalidate session
- **Password reset:** Email link → New password
- **Role-based access:** Candidate vs Hiring Manager views

### 4.2 Error & Edge Cases
- **Session timeout during assessment:** Save progress, allow resume
- **AI service failure:** Retry, fallback message, or defer evaluation
- **Network disconnect:** Offline indicator, auto-reconnect, queue submissions

---

## 5. Flow Summary Diagram (Text)

```
JOB SEEKER:
  Discover → Register → Select Assessment → Sandbox → Output Verification → Human-Plus → Results → [Pass: Certify | Fail: Retake]

HIRING MANAGER:
  Onboard → Configure Job → Share Link → [Candidates Complete] → Review Results → Export/Decide
```
