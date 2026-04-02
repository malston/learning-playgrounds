# Results

Single-file interactive spec builder (1552 lines) with two modes:

**Analyzer mode:** Paste an existing CLAUDE.md or project spec into the text area. The tool parses it and scores seven dimensions (Project Vision, Commands & Environment, Project Structure, Code Style & Patterns, Boundaries, Testing Strategy, Git Workflow) on a 0-5 scale with color-coded badges.

**Builder mode:** Expand any dimension to answer targeted questions with example answers showing what good coverage looks like. Fill in gaps identified by the analyzer or build a spec from scratch.

**Generator:** Assembles all dimension inputs into a complete, copyable markdown spec file.

How to use it:

- Paste an existing spec to get scored, or start fresh by expanding dimensions
- Click any dimension accordion to see its questions and example answers
- Score badges (red/orange/amber/emerald) show coverage at a glance
- Generate a complete spec from the output panel and copy it to your clipboard
