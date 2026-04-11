# Hybrid Memory Architecture

Interactive playground exploring a four-layer memory architecture that teaches AI coding agents to remember across sessions, compactions, and projects.

## Layers

| Layer           | Purpose                          | Persistence            |
| --------------- | -------------------------------- | ---------------------- |
| Beads           | Work tracking with dependencies  | Dolt database          |
| Auto-Memory     | User preferences and corrections | Markdown files         |
| Claude-Mem      | Past actions and outcomes        | SQLite via PostToolUse |
| Episodic Memory | Conversation history             | Indexed transcripts    |

## Sections

- **Architecture** -- Layer overview, session data flow, five kinds of forgetting
- **Memory Router** -- Routing table and interactive quiz
- **Claude-Mem vs Episodic** -- Side-by-side comparison and decision helper
- **Session Lifecycle** -- Timeline with token budget breakdown

## Usage

Open `index.html` in a browser. No dependencies required.
