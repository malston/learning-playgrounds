# AI Agent Spec Builder

Build an interactive playground that helps engineers evaluate and generate AI agent specification files (CLAUDE.md, SPEC.md, or similar project instructions). The tool should analyze an existing spec or guide the user through building one from scratch.

## Core Concept

Agent specs vary wildly in quality. Most are either too vague to be useful or too verbose to maintain. This tool scores a spec across the dimensions that actually matter for agent performance, then helps the user improve weak areas.

## Interactive Elements

### 1. Spec Analyzer

A text area where the user pastes their existing agent spec. The analyzer parses it and scores seven dimensions on a 0-5 scale:

- **Project Vision** -- Goal clarity and scope definition
- **Commands & Environment** -- Build, test, and run instructions
- **Project Structure** -- Directory layout and file organization
- **Code Style & Patterns** -- Coding conventions and preferred patterns
- **Boundaries** -- Agent autonomy and approval gates
- **Testing Strategy** -- Verification approach and coverage expectations
- **Git Workflow** -- Version control conventions and safety rules

Each dimension expands to show what was found, what's missing, and specific questions to improve coverage.

### 2. Guided Builder

For users starting from scratch, walk through each dimension with targeted questions. Provide example answers showing what good looks like for each area.

### 3. Spec Generator

Combine all dimension inputs into a complete, well-structured spec file. Output should be copyable markdown ready to drop into a project.

## Style & UX

- Dark theme with accent colors per score tier (red/orange/amber/emerald)
- Accordion layout for dimensions -- one open at a time
- Score badges visible at a glance without expanding
- Monospace font for generated output
- Single self-contained HTML file
