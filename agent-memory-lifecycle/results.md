# Results

Single-file session lifecycle explorer (1181 lines) with three tabbed views:

**Timeline:** Horizontal scrolling timeline of memory events across a session. Each event card shows the operation type, trigger, and involved memory layers. Click a card to see details about what data moved and where. Events are color-coded by memory layer.

**Layer Explorer:** Two-column intent-to-command mapping. Left side shows user intent ("recall a past decision"), right side shows the command the agent runs. Select items to see which memory layer handles the request.

**Query Router:** Visual routing diagram showing how recall questions get dispatched to memory layers. See which layer is checked first, what falls through to secondary layers, and what gets reconstructed vs. retrieved directly.

How to use it:

- Use the tab bar to switch between Timeline, Layer Explorer, and Query Router views
- On the Timeline, scroll horizontally and click event cards for details
- On the Layer Explorer, select intent items to see the corresponding command and memory layer
- Color-coded dots indicate which memory layer (conversation, file-based, episodic, project) is involved
