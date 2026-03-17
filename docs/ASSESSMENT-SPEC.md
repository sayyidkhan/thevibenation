# Assessment Specification: AI-Driver Proficiency Platform

## 1. Overview
This document defines the structure, content, scoring logic, and rubrics for the three assessment modules: **Practical AI Task Sandbox**, **Output Verification**, and **Human-Plus (Integrated Skill Evaluation)**.

---

## 2. Module Overview

| Module | Purpose | Duration (MVP) | Scoring |
|--------|---------|---------------|---------|
| **Sandbox** | Test prompt engineering + synthesis | 15 min | Rubric-based (DSPy) |
| **Verification** | Test ability to audit AI content | 10 min | Accuracy vs ground truth |
| **Human-Plus** | Test critical thinking, creativity, ethics | 10 min | Rubric-based (DSPy) |
| **Overall** | Combined pass/fail | — | Weighted average, threshold |

---

## 3. Practical AI Task Sandbox

### 3.1 Objective
Evaluate whether the candidate can effectively use AI to perform job-specific duties: **prompt engineering** and **summarizing/synthesizing information**.

### 3.2 Task Structure
1. **Scenario:** Job-specific context (e.g., "You are a marketing manager preparing a brief for a product launch")
2. **Input:** Source material (e.g., market report, competitor analysis)
3. **Instruction:** "Use the AI tool to summarize and synthesize key insights. Submit your final deliverable."
4. **Tool:** Integrated AI (GPT-4o/Claude) with chat interface
5. **Deliverable:** Candidate submits a brief, summary, or synthesis document

### 3.3 Content Template (Marketing Example)

**Scenario:**
> You are a Marketing Manager at a tech company. Your CMO has shared a 5-page market report and asked you to produce a 1-page executive brief highlighting opportunities and risks. Use the AI tool to help you analyze and synthesize. You may iterate on your prompts. Submit your final brief.

**Input:** (Attached or inline)
> [Market report content - 5 pages]

**Expected deliverable:** 1-page executive brief (text, markdown, or structured format)

### 3.4 Rubric (DSPy Evaluation)

| Criterion | Weight | Description | Score 0–1 |
|-----------|--------|-------------|-----------|
| **Prompt quality** | 0.25 | Clarity, specificity, relevance to task | Vague → Precise |
| **Synthesis depth** | 0.30 | Key insights captured, not just surface summary | Shallow → Deep |
| **Accuracy** | 0.25 | No hallucinations, faithful to source | Incorrect → Correct |
| **Structure & clarity** | 0.20 | Readable, well-organized, actionable | Poor → Excellent |

**Pass threshold:** ≥ 0.65 average (configurable per assessment)

### 3.5 Process Metrics (Optional for MVP)
* Number of prompts used
* Time to first submission
* Iteration count (indicator of "driving" vs blind reliance)

---

## 4. Output Verification Module

### 4.1 Objective
Evaluate the candidate's ability to **audit AI-generated content**—identify inaccuracies, hallucinations, and quality issues.

### 4.2 Task Structure
1. **Content:** AI-generated text with **intentional flaws** (injected by system)
2. **Instruction:** "Review this AI-generated content. Identify all inaccuracies, hallucinations, or quality issues. For each, provide the correction or explanation."
3. **Deliverable:** List of issues + corrections

### 4.3 Flaw Types (Ground Truth)

| Flaw Type | Example | Detection Difficulty |
|-----------|---------|---------------------|
| **Factual error** | Wrong date, number, or name | Easy |
| **Hallucination** | Made-up statistic or quote | Medium |
| **Logical inconsistency** | Contradiction within text | Medium |
| **Omission of key caveat** | Missing "as of 2024" or limitation | Hard |
| **Overconfidence** | "Definitely" where evidence is weak | Hard |

### 4.4 Content Template (Marketing Example)

**Instruction:**
> Below is an AI-generated competitive analysis. It contains intentional errors. Identify each error, explain why it's wrong, and provide the correct information. Submit your findings as a structured list.

**Content:** (Pre-generated with N known flaws, e.g., 5)
> [AI-generated competitive analysis with 5 injected flaws]

### 4.5 Scoring Logic

| Metric | Formula | Description |
|--------|---------|-------------|
| **True positives** | Count of correctly identified flaws | Candidate found real flaw |
| **False positives** | Count of "flaws" that aren't real | Candidate flagged non-issues |
| **Recall** | TP / (TP + FN) | % of real flaws found |
| **Precision** | TP / (TP + FP) | % of identifications that were correct |
| **Score** | 0.5 * Recall + 0.5 * Precision | Balanced (or F1) |

**Pass threshold:** ≥ 0.60 (configurable)

### 4.6 Ground Truth Format
```json
{
  "content_id": "ver_mkt001",
  "flaws": [
    {
      "id": "f1",
      "type": "factual_error",
      "location": "paragraph 2, sentence 3",
      "incorrect": "Q4 2024 revenue was $50M",
      "correct": "Q4 2024 revenue was $45M"
    }
  ]
}
```

---

## 5. Human-Plus (Integrated Skill Evaluation)

### 5.1 Objective
Measure how AI usage interacts with **critical thinking**, **creativity**, and **ethics**—skills AI cannot easily replicate alone.

### 5.2 Task Structure
1. **Scenario:** Ethical dilemma, creative enhancement, or judgment call
2. **Instruction:** Use AI to help, but demonstrate human judgment
3. **Deliverable:** Written response (or structured answers)

### 5.3 Scenario Types

| Type | Example |
|------|---------|
| **Ethics** | "AI recommends targeting a vulnerable demographic. How do you respond?" |
| **Creativity** | "Use AI to generate 3 taglines. Which do you choose and why? How would you improve it?" |
| **Critical thinking** | "AI says X. What assumptions does it rely on? What would you verify before acting?" |

### 5.4 Content Template (Ethics Example)

**Scenario:**
> You use an AI tool to segment your marketing audience. The tool suggests prioritizing "users aged 65+ with limited digital literacy" for a high-fee financial product. How do you evaluate this recommendation? What would you do?

**Expected:** Response showing ethical reasoning, not blind adoption.

### 5.5 Rubric (DSPy Evaluation)

| Criterion | Weight | Description | Score 0–1 |
|-----------|--------|-------------|-----------|
| **Ethical awareness** | 0.35 | Recognizes dilemma, considers stakeholders | Ignorant → Nuanced |
| **Reasoning quality** | 0.35 | Logical, considers tradeoffs | Weak → Strong |
| **Actionability** | 0.30 | Clear decision + rationale | Vague → Concrete |

**Pass threshold:** ≥ 0.60

---

## 6. Overall Scoring & Pass/Fail

### 6.1 Weighted Average
| Module | Weight (MVP) |
|--------|--------------|
| Sandbox | 0.40 |
| Verification | 0.35 |
| Human-Plus | 0.25 |

**Overall score** = 0.40 × Sandbox + 0.35 × Verification + 0.25 × Human-Plus

### 6.2 Pass Conditions
* **Option A:** Overall ≥ 0.65 AND no module < 0.50
* **Option B:** Overall ≥ 0.70 (simpler)

**MVP recommendation:** Option B for simplicity.

### 6.3 Fail Handling
* Show module-level breakdown
* Allow retake after cooldown (e.g., 7 days) — configurable
* No partial certification

---

## 7. Role-Specific Content (V1 vs Later)

| Role | Sandbox Task | Verification Content | Human-Plus Scenario |
|------|--------------|---------------------|---------------------|
| **Marketing (MVP)** | Executive brief from market report | Competitive analysis with flaws | Ethics: audience targeting |
| **Legal (V2)** | Research memo synthesis | Contract clause review with errors | Ethics: confidentiality |
| **Engineering (V2)** | Data synthesis from logs | Code review with bugs | Critical thinking: AI suggestion |

---

## 8. Content Creation Workflow

1. **SME designs** scenario + input + expected output
2. **Flaw injection** (Verification): SME or script adds flaws, documents ground truth
3. **Rubric definition:** SME defines criteria + DSPy prompt
4. **Pilot testing:** Internal team runs through, calibrate thresholds
5. **Deploy** to assessment template

---

## 9. Technical Integration

### 9.1 DSPy Evaluation Flow
1. Candidate submits deliverable
2. Backend queues evaluation job
3. DSPy program runs: input = deliverable + rubric, output = scores + feedback
4. Score stored in `Score` table, linked to session
5. (Optional) WebSocket pushes progress to client

### 9.2 Verification Ground Truth
* Stored in DB or config (e.g., `verification_content` table)
* Keyed by `content_id`, includes `flaws` JSON
* Evaluation compares candidate submission to `flaws` programmatically

---

## 10. Open Questions / TBD
* Exact DSPy signature and prompts (implementation detail)
* Retake policy (free vs paid, cooldown)
* Localization / multi-language support
* Accessibility (time extensions, screen reader support)
