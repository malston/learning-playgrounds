# Parallel Agent Dispatch Builder

Build an interactive playground that helps engineers design parallel agent dispatch strategies. The tool should guide the user from task definition through pattern selection to generating a ready-to-use orchestration prompt.

## Core Concept

Parallel agent dispatch is powerful but choosing the wrong pattern wastes tokens, creates coordination overhead, or produces incoherent results. This builder walks the user through the decision: inventory your tasks, answer questions about dependencies and isolation, get a pattern recommendation, configure it, and export a dispatch prompt.

## Interactive Elements

### 1. Task Inventory

A form where the user lists discrete units of work. Each task gets a name and brief description. Minimum two tasks required -- the wizard needs them to reason about parallelism, isolation, and coordination.

### 2. Decision Wizard

A guided questionnaire that analyzes the user's tasks and recommends one of eight dispatch patterns:

- **Fan-out** -- Independent tasks, all run simultaneously
- **Pipeline** -- Sequential stages, output feeds the next
- **Shared Workspace** -- Agents read/write a common artifact
- **Peer Coordination** -- Agents negotiate across domain boundaries
- Each pattern also has a **with Critic** variant that adds a review stage

The wizard explains why each recommendation fits the user's task profile.

### 3. Configuration Panel

After pattern selection, configure the dispatch details:

- Number of agents and their roles
- Fan-out settings (merge strategy, timeout)
- Pipeline settings (stage ordering, handoff format)
- Critic settings when applicable

### 4. Prompt Generator

Assembles the task inventory, pattern selection, and configuration into a copyable orchestration prompt ready for use with an AI coding agent.

## Style & UX

- Dark theme with tabbed navigation
- Step-by-step flow: inventory -> wizard -> configure -> generate
- Pattern cards with visual diagrams showing agent topology
- Copyable output with syntax highlighting
- Single self-contained HTML file
