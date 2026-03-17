# TECH-STACK.md: AI-Driver Proficiency Platform (V1)

## 1. Overview
The technology stack is designed to support the shift from credential-based hiring to competency-based evaluation. The goal is to build a platform that tests a candidate's ability to "drive" AI tools through Practical AI Tasks and Output Verification.

## 2. Frontend Layer (The Assessment Workspace)
* **Framework:** TanStack Start / TanStack Router & Query
    * **Reasoning:** Provides a type-safe, highly controlled environment for building complex "Sandbox" simulations where candidates perform job-specific duties like writing prompts or summarizing information.
* **State Management:** TanStack Query for efficient data fetching and synchronization during real-time assessments.
* **Styling:** Tailwind CSS for rapid UI development of role-specific assessment dashboards (e.g., Legal, Marketing, Engineering).

## 3. AI Orchestration & Evaluation (Beyond LangChain)
* **Primary SDK:** Vercel AI SDK
    * **Reasoning:** Optimized for building streaming AI interfaces, essential for the interactive nature of Practical AI Tasks.
* **Evaluation Logic:** DSPy (Declarative Self-improving Language Programs)
    * **Reasoning:** Moves away from brittle "chains" to programmatic logic. This is critical for building rigorous grading systems that evaluate accuracy and quality in the Output Verification module.
* **Models:** GPT-4o (OpenAI) and Claude 3.5 Sonnet (Anthropic)
    * **Reasoning:** High-reasoning models needed to simulate complex scenarios that test critical thinking, creativity, and ethics alongside AI skills.

## 4. Backend & API Layer
* **Framework:** FastAPI (Python)
    * **Reasoning:** Offers high performance and native compatibility with AI libraries (like DSPy) needed to synthesize candidate performance data.
* **Real-time Communication:** WebSockets for live feedback during the "Sandbox" evaluation phases.

## 5. Infrastructure & Security
* **Cloud Provider:** AWS (Amazon Web Services)
    * **Reasoning:** Specifically identified in the sources as a major cloud provider that will likely define the rise in standardized certifications for AI proficiency.
* **Database:**
    * **PostgreSQL:** For storing structured candidate assessments, scores, and metadata.
    * **Redis:** For managing session states during the high-concurrency assessment windows.

## 6. Certification & Integration
* **Standardized Certification Engine:** A custom module to generate role-based badges that meet the predicted 2027 requirement for mandatory AI proficiency testing.
* **Audit Logging:** Comprehensive logging of candidate prompts and AI responses to verify that the candidate is truly "driving" the tool rather than relying on it blindly.