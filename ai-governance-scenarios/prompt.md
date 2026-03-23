# AI Governance Scenario Explorer Playground

Build an interactive scenario-driven explorer that teaches AI governance through real-world failures. Based on _AI Governance_ by Bozdag & Bennati (Manning, 2026). Users explore 6 governance failure scenarios, map breakdowns to the 6L-G framework, and discover what controls would have prevented each incident.

## Learning Goals

By the end, the user should be able to:

- Trace a governance failure from its visible symptom back to the governance level where it should have been caught
- Explain why GenAI incidents are harder to detect and remediate than traditional software failures
- Identify which controls at which governance levels prevent specific categories of failure
- Recognize common patterns across incidents (skipped acceptance testing, absent monitoring, missing policy)

## Core Interactive Elements

### 1. Scenario Selector

A grid of 6 scenario cards, each with a one-line hook and a severity badge:

- **"The Bot That Waived Fees"** (Financial / Hallucination) -- A bank's AI copilot hallucinated a fee waiver policy, and no one caught it until a customer filed a complaint.
- **"The Quiet Heist"** (Security / Model Extraction) -- A partner's employee harvested a startup's proprietary model through ordinary-looking API calls. The dashboards stayed green.
- **"My AI Said What?"** (Safety / Vulnerable Populations) -- Snap deployed a chatbot to millions of users including minors. It gave tips on masking alcohol and cannabis odors to 13-year-olds.
- **"Delete Me From Your Model"** (Privacy / GDPR Erasure) -- A GDPR right-to-erasure request hits an organization using a fine-tuned LLM. Deleting the database row is trivial. Deleting the influence on model weights is not.
- **"0.001% Hallucination Rate"** (Compliance / Deceptive Claims) -- A healthcare AI vendor marketed near-perfect accuracy without substantiation. Texas opened a deceptive trade practices investigation.
- **"Whose Red Lines?"** (Defense / Builder vs. Deployer Governance) -- Anthropic drew red lines on autonomous weapons and mass surveillance. The Department of Warfighting said those red lines made Anthropic an "unacceptable risk to national security." OpenAI took the contract instead.

### 2. Scenario Deep Dive

When a user selects a scenario, expand into a multi-panel view:

**Panel A: What Happened**
A narrative timeline (3-5 steps) showing the incident unfold. Each step is revealed sequentially with a "Next" button so the user can follow the chain of events. Use clear, factual language -- no dramatization.

**Panel B: The 6L-G Failure Map**
An interactive version of the six governance levels with each level color-coded:

- **Red**: This level failed -- the control was absent or inadequate
- **Yellow**: This level partially addressed the risk but had gaps
- **Green**: This level was not relevant to this incident
- **Gray**: Unknown / not enough information

Click any red or yellow level to see:

- What should have been in place at this level
- What was actually in place (or missing)
- The specific control that would have caught or prevented the incident

**Panel C: The Fix**
For each failed governance level, show the concrete control that would address it:

- The control name and description
- Who owns implementing it (role)
- What tools support it (from Table 1.3)
- How it connects to the other levels (governance is a system, not isolated checkboxes)

### 3. Pattern Recognition Engine

After exploring 2+ scenarios, unlock a "Patterns" tab that highlights recurring themes:

- **Skipped acceptance testing** -- appears in bank chatbot (no adversarial prompts tested), Snap My AI (no edge-case testing with minors)
- **Absent monitoring** -- appears in model extraction (no anomaly detection on API calls), bank chatbot (no confidence scoring on outputs)
- **Missing strategy & policy** -- appears in Snap My AI (no policy for vulnerable populations), healthcare vendor (no policy on accuracy claims), DoW/Anthropic (conflicting charters between builder and deployer)
- **Governance-deployment mismatch** -- controls designed for one deployment model applied to a different one
- **Builder vs. deployer governance conflict** -- appears in DoW/Anthropic (who controls red lines once a model is on classified networks?), and echoes in the model extraction scenario (vendor's safeguards vs. integrator's operational context)

Show these as a matrix: patterns (rows) vs. scenarios (columns), with checkmarks where each pattern appears. This helps users see that governance failures are systematic, not random.

### 4. "What Would You Do?" Interactive

For each scenario, before revealing the failure analysis, present the user with a challenge:

- Show only the "What Happened" timeline
- Ask: "At which governance level(s) should this have been caught?"
- Let the user click the governance levels they think failed
- Reveal the actual answer and compare with the user's assessment
- Show a brief explanation of any levels the user missed or incorrectly flagged

This transforms passive reading into active analysis. Not graded -- it's a self-check, not a quiz.

### 5. Build-Your-Own Scenario

An empty scenario template where the user can:

- Describe an AI deployment in their organization (free text or guided prompts)
- Walk through each governance level and self-assess: "Do we have this control in place?"
- Flag their own red and yellow levels
- See which of the five case study patterns their scenario matches

This bridges from "learning about governance failures" to "applying governance to my own work."

### 6. Regulatory Consequence Tracker

For each scenario, show the actual or projected regulatory consequences:

- Which laws or regulations were violated (GDPR, FTC Act, state consumer protection, EU AI Act)
- What enforcement action was taken (fines, injunctions, required disclosures, model destruction)
- The financial and reputational cost
- A timeline from incident to resolution

Include a callout: "The FTC v. Everalbum case (2021) was the first U.S. enforcement action requiring model destruction -- regulators can demand you delete not just data but the model trained on it."

### 7. "Whose Red Lines?" -- Scenario Deep Dive Notes

This scenario is unique because it's not a governance failure in the traditional sense -- it's a governance _conflict_ between the model builder (Anthropic) and the model deployer (DoW via Palantir's Maven Smart System). Use this scenario to explore a dimension the other five don't cover: what happens when two parties in the AI supply chain have incompatible governance frameworks?

**Timeline:**

1. Anthropic deploys Claude on DoW classified networks, integrated through Palantir's Maven Smart System. Claude is used for intelligence analysis, target selection, and target prioritization -- including in operations against Iran (producing ~1,000 targets on the first day, roughly 2x the 2003 shock-and-awe campaign in Iraq).
2. Anthropic draws two red lines: no autonomous weapon systems (weapons that select and engage targets without human intervention), and no mass surveillance of Americans. These are charter-level governance controls (Level 1).
3. The DoW demands broader operational latitude -- the ability to use the models "across all lawful use cases" without vendor-imposed restrictions. In a 40-page court filing, the DoW argues Anthropic might "disable its technology or alter model behavior" during warfighting operations if it feels its red lines are being crossed. The DoW calls Anthropic's position an "unacceptable risk to national security."
4. Anthropic rejects the DoW's final offer. CEO Dario Amodei states: "We cannot in good conscience accede to their request."
5. Within hours of Anthropic being blacklisted by the Trump administration, OpenAI signs a deal with the DoW on the broader terms.
6. Weeks later, Anthropic and the Pentagon resume negotiations, but the fundamental tension -- builder governance vs. deployer sovereignty -- remains unresolved.

**6L-G Failure Map:**

- **Level 1 (Strategy & Policy) -- RED**: The core failure. Anthropic's charter and the DoW's operational doctrine are incompatible. No mechanism existed to reconcile them before the contract was signed. The question "whose charter governs?" was never answered upfront.
- **Level 2 (Risk & Impact Assessment) -- YELLOW**: Anthropic assessed that LLMs are not sufficiently reliable for autonomous weapons -- a defensible position. But neither party conducted a joint risk assessment that mapped which use cases were acceptable to both sides before deployment began.
- **Level 3 (Implementation Review) -- YELLOW**: Claude was deployed on classified networks where Anthropic has limited visibility into how it's actually used. The architecture gave Anthropic a theoretical kill switch but no operational insight into context of use -- a governance gap the DoW later cited as a threat.
- **Level 5 (Operations & Monitoring) -- RED**: Once Claude is on classified networks, Anthropic cannot monitor its outputs or verify compliance with its own red lines. The DoW controls the operational environment. This is the inversion problem: the builder's governance controls depend on visibility the deployer won't grant.
- **Level 6 (Learning & Improvement) -- GRAY**: The relationship collapsed before any feedback loop could be established.

**The Fix -- what controls would have prevented this:**

- **Level 1**: A joint governance charter negotiated before deployment, explicitly listing permitted and prohibited use cases with escalation procedures for ambiguous cases. Both parties sign the same document.
- **Level 2**: A shared risk assessment framework that classifies military use cases by autonomy level (human-in-the-loop, human-on-the-loop, human-out-of-the-loop) and maps each to acceptable/unacceptable for the model builder.
- **Level 3**: Architecture that provides the builder with anonymized usage telemetry -- enough to verify red-line compliance without exposing classified operational details. A technical middle ground between full visibility and total opacity.
- **Level 5**: An independent third-party auditor with clearance to review model usage on classified networks and report red-line compliance to both parties. Neither the builder nor the deployer needs to trust the other -- they both trust the auditor.

**Why this scenario matters for the framework:**
The other five scenarios assume a single organization governs its own AI. This scenario breaks that assumption. In defense AI, the supply chain spans model builders (Anthropic/OpenAI), system integrators (Palantir), and deployers (DoW). Governance responsibility is distributed across organizations with different values, incentives, and legal obligations. The 6L-G framework still applies, but it must be applied _across_ organizational boundaries -- and that requires contracts, shared frameworks, and independent verification that most governance programs don't yet address.

**Regulatory context:**

- DoD Directive 3000.09 requires "appropriate levels of human judgment" for autonomous weapons -- Anthropic's position aligned with existing US policy
- No federal statute currently governs AI vendor restrictions on military use
- The Center for American Progress called for Congressional action to establish clear rules for AI vendor governance in defense contracts
- OpenAI's decision to accept the broader terms demonstrates that governance posture is a market differentiator, not a regulatory requirement -- companies can choose different positions

## Style & UX

- Dark theme with scenario cards using distinct accent colors for each category
- The failure map should feel like a diagnostic tool -- clinical, precise
- Smooth reveal animations for the timeline steps (not flashy, just paced)
- Color coding must be accessible (use patterns/icons in addition to color for red/yellow/green)
- Cross-linking between scenarios -- when a pattern appears in multiple scenarios, link them
- Each scenario should take 3-5 minutes to explore thoroughly
- Mobile-friendly layout (scenarios work well on tablets for workshop settings)

## Audience

Engineers, product managers, and governance practitioners who learn better from concrete examples than abstract frameworks. They want to see what goes wrong when governance fails, understand why, and know what to do differently. The scenario format works well for team workshops and training sessions.

## Source Material

- _AI Governance: Secure, privacy-preserving, ethical systems_ by Engin Bozdag & Stefano Bennati (Manning, 2026)
- Section 1.4: Challenges in Practice -- Illustrative Scenarios
- Table 1.2: Applying the 6L-G model to GDPR "right to erasure" (full lifecycle walkthrough)
- Table 1.3: Overview of tools available at each governance level
- Figure 1.4: Six Levels of Generative AI Governance
- Table 1.1: Why Generative AI Breaks Traditional Control Models
- Real-world cases: FTC v. Everalbum (model destruction), Pieces Technologies (Texas deceptive trade), Snap My AI (child safety), CFPB bank chatbot report, Microsoft/OpenAI DeepSeek investigation
- DoW/Anthropic contract dispute (Feb-Mar 2026): TechCrunch, CNBC, Axios, CNN, NPR reporting; DoW 40-page court filing; Center for American Progress analysis; Jon Stewart Weekly Show interview with Dr. Sarah Shoker and Paul Scharre
- DoD Directive 3000.09 (autonomous weapons policy)
- GDPR Article 17 (right to erasure), EU AI Act risk tiers, NIST AI RMF
