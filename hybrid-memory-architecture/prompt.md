# Hybrid Memory Architecture Playground

Create an interactive learning playground that explains a four-layer hybrid memory architecture for AI coding agents. The architecture solves five distinct types of "forgetting" that occur when working with LLM-based coding assistants across sessions.

## Four Layers

1. **Beads** -- Dolt-backed issue tracker for durable work tracking with dependency awareness. Handles work items, epics, decisions, and stable project facts via `bd remember`.
2. **Auto-Memory** -- Claude Code's built-in memory system (markdown + YAML frontmatter). Handles user preferences, behavioral corrections, and in-progress brainstorms.
3. **Claude-Mem** -- Plugin that records observations via PostToolUse hook on every tool call. Provides semantic search over past actions, outcomes, and artifacts.
4. **Episodic Memory** -- Plugin that indexes full conversation transcripts for semantic + text search. Captures reasoning, discussions, and verbal instructions.

## Interactive Sections

### Tab 1: Architecture Overview

- Clickable layer cards showing purpose, persistence mechanism, what goes here, key commands, token cost, and which forgetting type it solves
- Session data flow diagram: Startup > Working > Shutdown/Compaction
- Five Kinds of Forgetting grid mapping each amnesia type to its solution layer

### Tab 2: Memory Router

- Interactive routing table: click a knowledge type to highlight its canonical destination with the exact command
- "Route This Knowledge" quiz: 8 scenarios where the user picks the correct memory layer, with green/red feedback and explanations

### Tab 3: Claude-Mem vs Episodic Memory

- Side-by-side comparison with the "lab notebook vs meeting transcript" metaphor
- Hook comparison table showing how each system hooks into Claude Code differently
- "Which Do I Search?" decision helper: 6 clickable scenarios showing which system has the answer

### Tab 4: Session Lifecycle

- Interactive timeline with three phases (Startup, Working, Shutdown/Compaction) and expandable events
- Token budget breakdown with bar chart showing ~23K startup cost on a 200K context window

## Design Requirements

- Dark theme matching existing playgrounds (CSS custom properties)
- Self-contained single HTML file, no external dependencies
- Accessible: ARIA roles, keyboard navigation, focus-visible outlines
- Responsive grid layouts
