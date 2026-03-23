# AI Governance 6L-G Reference Panel Playground

Build an interactive reference panel that shows how real governance challenges map across all six levels of the 6L-G framework from _AI Governance_ by Bozdag & Bennati (Manning, 2026). Users select from 4 scenario threads and see how each one is handled (or should be handled) at every governance level.

## Learning Goals

By the end, the user should be able to:

- Trace any governance challenge through all six levels and identify what each level contributes
- Recognize that governance is a system -- controls at one level depend on work done at other levels
- Compare how different risk categories (privacy, security, fairness, safety) exercise different parts of the framework
- Identify which levels are most critical for which types of risk

## Core Interactive Elements

### 1. Scenario Thread Selector

Four scenario threads, each representing a different risk category:

- **GDPR Right to Erasure** (Privacy) -- a data subject requests deletion from an organization using a fine-tuned LLM. Based on Table 1.2 from the book.
- **Prompt Injection Defense** (Security) -- an attacker tries to manipulate an AI system through hidden instructions in user-provided content. Covers both direct jailbreaks and indirect injection via documents/images.
- **Model Extraction Attack** (IP/Security) -- a partner harvests a proprietary model through ordinary-looking API calls. Based on the book's startup scenario.
- **Hiring Bias Detection** (Fairness) -- an AI screening tool exhibits disparate impact against protected groups. Based on the Workday, iTutorGroup, and HireVue cases from the book.

### 2. Six-Level Flow View

The main interface: a vertical stack of the six governance levels, always visible. When a scenario is selected, each level populates with:

- **Control**: What should be in place at this level for this scenario
- **Owner**: Which role is responsible
- **Status indicators**: Whether this is a preventive control (catches the problem before it happens), detective control (catches it after), or corrective control (fixes it after detection)
- **Tools**: Specific tools from Table 1.3 that support this control

The flow should visually show dependencies -- an arrow from "Strategy & Policy" to "Implementation Review" indicating that the charter decision constrains the architecture review.

### 3. Cross-Scenario Comparison

A comparison mode where the user selects two scenarios and sees them side by side across all six levels. This reveals:

- Which levels are stressed most by each risk category (privacy stresses Strategy and Operations; security stresses Implementation and Acceptance Testing)
- Where controls overlap (monitoring is critical for all four)
- Where controls diverge (bias testing at Acceptance Testing is very different from security red-teaming)

### 4. Level Detail Panels

Clicking any level in the flow view expands a detail panel with:

- A longer explanation of the control (2-3 sentences)
- The governance question this control answers
- What failure looks like if this control is absent (connect to case studies)
- Related controls at adjacent levels (what feeds into this level, what it feeds)

### 5. Tool Catalog

A filterable reference of tools mentioned in Table 1.3, organized by governance level and scenario. Each tool entry includes:

- Name and brief description
- Which governance level it serves
- Which scenarios it applies to
- Whether it's procedural, technical, or organizational
- Link or reference for further reading

Users can filter by level, scenario, or tool type to find what's relevant to them.

## Style & UX

- Dark theme, reference-tool aesthetic
- The six-level stack is always visible as the primary navigation structure
- Scenario selector as tabs or pills at the top -- switching scenarios re-populates all six levels
- Comparison mode uses a split-pane layout
- Smooth transitions when switching scenarios (levels animate their content swap)
- Print-friendly mode that outputs the full flow for a selected scenario as a single-page reference

## Audience

Governance practitioners, compliance officers, and engineers who already understand the 6L-G framework conceptually and want to see how it applies to specific, concrete challenges. They'll use this as a reference during governance planning sessions and incident reviews.

## Source Material

- _AI Governance: Secure, privacy-preserving, ethical systems_ by Engin Bozdag & Stefano Bennati (Manning, 2026)
- Table 1.2: Applying the 6L-G model to GDPR "right to erasure" (primary reference for erasure scenario)
- Table 1.3: Overview of tools available at each governance level
- Figure 1.4: Six Levels of Generative AI Governance
- Section 1.4: Illustrative scenarios (model extraction, bank chatbot)
- Section 1.1: Prompt injection, model extraction, and data privacy risks
- Hiring bias cases: Workday (EEOC), iTutorGroup (age discrimination), HireVue (ACLU complaint), Intuit (speech recognition bias)
