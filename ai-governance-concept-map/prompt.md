# AI Governance Concept Map Playground

Build an interactive concept map covering the full landscape of Generative AI governance based on _AI Governance_ by Bozdag & Bennati (Manning, 2026). A clickable reference tool that lets users explore the 6L-G framework, GRC stack, deployment models, risk dimensions, and case studies -- all connected.

## Learning Goals

By the end, the user should be able to:

- Explain the three layers of the GRC stack (Classic GRC, AI GRC, GenAI GRC) and what each adds
- Navigate the six governance levels and understand what happens at each checkpoint
- Describe how deployment model (SaaS, API integrator, self-hosted) shifts governance responsibility
- Compare how GenAI risk dimensions differ from classical software and human operator risk
- Connect real-world incidents to governance failures at specific levels

## Core Interactive Elements

### 1. GRC Stack Explorer

An interactive layered diagram showing the three GRC layers stacked vertically (per Figure 1.3):

- **Classic GRC** (base): IT controls, API security
- **AI GRC** (middle): Data-lineage controls, model-training controls, privacy controls
- **GenAI GRC** (top): Real-time content generation, prompt injection risks, jailbreak attempts, semi-autonomous agents

Click any layer to expand it. Each layer shows its specific controls, who owns them, and how they relate to the layers above and below. Highlight that GenAI GRC inherits everything below it -- it doesn't replace traditional governance.

Include a "What's different about GenAI?" callout that explains why GenAI breaks the traditional playbook: unbounded output domains, probabilistic behavior, low remediation ease, opaque failure modes.

### 2. Six-Level Governance Lifecycle

An interactive vertical flow diagram of the 6L-G model (per Figure 1.4):

1. Strategy & Policy
2. Risk & Impact Assessment
3. Implementation Review
4. Acceptance Testing
5. Operations & Monitoring
6. Learning & Improvement

With a continuous feedback arrow cycling back from level 6 to level 1.

Click any level to expand a detail panel showing:

- **Purpose**: What this level accomplishes (one paragraph)
- **Key questions**: The governance questions answered at this level
- **Procedural tools**: Charters, checklists, templates, workshops
- **Technical tools**: Software and frameworks used (from Table 1.3)
- **Organizational tools**: Committees, roles, culture practices
- **Who's involved**: Which roles participate at this level

Include toggle buttons to filter the view by deployment model (SaaS / API / Self-hosted) so users can see which controls apply to their situation.

### 3. Risk Dimension Comparison Matrix

An interactive version of Table 1.1 comparing risk/control dimensions across three paradigms:

| Dimension | Classical Software | Human Operators | Generative AI |
| --------- | ------------------ | --------------- | ------------- |

Dimensions: Susceptibility to manipulation, Ease of remediation, Behavior predictability, Pre-deployment assurance, Post-launch controls, Failure detection & attribution, Memory/learning capability.

Click any cell to expand with examples and context. Highlight the GenAI column to show where traditional controls fall short:

- Low remediation (can't patch a model like you patch code)
- Low predictability (probabilistic, context-dependent, emergent)
- Opaque failure detection (failures can appear valid)

### 4. Deployment Model Navigator

Three clickable cards representing the deployment models:

- **SaaS Customer**: Using vendor-hosted AI tools
- **API Integrator**: Building on top of AI APIs
- **Self-Hosted / Fine-Tuned**: Running or training your own models

Selecting a model highlights:

- Which of the six governance levels are primarily your responsibility
- Which controls are shared with or delegated to the vendor
- Key vendor due diligence questions for that model
- The top 3 risks specific to that deployment posture

### 5. Case Study Gallery

A carousel or grid of 4-5 real-world governance failures, each as an interactive card:

- **Bank chatbot hallucination** -- junior agent relies on AI-generated fee waiver, customer files complaint
- **Model extraction attack** -- partner employee harvests proprietary model through API queries
- **Snap My AI** -- chatbot deployed to minors without adequate safety controls
- **GDPR right-to-erasure** -- deletion request that must flow through all six governance levels
- **Pieces Technologies** -- healthcare AI marketing unsubstantiated hallucination rate claims

Each card expands to show:

- What happened (2-3 sentences)
- Which governance levels failed (highlighted on a mini 6L-G diagram)
- What controls would have caught or prevented the incident
- The regulatory or reputational consequence

### 6. Connections Web

A visual network diagram showing how all the concepts connect:

- GRC layers connect to governance levels
- Governance levels connect to tools and roles
- Deployment models connect to responsibility assignments
- Case studies connect to the governance levels where failures occurred

Users can click any node to highlight its connections and see a brief description. This helps users build a mental model of how the pieces fit together rather than seeing them as isolated concepts.

## Style & UX

- Dark theme, concept-map aesthetic with clear visual hierarchy
- Smooth transitions when expanding/collapsing sections
- Breadcrumb or navigation rail showing which section the user is exploring
- Each section is self-contained but cross-linked -- clicking a governance level name in the case study view should jump to that level's detail panel
- Hover tooltips for key terms (GRC, 6L-G, CMEK, red-teaming, model card, etc.)
- No quiz or assessment -- this is a reference explorer, not a test

## Audience

Anyone studying AI governance -- from engineers building AI systems to managers responsible for oversight to students learning the field. They want to understand the full landscape and how the pieces connect, at their own pace, by clicking around and exploring.

## Source Material

- _AI Governance: Secure, privacy-preserving, ethical systems_ by Engin Bozdag & Stefano Bennati (Manning, 2026)
- Figure 1.3: Classic GRC / AI GRC / GenAI GRC stack
- Figure 1.4: Six Levels of Generative AI Governance
- Table 1.1: Why Generative AI Breaks Traditional Control Models
- Table 1.2: Applying the 6L-G model to GDPR "right to erasure"
- Table 1.3: Overview of tools available at each governance level
- Section 1.3: A Mental Model for GenAI GRC
- Section 1.4: Illustrative scenarios (bank chatbot, model extraction, Snap My AI)
