# AI Governance 6L-G Explorer -- Results

## What was produced

A single-file interactive HTML playground (`ai-governance-6lg-explorer.html`) for exploring the Six-Level GenAI Governance (6L-G) framework.

## Features

- **Flowchart navigation**: Six governance levels as clickable nodes with a feedback loop indicator
- **Questions-first detail view**: 4 expandable questions per level, each revealing categorized tool pills (procedural/technical/organizational), role ownership, and context
- **Deployment model filter**: SaaS / API Integrator / Self-Hosted toggle that reframes questions as vendor due diligence for SaaS customers on levels 3-5
- **GDPR erasure walkthrough**: Toggle mode that traces a right-to-erasure request through all six governance levels (control, in practice, if skipped)
- **Prompt output**: Generates a contextual learning prompt based on current view with clipboard copy
- **Tooltips**: Hover definitions on tool pills, deployment filter pills, and the 6L-G framework title
- **Keyboard accessible**: Arrow keys navigate levels, Enter/Space expands questions, Tab moves through interactive elements

## How to use

Open `ai-governance-6lg-explorer.html` in any browser. No server or dependencies required.

1. Click governance levels on the left to explore questions and tools on the right
2. Use the deployment filter pills in the header to see which controls apply to your deployment model
3. Toggle "GDPR Erasure Walkthrough" at the bottom of the left panel to trace a deletion request through the framework
4. Copy the generated prompt at the bottom to continue learning in Claude or another AI assistant

## Source material

Based on _AI Governance: Secure, privacy-preserving, ethical systems_ by Engin Bozdag & Stefano Bennati (Manning, 2026), specifically the 6L-G framework (Figure 1.4), tools matrix (Table 1.3), and GDPR erasure lifecycle (Table 1.2).
