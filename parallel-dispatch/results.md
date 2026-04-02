# Results

Single-file parallel dispatch builder (1773 lines) with a four-step workflow:

**Task Inventory:** Add discrete units of work with names and descriptions. The wizard uses these to reason about parallelism, isolation, and coordination needs.

**Decision Wizard:** Guided questionnaire that analyzes your tasks and recommends one of eight patterns: Fan-out, Pipeline, Shared Workspace, Peer Coordination, or any of these with a Critic review stage. Each recommendation includes an explanation of why it fits your task profile.

**Configuration:** After selecting a pattern, configure agent roles, fan-out merge strategy, pipeline stage ordering, and critic settings as applicable.

**Prompt Generator:** Assembles everything into a copyable orchestration prompt ready for use with an AI coding agent.

How to use it:

- Start by adding at least two tasks to the inventory
- Step through the decision wizard to get a pattern recommendation
- Configure the dispatch details for your selected pattern
- Generate and copy the orchestration prompt from the output panel
- Use the tab bar to switch between pattern visualizations (Fan-out, Pipeline, Shared Workspace, Peer Coordination)
