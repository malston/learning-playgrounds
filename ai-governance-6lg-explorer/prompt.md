# AI Governance 6L-G Deep Dive Explorer

Build an interactive deep-dive explorer for the Six-Level GenAI Governance (6L-G) framework from _AI Governance_ by Bozdag & Bennati (Manning, 2026). Users click through each governance level, explore the questions and tools at each checkpoint, filter by deployment model, and follow a GDPR erasure request through the entire framework as a guided walkthrough.

## Learning Goals

By the end, the user should be able to:

- Explain what each of the six governance levels does and what questions it answers
- Identify which procedural, technical, and organizational tools apply at each level
- Understand how deployment model (SaaS, API integrator, self-hosted) shifts which controls are your responsibility
- Trace a GDPR right-to-erasure request through all six levels and explain what each level contributes to handling it
- Recognize that governance is a continuous cycle, not a linear checklist

## Layout

Two-panel layout:

- **Left panel (~35% width)**: Vertical flowchart of the six governance levels with CSS-drawn downward arrows between nodes. A curved SVG arrow along the left side of the flowchart runs from Level 6 back to Level 1, labeled "Continuous Feedback." GDPR walkthrough mode toggle at the bottom of the left panel.
- **Right panel (~65% width)**: Detail view that populates when a level is clicked.
- **Header bar**: Title and deployment model filter (three toggle pills: SaaS / API Integrator / Self-Hosted).

**Initial state**: On first load, Level 1 (Strategy & Policy) is pre-selected and its detail view is shown in the right panel. The deployment filter defaults to no filter active (all controls visible). Walkthrough mode is off.

**Deployment filter behavior**: Single-select. Clicking a pill activates that filter and deactivates any other. Clicking the active pill again deactivates it, returning to the unfiltered state. When no filter is active, all controls are shown at full opacity.

## Left Panel -- Flowchart Navigation

Six level nodes stacked vertically. Each node shows:

- Level number and name
- One-line summary (e.g., "Charter, sponsorship, principles")
- A subtle color accent unique to each level, consistent throughout the playground

Clicking a node highlights it and populates the right panel. The previously selected node dims back.

### Walkthrough Mode

When the GDPR walkthrough is toggled on:

- Each node gets an erasure-specific badge/annotation (e.g., Level 1 shows "Charter forbids training on PII without consent")
- Nodes gain a sequential indicator (Step 1 of 6, Step 2 of 6...)
- A "Next" / "Previous" control appears below the flowchart to step through linearly
- The user can still click any node directly to jump around

### Deployment Filter on Left Panel

When a filter is active, levels where all controls are vendor-responsibility get a dimmed treatment (not hidden -- visually deprioritized with a note like "Primarily vendor responsibility").

## Right Panel -- Detail View

### General Mode

When a level is clicked, the right panel shows:

**Level header**: Level number, name, and a one-paragraph purpose statement.

**Governance questions**: 4-5 expandable rows per level. Collapsed state shows the question text with a chevron. Expanded state reveals:

- **Tools** as small pills/tags, categorized by type:
  - Procedural (blue pills): Charters, checklists, templates, workshops
  - Technical (orange pills): Software tools and frameworks
  - Organizational (green pills): Committees, roles, culture practices
- **Who owns this**: Role label (security architect, privacy engineer, product manager, etc.)
- A brief sentence connecting the question to why it matters

### Walkthrough Mode

When GDPR walkthrough is active, the right panel retains the level header and purpose statement but replaces the governance questions section with the erasure-specific content:

- Each level shows three clearly labeled sections:
  - **The control**: What should be in place (e.g., "Red-team tries membership-inference attacks on deleted records")
  - **In practice**: A concrete example of what this looks like
  - **If skipped**: What goes wrong when this level is missing from the erasure process
- The erasure content provided in this spec is the complete set -- no external lookup of Table 1.2 is needed
- The user sees the deletion request flowing through the governance system as a coherent narrative

### Deployment Filter on Right Panel

Questions that don't apply to the selected deployment model get a "Not applicable -- vendor responsibility" treatment. Still visible (so the user learns what exists) but visually deprioritized with an explanation of why.

## Content -- Six Governance Levels

### Level 1: Strategy & Policy

**Purpose**: The policies, leadership, and oversight that guide AI use. Includes Responsible AI principles, governance committee, executive sponsorship, and ownership of AI risk.

**Questions**:

1. Do you have an AI governance charter or responsible AI principles?
   - Tools: Responsible AI Charters (procedural), Policy Workshops (procedural), Cross-functional committees (organizational)
   - Owner: Executive leadership, legal
   - Applies to: SaaS, API Integrator, Self-hosted

2. Is there executive sponsorship and clear ownership of AI risk?
   - Tools: Accountability mapping (organizational)
   - Owner: C-suite, board
   - Applies to: SaaS, API Integrator, Self-hosted

3. Do you have a committee or process for reviewing high-risk AI initiatives?
   - Tools: Risk Tiers (procedural), Cross-functional decision bodies (organizational)
   - Owner: Governance committee
   - Applies to: SaaS, API Integrator, Self-hosted

4. Have you defined what "responsible AI" means for your organization?
   - Tools: Responsible AI Charters (procedural)
   - Owner: Executive leadership, ethics lead
   - Applies to: SaaS, API Integrator, Self-hosted

**Erasure walkthrough**: Charter forbids training on identifiable user data without consent; policy states erased data must be expunged from models. In practice, the governance charter explicitly names "right to erasure" as a supported data subject right and assigns ownership to the DPO. If skipped, teams have no mandate to act on erasure requests -- each request becomes an ad-hoc crisis.

### Level 2: Risk & Impact Assessment

**Purpose**: Classify the risk profile of a proposed AI system and make a go/no-go decision. Multiple roles contribute: product managers run impact worksheets, legal reviews vendor compliance, data scientists outline data handling.

**Questions**:

1. Do you classify AI use cases by risk tier before deployment?
   - Tools: Regulatory Checklists (procedural), Risk assessment questionnaires (technical), Cross-functional decision bodies (organizational)
   - Owner: Product managers, legal, compliance
   - Applies to: SaaS, API Integrator, Self-hosted

2. Do you conduct impact assessments for AI systems affecting vulnerable populations?
   - Tools: Impact Assessment Templates (procedural)
   - Owner: Legal, ethics lead, product
   - Applies to: API Integrator, Self-hosted

3. Is there a formal go/no-go decision point before AI projects proceed?
   - Tools: Risk Tiers (procedural)
   - Owner: Governance committee, executive sponsor
   - Applies to: SaaS, API Integrator, Self-hosted

4. Do legal, compliance, and data science teams participate in risk assessments?
   - Tools: Cross-functional decision bodies (organizational)
   - Owner: Cross-functional team
   - Applies to: API Integrator, Self-hosted

**Erasure walkthrough**: Data-mapping proves which tables or vendor logs might hold personal data; privacy impact assessment flags erasure as high priority. In practice, the data steward produces a map showing every system where the user's data lives -- including model training logs and embedding stores. If skipped, you can't delete what you can't find.

### Level 3: Implementation Review

**Purpose**: The technical deep-dive. Threat modeling, privacy-by-design, security architecture review, model documentation. Think of this as obtaining planning permission for a building.

**Questions**:

1. Do you conduct threat modeling for AI-specific risks (prompt injection, data poisoning, model extraction)?
   - Tools: Threat Modeling Sessions (procedural), AI Firewalls (technical), "Privacy by Design" training (organizational)
   - Owner: Security architects, ML engineers
   - Applies to: API Integrator, Self-hosted

2. Are privacy-by-design principles applied (data minimization, encryption, retention controls)?
   - Tools: Customer-Managed Encryption Keys / CMEK (technical), Design Reviews (procedural)
   - Owner: Privacy engineers, security architects
   - Applies to: API Integrator, Self-hosted

3. Do you use model cards or system cards documenting capabilities and limitations?
   - Tools: Model Cards (technical), System Cards (technical)
   - Owner: ML engineers, product managers
   - Applies to: Self-hosted (verify vendor provides for SaaS/API Integrator)

4. Are security architects reviewing AI system designs before implementation?
   - Tools: Design Reviews (procedural), Red-teaming exercises (organizational)
   - Owner: Security architects
   - Applies to: API Integrator, Self-hosted

**Erasure walkthrough**: Architecture review validates hashing (one-way scrambling) for IDs; pipelines pseudonymize data before fine-tune; models are version-tagged for selective retrain. In practice, the architecture ensures personal data is never stored in a form that can't be traced and removed -- every pipeline step is auditable. If skipped, personal data gets baked into model weights with no extraction path.

### Level 4: Acceptance Testing

**Purpose**: The design-freeze gate. The feature isn't allowed to move to production until mitigations are demonstrably built and validated. Independent verification, not just self-assessment.

**Questions**:

1. Do you perform adversarial testing (red-teaming) before deployment?
   - Tools: Promptfoo (technical), Garak (technical), Adversarial Robustness Toolbox / ART (technical), External expert reviews (organizational)
   - Owner: Red team, external auditors
   - Applies to: API Integrator, Self-hosted

2. Is bias testing performed against protected attributes?
   - Tools: PyRIT (technical), Simulation workshops (organizational)
   - Owner: Applied scientists, fairness team
   - Applies to: API Integrator, Self-hosted

3. Do you test for hallucination and edge-case behavior under realistic conditions?
   - Tools: Promptfoo (technical), Go/No-Go Criteria (procedural)
   - Owner: QA, product managers
   - Applies to: SaaS (verify vendor does this), API Integrator, Self-hosted

4. Is there an independent review gate (not just the building team) before go-live?
   - Tools: Test Plans (procedural), Go/No-Go Criteria (procedural), External expert reviews (organizational)
   - Owner: GenAI Steering Committee
   - Applies to: API Integrator, Self-hosted

**Erasure walkthrough**: Red-team tries membership-inference attacks on deleted records (guessing whether a record is in the model); model must refuse or return "not in corpus." In practice, after a deletion, testers probe the model with queries designed to surface memorized personal data. The model must demonstrate it no longer reproduces or confirms the deleted information. If skipped, you claim data is deleted but can't prove it.

### Level 5: Operations & Monitoring

**Purpose**: Continuous oversight once the system is live. Runtime guardrails, output scanning, drift detection, incident response, and data lineage. Also covers end-of-life and decommissioning.

**Questions**:

1. Do you have real-time output scanning for harmful content or privacy violations?
   - Tools: Real-time output scanners (technical), Monitoring Policies (procedural), Incident escalation paths (organizational)
   - Owner: Operations team, security
   - Applies to: API Integrator, Self-hosted

2. Is model drift detection in place (monitoring output quality over time)?
   - Tools: Drift-detection dashboards (technical), MLFlow Tracking (technical)
   - Owner: ML engineers, data scientists
   - Applies to: Self-hosted (request from vendor for API Integrator)

3. Do you maintain decision logs and data lineage for AI outputs?
   - Tools: MLFlow Tracking (technical), Incident Response Playbooks (procedural)
   - Owner: Operations, compliance
   - Applies to: API Integrator, Self-hosted

4. Are there incident response playbooks specific to AI failures?
   - Tools: Incident Response Playbooks (procedural), Post-incident reviews (organizational)
   - Owner: Operations, security, legal
   - Applies to: SaaS, API Integrator, Self-hosted

**Erasure walkthrough**: Privacy inbox (erasure requests made by individuals) feeds an automated job that black-lists the user's embeddings and schedules a retrain; logs confirm completion. In practice, the ops team runs the deletion pipeline, verifies the data is purged from all stores including vector databases and training queues, and generates an audit trail. If skipped, erasure requests pile up with no tracking, no SLA, and no proof of completion.

### Level 6: Learning & Improvement

**Purpose**: Feedback loops. Customer complaints, red-team findings, fresh regulations, and post-incident reviews feed into charter revisions, policy refreshes, and next-sprint engineering work.

**Questions**:

1. Do you conduct post-incident reviews and feed findings back into governance?
   - Tools: Feedback Loops (procedural), Lessons learned sessions (organizational)
   - Owner: Product owners, governance committee
   - Applies to: SaaS, API Integrator, Self-hosted

2. Is there a feedback loop from operations to policy updates?
   - Tools: Policy Update Protocols (procedural), Culture of transparency (organizational)
   - Owner: Governance committee, DPO
   - Applies to: SaaS, API Integrator, Self-hosted

3. Do you track governance KPIs (e.g., incident rate, bias metrics, erasure SLAs)?
   - Tools: Trust score dashboards (technical), Calibration metrics (technical)
   - Owner: Governance committee, data team
   - Applies to: API Integrator, Self-hosted

4. Do you periodically audit whether governance controls are still effective?
   - Tools: Feedback Loops (procedural), Policy Update Protocols (procedural)
   - Owner: Internal audit, governance committee
   - Applies to: SaaS, API Integrator, Self-hosted

**Erasure walkthrough**: Quarterly review shows erasure SLA (service-level-agreement target), e.g., > 90%; committee funds research into faster "machine unlearning" to hit 99%. In practice, the governance committee reviews erasure metrics, identifies bottlenecks (e.g., retraining takes 3 weeks), and invests in process or tooling improvements. If skipped, the erasure process never improves -- response times degrade as request volume grows.

## Style & UX

- Dark theme (background ~#1a1a2e, text ~#e0e0e0), clean and professional
- Level accent colors (muted, readable on dark backgrounds):
  - Level 1: steel blue (#4a8abf)
  - Level 2: sage green (#4a8a5f)
  - Level 3: amber (#8a6a3f)
  - Level 4: rose (#8a4a6f)
  - Level 5: purple (#6a4a8a)
  - Level 6: teal (#4a6a8a)
- Smooth transitions when switching levels (detail panel content fades/slides)
- Walkthrough mode toggle is visually prominent -- a pill button in the left panel that changes color when active
- Tool pills use three colors by type: procedural (#4a8abf blue), technical (#bf8a4a orange), organizational (#4abf6a green)
- Deployment filter pills use a selected/unselected toggle pattern
- Hover tooltips on domain terms -- the implementer should write brief definitions for terms that appear in the content (GRC, CMEK, DPO, red-teaming, model card, membership inference, prompt injection, data poisoning, model extraction, pseudonymization, embeddings, machine unlearning, data lineage, etc.)
- Responsive: below 768px, stack panels vertically (flowchart on top, detail below)
- Keyboard accessible: arrow keys navigate between levels, Enter/Space expands questions, Tab moves through interactive elements
- No external dependencies -- single self-contained HTML file

## Audience

Engineers, product managers, and governance practitioners exploring the 6L-G framework for the first time or using it as a reference. They want to understand what happens at each governance checkpoint, what tools exist, and how the framework applies to a real scenario. The deployment model filter lets them focus on what's relevant to their situation.

## Source Material

- _AI Governance: Secure, privacy-preserving, ethical systems_ by Engin Bozdag & Stefano Bennati (Manning, 2026)
- Figure 1.4: Six Levels of Generative AI Governance
- Table 1.2: Applying the 6L-G model to GDPR "right to erasure"
- Table 1.3: Overview of tools available at each governance level
- Section 1.2: GRC for AI -- More Than a Compliance Checklist
- Section 1.3: A Mental Model for GenAI GRC
- Section 1.5: Tools and Practices You'll Need
