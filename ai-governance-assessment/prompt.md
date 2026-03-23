# AI Governance Maturity Assessment Playground

Build an interactive governance maturity assessment tool based on the Six-Level GenAI Governance (6L-G) framework from _AI Governance_ by Bozdag & Bennati (Manning, 2026). A team lead, CTO, or compliance officer walks through their organization's AI projects and gets a maturity score across all six governance levels, with gap analysis and recommended next steps.

## Learning Goals

By the end, the user should be able to:

- Identify which of the six governance levels their organization is strong or weak on
- Map their current AI projects to a deployment model (SaaS customer, API integrator, self-hosted) and understand how that changes the governance burden
- Prioritize governance investments based on where their gaps pose the most risk
- Walk away with a concrete action list, not just a score

## Core Interactive Elements

### 1. Deployment Model Selector

Before the assessment, the user selects their primary AI deployment model:

- **SaaS Customer** -- using vendor-hosted AI tools (ChatGPT, Copilot, etc.)
- **API Integrator** -- building on top of AI APIs (calling OpenAI, Anthropic, etc.)
- **Self-Hosted / Fine-Tuned** -- running or training your own models

Each model shifts which governance controls are your responsibility vs. the vendor's. Show a responsibility matrix that updates as the user selects their model. Some controls (like model training oversight) only apply to self-hosted; others (like acceptable use policy) apply to all three.

### 2. Six-Level Assessment Questionnaire

For each of the six governance levels, present 4-6 targeted questions with multiple-choice answers scored on a maturity scale (0: not started, 1: ad-hoc, 2: defined, 3: managed, 4: optimized). Questions should be practical, not theoretical.

**Level 1: Strategy & Policy**

- Do you have an AI governance charter or responsible AI principles?
- Is there executive sponsorship and clear ownership of AI risk?
- Do you have a committee or process for reviewing high-risk AI initiatives?
- Have you defined what "responsible AI" means for your organization?

**Level 2: Risk & Impact Assessment**

- Do you classify AI use cases by risk tier before deployment?
- Do you conduct impact assessments for AI systems affecting vulnerable populations?
- Is there a formal go/no-go decision point before AI projects proceed?
- Do legal, compliance, and data science teams participate in risk assessments?

**Level 3: Implementation Review**

- Do you conduct threat modeling for AI-specific risks (prompt injection, data poisoning, model extraction)?
- Are privacy-by-design principles applied (data minimization, encryption, retention controls)?
- Do you use model cards or system cards documenting capabilities and limitations?
- Are security architects reviewing AI system designs before implementation?

**Level 4: Acceptance Testing**

- Do you perform adversarial testing (red-teaming) before deployment?
- Is bias testing performed against protected attributes?
- Do you test for hallucination and edge-case behavior under realistic conditions?
- Is there an independent review gate (not just the building team) before go-live?

**Level 5: Operations & Monitoring**

- Do you have real-time output scanning for harmful content or privacy violations?
- Is model drift detection in place (monitoring output quality over time)?
- Do you maintain decision logs and data lineage for AI outputs?
- Are there incident response playbooks specific to AI failures?

**Level 6: Learning & Improvement**

- Do you conduct post-incident reviews and feed findings back into governance?
- Is there a feedback loop from operations to policy updates?
- Do you track governance KPIs (e.g., incident rate, bias metrics, erasure SLAs)?
- Do you periodically audit whether governance controls are still effective?

### 3. Maturity Radar Chart

After completing the questionnaire, display a radar/spider chart showing the organization's maturity score across all six levels. Include:

- The user's scores as a filled polygon
- A "minimum viable governance" baseline (the floor below which you're exposed)
- Industry benchmark lines if applicable (e.g., "regulated industry baseline")
- Color coding: red zones (critical gaps), yellow (needs attention), green (adequate)

### 4. Gap Analysis Dashboard

Below the radar chart, a detailed breakdown:

- **Critical gaps** -- levels scoring 0-1, prioritized by risk severity
- **For each gap**: what the risk is, a concrete first step to address it, and which roles should own it
- **Deployment-model-specific recommendations** -- e.g., "As an API integrator, your vendor handles model security, but YOU own prompt injection defense and output validation"
- **Quick wins** -- governance improvements that can be implemented in under a week

### 5. GRC Stack Visualization

An interactive layered diagram showing how Classic GRC, AI GRC, and GenAI GRC stack on top of each other (per Figure 1.3 from the book). As the user hovers over each layer, highlight which of their assessment answers map to that layer. This helps the user understand that GenAI governance builds on -- not replaces -- existing governance.

### 6. Action Plan Generator

Based on the assessment results, generate a prioritized 30/60/90-day action plan:

- **30 days**: Address critical gaps, establish minimum viable governance
- **60 days**: Build out processes for medium-priority areas
- **90 days**: Implement continuous monitoring and improvement loops

Each action item includes: what to do, who owns it, and what "done" looks like.

## Style & UX

- Dark theme, professional dashboard aesthetic
- Radar chart should animate as scores are entered
- Progress indicator showing which level the user is assessing
- Allow going back to change answers without losing progress
- Results should be exportable (print-friendly layout or copy-to-clipboard)
- Include a "Start Over" option that clears all answers
- No jargon tooltips needed -- questions are written in plain language

## Audience

Technology leaders, compliance officers, and engineering managers responsible for AI governance at organizations that are past the "experimenting" phase and into production AI deployments. They may or may not have formal governance training, but they understand risk management concepts and want actionable output, not a lecture.

## Source Material

Framework based on the Six-Level GenAI Governance (6L-G) model and GRC concepts from:

- _AI Governance: Secure, privacy-preserving, ethical systems_ by Engin Bozdag & Stefano Bennati (Manning, 2026)
- Table 1.1: Why Generative AI Breaks Traditional Control Models
- Table 1.2: Applying the 6L-G model to GDPR "right to erasure"
- Table 1.3: Overview of tools available at each governance level
- Figure 1.3: Classic GRC compared with AI GRC and GenAI GRC
- Figure 1.4: Six Levels of Generative AI Governance
