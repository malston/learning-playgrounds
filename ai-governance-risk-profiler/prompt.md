# AI Governance Risk Profiler Playground

Build an interactive risk profiling tool based on the Six-Level GenAI Governance (6L-G) framework from _AI Governance_ by Bozdag & Bennati (Manning, 2026). A product manager, engineer, or compliance officer inputs details about a specific AI deployment and gets a risk profile with recommended controls mapped to each governance level.

## Learning Goals

By the end, the user should be able to:

- Classify their AI use case into a risk tier (low, medium, high, unacceptable) with reasoning
- Understand which deployment model they're operating under and what that means for responsibility
- Map specific risks to the governance level where they should be addressed
- Walk away with a concrete control checklist tailored to their use case

## Core Interactive Elements

### 1. Use Case Input Wizard

A guided 5-step questionnaire to profile the AI deployment:

**Step 1: What does the AI do?**

- Content generation (text, images, code)
- Decision support (recommendations, classifications)
- Autonomous action (automated approvals, transactions, communications)
- Information retrieval (search, summarization, Q&A)
- Custom (free text description)

**Step 2: Who is affected?**

- Internal employees only
- External customers (B2B)
- General public (B2C)
- Vulnerable populations (minors, patients, job applicants, financial consumers)

**Step 3: What data does it touch?**
Multi-select from:

- Public/non-sensitive data
- Internal business data
- Personally identifiable information (PII)
- Protected health information (PHI)
- Financial data (credit scores, transactions)
- Biometric data
- Data from minors

**Step 4: Deployment model**

- SaaS customer (using a vendor's hosted tool)
- API integrator (building on AI APIs)
- Self-hosted / fine-tuned model
- Hybrid (multiple models from different sources)

**Step 5: Industry and regulatory context**
Multi-select:

- Healthcare (HIPAA, FDA)
- Financial services (SOX, GLBA, fair lending)
- EU operations (EU AI Act, GDPR)
- Education (FERPA, COPPA)
- Government / public sector
- General commercial (FTC, state laws)
- No specific regulatory requirements

### 2. Risk Classification Engine

Based on the inputs, calculate and display a risk tier with an explanation of the scoring factors:

- **Unacceptable** -- prohibited under EU AI Act or clearly harmful (e.g., social scoring, real-time biometric surveillance). Show a stop sign and explain why this deployment should not proceed without fundamental redesign.
- **High Risk** -- triggers mandatory compliance obligations. Autonomous decisions affecting vulnerable populations, healthcare diagnostics, financial credit scoring, hiring/screening.
- **Medium Risk** -- customer-facing content generation, internal decision support with PII, B2B applications with business-critical outputs.
- **Low Risk** -- internal productivity tools, summarization of public data, code generation for internal use.

Show which input factors pushed the score up or down, so the user understands the reasoning.

### 3. Risk Map Visualization

An interactive matrix with:

- **Y-axis**: Risk categories (Security, Privacy, Bias/Fairness, Reliability/Hallucination, Compliance, Reputational)
- **X-axis**: The six governance levels where each risk should be addressed
- **Cells**: Filled with specific risks and controls relevant to the user's profile

For example, if the user selected "healthcare" + "PHI" + "API integrator":

- Security row, Implementation Review column: "Validate API data-in-transit encryption, implement CMEK for stored data"
- Privacy row, Operations column: "Deploy PHI detection in output scanning, enforce retention limits per HIPAA"
- Bias row, Acceptance Testing column: "Test for demographic disparities in diagnostic suggestions"

Clicking a cell expands to show the specific control, who owns it, and what tools can help (referencing Table 1.3 from the book).

### 4. Responsibility Assignment Matrix

Based on the deployment model, show a RACI-style matrix:

| Control Area | Model Builder | API Provider | Integrator (You) | End-User Org |
| ------------ | ------------- | ------------ | ---------------- | ------------ |

Highlight where the user sits in the chain and what they're accountable for vs. what they can delegate or verify through vendor due diligence.

For SaaS customers: emphasize acceptable use policies, vendor vetting, output review.
For API integrators: emphasize prompt injection defense, output validation, data handling.
For self-hosted: the full stack is your responsibility.

### 5. Control Checklist Generator

Output a prioritized checklist of controls organized by governance level. For each control:

- **What**: The specific control (e.g., "Implement output scanning for PHI leakage")
- **Why**: The risk it mitigates
- **Priority**: Critical / High / Medium / Low (based on the risk profile)
- **Owner**: Suggested role (security, privacy, data science, legal, product)
- **Tools**: Specific tools that can help (from Table 1.3: Promptfoo, Garak, ART, MLFlow, etc.)

Include checkboxes so the user can track progress. Make the checklist copy-friendly.

### 6. Scenario Comparison

Let the user save their current profile and modify inputs to see how the risk profile changes. For example:

- "What if we move from API integrator to self-hosted?"
- "What if we add PHI to the data scope?"
- "What if we expand from internal to customer-facing?"

Show the before/after risk tiers and highlight which controls are added or removed.

## Style & UX

- Dark theme, dashboard aesthetic matching a risk management tool
- Risk tier badges with clear color coding (red/orange/yellow/green)
- The wizard should feel fast -- no more than 2 minutes to complete
- Results should be scannable -- use severity indicators, not walls of text
- The control checklist should be exportable (print-friendly or copy-to-clipboard)
- Hovering over risk categories shows a one-line definition
- Allow re-running the wizard without losing previous results (comparison mode)

## Audience

Product managers evaluating whether an AI feature is ready for production, engineers implementing AI systems who need to know what controls to build, and compliance officers reviewing AI deployments. They understand their domain but may not have deep governance expertise. They want a fast, specific answer -- "for YOUR use case, HERE are the risks and HERE is what to do about them."

## Source Material

Framework based on the Six-Level GenAI Governance (6L-G) model and risk concepts from:

- _AI Governance: Secure, privacy-preserving, ethical systems_ by Engin Bozdag & Stefano Bennati (Manning, 2026)
- Table 1.1: Why Generative AI Breaks Traditional Control Models (risk dimensions across classical software, human operators, and GenAI)
- Table 1.2: Applying the 6L-G model to GDPR "right to erasure" (lifecycle-mapped controls)
- Table 1.3: Overview of tools available at each governance level (procedural, technical, organizational)
- Figure 1.3: Classic GRC / AI GRC / GenAI GRC stack
- Figure 1.4: Six Levels of Generative AI Governance
- Section 1.4: Illustrative scenarios (bank chatbot, model extraction, Snap My AI)
- EU AI Act risk classification tiers
- NIST AI Risk Management Framework
- ISO/IEC 42001 AI management system standard
