# Results

## What Was Built

A four-tab interactive playground exploring a hybrid memory architecture for AI coding agents.

### Tab 1: Architecture Overview

- **Layer cards**: 4 expandable cards (Beads, Auto-Memory, Claude-Mem, Episodic Memory) showing persistence mechanism, contents, commands, token cost, and which forgetting type each solves
- **Session data flow**: Visual diagram showing what fires at Startup, Working, and Shutdown/Compaction phases
- **Five Kinds of Forgetting**: Grid mapping 5 amnesia types to their solution layers

### Tab 2: Memory Router

- **Routing table**: 10 clickable knowledge-type cards with highlighted destination layers and exact commands
- **Routing quiz**: 8 scenario-based questions testing routing knowledge with immediate feedback and score tracking

### Tab 3: Claude-Mem vs Episodic Memory

- **Side-by-side comparison**: Lab notebook vs meeting transcript metaphor with bullet lists of what each captures
- **Hook comparison table**: 5-row table showing which Claude Code hooks each system uses
- **Decision helper**: 6 clickable "Which do I search?" scenarios with explanations

### Tab 4: Session Lifecycle

- **Interactive timeline**: 10 expandable events across 3 phases showing hook, token cost, output, and detail
- **Token budget**: Bar chart breaking down ~23,300 token startup cost across 6 components

## How to Use

Open `index.html` in any browser. Navigate between tabs using the top bar. Click cards and timeline events to expand details. Take the routing quiz on Tab 2 to test your understanding.
