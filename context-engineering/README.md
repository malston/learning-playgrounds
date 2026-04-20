# Context Engineering Playground

A hands-on notebook for exploring LLM context management — context rot, failure modes, RAG, multi-agent isolation, and compression strategies. Each section pairs a concept with runnable experiments so you can observe the behavior directly.

## Prerequisites

- Python 3.9 or later ([python.org/downloads](https://www.python.org/downloads/))
- An Anthropic API key ([console.anthropic.com](https://console.anthropic.com))

Check your Python version:

```bash
python3 --version
```

## Setup (5 minutes)

### 1. Create a virtual environment

A virtual environment keeps this project's packages isolated from the rest of your system. It's optional, but recommended — without it you risk version conflicts with other tools, and some macOS setups block global `pip` installs entirely.

From the directory where the notebook lives:

```bash
python3 -m venv .venv
```

Then activate it:

**macOS / Linux:**

```bash
source .venv/bin/activate
```

**Windows (Command Prompt):**

```cmd
.venv\Scripts\activate.bat
```

**Windows (PowerShell):**

```powershell
.venv\Scripts\Activate.ps1
```

Your terminal prompt will change to show `(.venv)` when it's active. You'll need to run the activate command each time you open a new terminal before launching Jupyter.

### 2. Install JupyterLab and the Anthropic SDK

With the venv active:

```bash
pip install jupyterlab anthropic numpy ipykernel
```

Then register the venv as a Jupyter kernel so the notebook uses the right packages:

```bash
python -m ipykernel install --user --name=context-eng --display-name "Context Engineering"
```

> **Note:** If `pip` isn't found, try `pip3`. If you get a permissions error, make sure your venv is activated (you should see `(.venv)` in your prompt).

### 2. Set your API key

The notebook reads your key from an environment variable. Set it in your terminal before launching Jupyter:

**macOS / Linux:**

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

**Windows (Command Prompt):**

```cmd
set ANTHROPIC_API_KEY=sk-ant-...
```

**Windows (PowerShell):**

```powershell
$env:ANTHROPIC_API_KEY="sk-ant-..."
```

> The key only lasts for your current terminal session. You'll need to set it again each time you open a new terminal, or add the export line to your shell profile (`~/.zshrc`, `~/.bashrc`, etc.) to make it permanent.

### 3. Launch JupyterLab

From the same terminal where you set the API key, run:

```bash
jupyter lab
```

This opens a browser window at `http://localhost:8888`. Leave the terminal running — closing it stops the server.

### 4. Open the notebook

In the JupyterLab file browser on the left, double-click **`context-engineering-playground.ipynb`**.

In the top-right corner of the notebook, click the kernel selector and choose **"Context Engineering"** — this ensures the notebook uses your venv's packages rather than a system Python.

## Running the Notebook

Jupyter notebooks are made of **cells** — blocks of text or code. You run them one at a time, in order from top to bottom.

**To run a cell:**

- Click on it, then press `Shift + Enter`
- Or click the ▶ button in the toolbar

**Important:** Always run cells in order. Later cells depend on code defined in earlier ones. If you skip the Setup cell (Section 1), everything else will fail.

The first time you run a code cell, you'll see `In [*]:` next to it — the `*` means it's running. It changes to a number (e.g., `In [1]:`) when it's done.

### Run order

1. **Section 1 (Setup)** — run this first, every time you open the notebook
2. **Sections 2–7** — run in any order after that, but within each section run cells top to bottom

## Cost and Speed

The notebook uses `claude-haiku-4-5` by default — the fastest and cheapest model. Running the entire notebook end-to-end costs roughly **$0.05–0.15** depending on how many trials you run.

To change the model, edit this line in the Setup cell:

```python
MODEL = 'claude-haiku-4-5-20251001'   # change to claude-sonnet-4-6 for better results
```

Haiku is fine for learning the concepts. Sonnet will show more interesting degradation in the context rot experiments.

## Troubleshooting

**`ModuleNotFoundError: No module named 'anthropic'`**
Your venv probably isn't active, or the kernel is set to a system Python. Check that `(.venv)` appears in your terminal prompt, and that the kernel selector in the top-right of the notebook shows "Context Engineering". If not, run the `ipykernel install` command from Step 2 again, then switch the kernel via **Kernel → Change Kernel**.

**`AuthenticationError` or `Invalid API key`**
Your key wasn't picked up. Close the notebook, re-run `export ANTHROPIC_API_KEY="sk-ant-..."` in your terminal, then `jupyter lab` again.

**`RateLimitError`**
You've hit the API rate limit. Wait 30 seconds and re-run the cell.

**Kernel died / notebook stopped responding**
Go to **Kernel → Restart Kernel** in the menu bar, then re-run from Section 1.

**Output looks wrong or a cell seems stuck**
Go to **Kernel → Restart Kernel and Clear Outputs**, then run from the top.

## What Each Section Does

| Section | What you'll observe |
| --- | --- |
| 2 — Context Rot | Retrieval accuracy drop as window fills; position bias; coherent vs. shuffled haystack |
| 3 — Failure Modes | Context poisoning, clash, and entity confusion — deliberately triggered, then mitigated |
| 4 — Four Operations | Write / Select / Compress / Isolate implemented as runnable patterns |
| 5 — RAG Pattern | Retrieval from a small corpus; out-of-scope query handling |
| 6 — Multi-Agent Isolation | Critic + Advocate in isolated contexts vs. anchoring contamination |
| 7 — Compression | Rolling summary chat; structured checkpoint reset |

## Saving Your Work

Jupyter auto-saves periodically. To save manually: `Cmd+S` (Mac) or `Ctrl+S` (Windows/Linux).

Your outputs (the results printed below each cell) are saved with the file. To share a clean copy with no outputs: **Kernel → Restart Kernel and Clear Outputs**, then save.
