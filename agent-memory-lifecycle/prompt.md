# Agent Memory Lifecycle

Build an interactive playground that visualizes how AI coding agents manage memory across a session. The tool should make the invisible lifecycle of memory operations tangible -- showing when memory is read, written, searched, and compacted, and which memory layer handles each type of recall.

## Core Concept

AI coding agents juggle multiple memory layers (conversation context, file-based memory, episodic search, project facts) but the mechanics are invisible to the user. This explorer makes the lifecycle concrete so engineers can reason about what their agent remembers, forgets, and reconstructs.

## Interactive Elements

### 1. Session Timeline

A horizontal timeline showing memory events across a typical agent session. Each event is a card showing:

- The trigger (user message, tool call, compaction)
- Which memory layer was involved
- Whether it was a read, write, or search operation
- What data moved and where

Click an event to see details. Color-code by memory layer.

### 2. Layer Explorer

An intent-to-command mapping view. Left column shows what the user wants ("recall a past decision", "remember this preference"). Right column shows the actual command or tool the agent runs. Select items to see which memory layer handles the request.

### 3. Query Router

A visual router showing how different types of recall questions get dispatched to different memory layers. Input a question type and see the routing path -- which layer is checked first, what falls through, and what gets reconstructed vs. retrieved.

## Style & UX

- Dark theme, tabbed navigation between the three views
- Timeline scrolls horizontally with event cards
- Layer explorer uses a two-column intent/command layout
- Color-coded dots per memory layer throughout
- Single self-contained HTML file
