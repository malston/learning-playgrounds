# Frontend Architecture Decision Playground

Build an interactive decision-making playground for frontend architecture. The goal is not to teach what frameworks exist — this playground is the decision layer: given a specific context, what should I choose and why?

The audience is engineers returning to frontend after time away, backend engineers moving into fullstack, and tech leads evaluating options for a new project. They understand software engineering. They don't need definitions — they need opinionated, defensible recommendations with the reasoning exposed.

---

## Core Interactive Elements

### 1. Framework Selector — Guided Questionnaire

A multi-step wizard (not a single long form) that asks 5-6 questions in sequence, each informed by the previous answer. Questions should feel like a conversation with a senior engineer, not a survey.

**Questions (adapt based on prior answers):**

1. **What are you building?** — Public-facing content site / E-commerce or marketing / SaaS app behind auth / Internal tooling / Mobile-first consumer app
2. **What's your team size and structure?** — Solo or pair / Small team (3-8) / Mid-size (8-20) / Large or distributed (20+)
3. **What's your hiring situation?** — Actively hiring in a competitive market / Stable team, no near-term hiring / Contractor-heavy / Offshore or distributed team
4. **How much do you care about bundle size and initial load performance?** — Core to the product (e-commerce, media, mobile) / Important but not critical / Not a primary concern
5. **Does this project need to ship fast with junior developers?** — Yes, fast ramp-up matters / No, experienced team only

**Output:** Top recommendation + 1-2 alternatives, each with:

- A one-paragraph explanation of *why* this fits their answers
- The 2-3 tradeoffs they're accepting
- A warning if their answers suggest a common mistake (e.g., choosing Solid with a large hiring need, or choosing Angular for a 2-person startup)

Show the decision logic — don't just show the answer, show which answers drove it.

### 2. Rendering Strategy Visualizer

An animated, step-by-step visualization of what actually happens in the browser for each rendering strategy. Let the user select a strategy and step through the timeline:

**For each strategy, show:**

- What leaves the server (HTML? JS bundle? Both?)
- What the browser receives at t=0, t=500ms, t=1000ms, t=2000ms
- When the page is *visible* vs *interactive* — these are different moments
- The infrastructure required (static host / Node server / Edge)

**The 5 strategies to cover:**

- CSR — blank screen until JS executes
- SSR + Hydration — visible early, interactive later, hydration cost shown explicitly
- SSG — fastest possible, build pipeline constraint shown
- Islands (Astro) — static shell with isolated hydration zones, show which parts are JS and which aren't
- Resumability (Qwik) — no hydration replay, show lazy handler loading on first interaction

**Key insight to make visceral:** SSR and CSR with the same framework (e.g., Next.js) produce completely different performance profiles. The visualization should make this obvious without text explanation.

### 3. Reactivity Model Side-by-Side

A single component — a filtered list that updates as the user types — implemented in three models. Show them side by side:

- **React** (runtime VDOM diffing) — show what re-renders on each keystroke, bundle size contribution, what the compiler emits
- **Svelte** (compile-time) — show what the compiler generates, what actually runs in the browser, the absence of a diffing runtime
- **Signals (Solid)** — show the dependency graph, which nodes update and which don't

**Interactive element:** A slider that increases list size (100 → 1000 → 10,000 items). Show how update cost scales differently for each model. Make the O(tree size) vs O(1) distinction concrete.

**Don't show code as the primary view** — show the *effect* of the code. Code snippets should be secondary, collapsible for those who want them.

### 4. Meta-Framework Elimination Bracket

Start with all meta-frameworks: Next.js, Remix, Nuxt, SvelteKit, SolidStart, Astro, Qwik City.

User toggles their constraints:

- Must support SSG → eliminates options that don't
- Team already knows Vue → eliminates non-Vue options
- Need islands architecture → filters to Astro
- Vercel deployment preferred → surfaces Next.js advantage
- Web standards / no vendor lock-in → surfaces Remix

As they toggle, frameworks fade out with a one-line reason why they were eliminated. What remains is their shortlist. Clicking a surviving option shows its key differentiator and the main reason to reject it even when it fits.

### 5. The Org Structure Test

A single provocative question with a branching result:

> "When 10 different developers touch the same codebase over 2 years, what do you want to be true?"

Two paths:

- **"I want consistency enforced by the framework"** → explains the Angular value proposition honestly: DI, opinionated structure, CLI enforcement, why large enterprises choose it
- **"I want flexibility, we'll define our own conventions"** → explains the React tradeoff: you get freedom but you're now responsible for the conventions, linting rules, folder structure, state management choice

Don't frame one as better. Frame them as solving different organizational problems. Show what goes wrong when you make the wrong choice for your org type — the React project that became 10 different architectures, the Angular project that blocked a 3-person startup from moving fast.

---

## Style & UX

- Dark theme — `#0a0e17` background, `#38bdf8` / `#a78bfa` accents, `#34d399` / `#fb923c` secondary accents
- Wizard steps should feel progressive — don't show all questions at once
- Animations should be functional, not decorative — they should reveal information, not just look good
- Every recommendation should show its reasoning — no black box answers
- Tooltips on technical terms (hydration, VDOM, signals, resumability) for the backend engineers in the audience
- Mobile-readable but optimized for desktop — this is a tool engineers use while planning, not while commuting

## What This Is Not

- Not a beginner tutorial — don't explain what React is
- Not a benchmark — performance numbers date quickly, focus on the *shape* of the tradeoffs
- Not opinionated about one right answer — different contexts have genuinely different right answers, and the tool should reflect that
