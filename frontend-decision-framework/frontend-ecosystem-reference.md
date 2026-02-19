# Frontend Ecosystem Reference Guide

Architectural decisions, reactivity models, and organizational factors for modern frontend development. Companion to the [interactive frontend field guide](./frontend-reference.html).

---

## Rendering Strategies: The Real Architectural Decision

Framework choice gets the attention, but the rendering strategy is the decision that shapes your architecture. React alone can do CSR, SSR, and SSG depending on how you deploy it. Pick the rendering model first, then pick the framework that supports it.

### Strategy Comparison

| Strategy         | Initial Load                     | Time to Interactive               | SEO                       | Server Infra         | Best For                                     |
| ---------------- | -------------------------------- | --------------------------------- | ------------------------- | -------------------- | -------------------------------------------- |
| **CSR**          | Slow (large JS bundle)           | Slow (download + parse + execute) | Poor without prerendering | None (static host)   | Dashboards, SPAs behind auth, internal tools |
| **SSR**          | Fast (HTML ready)                | Depends on hydration cost         | Good                      | Required (Node/edge) | E-commerce, content apps, personalized pages |
| **SSG**          | Fastest (pre-built from CDN)     | Fast (minimal JS)                 | Excellent                 | Build pipeline only  | Docs, blogs, marketing sites                 |
| **Islands**      | Fast (mostly static HTML)        | Fast (only islands hydrate)       | Excellent                 | Optional             | Content sites with interactive widgets       |
| **Resumability** | Very fast (near-zero initial JS) | Instant (no hydration)            | Excellent                 | Required             | Performance-critical apps at scale           |

### What Each Strategy Actually Does

**CSR (Client-Side Rendering)** -- The browser downloads a minimal HTML shell, then JavaScript builds the entire page. Simple to deploy (any static host works) but the user stares at a blank screen until the JS bundle downloads, parses, and executes.

**SSR (Server-Side Rendering)** -- The server renders HTML per request and sends it to the browser. The page is visible immediately, but interactive elements don't work until JavaScript "hydrates" the static HTML into a live application. Hydration replays the rendering work the server already did -- that's the fundamental cost.

**SSG (Static Site Generation)** -- HTML is generated once at build time and served from a CDN. No server-side computation per request. The constraint is that content changes require a rebuild and deploy, which makes it impractical for pages that update frequently or are personalized per user.

**Islands Architecture (Astro)** -- The page is static HTML by default. Interactive components ("islands") hydrate independently, so a search widget can be interactive while the rest of the page ships zero JavaScript. This is partial hydration -- you only pay the JS cost for the parts that need interactivity.

```text
Traditional SSR:
  [Server HTML] → hydrate ENTIRE page → interactive

Islands:
  [Server HTML] → hydrate only [search] and [cart] → rest stays static
                   ^^^^^^^^         ^^^^
                   island 1         island 2
```

**Resumability (Qwik)** -- Instead of hydrating, the framework serializes the application state into the HTML. The browser "resumes" from that serialized state when the user interacts, downloading only the event handler needed for that specific interaction. No replay of rendering work.

```text
SSR + Hydration:
  Server renders → Client downloads all JS → replays rendering → interactive

Resumability:
  Server renders + serializes state → Client loads handler on interaction → interactive
                                      (lazy, per-interaction)
```

### Why This Matters More Than Framework Choice

A React app using Next.js with SSG behaves fundamentally differently from a React SPA. Same framework, completely different performance profile, infrastructure requirements, and developer workflow. The rendering strategy determines:

- **Infrastructure costs** -- CSR needs a static host; SSR needs compute at the edge or origin
- **Performance ceiling** -- No amount of optimization makes CSR beat SSG for first contentful paint
- **Caching model** -- Static pages cache trivially; SSR responses need careful cache invalidation
- **Developer workflow** -- SSG means rebuild on content change; SSR means managing server state

---

## Compile-Time vs Runtime Reactivity

The other foundational split in the frontend ecosystem: how does the framework detect and apply state changes?

### The Two Models

**Runtime diffing (React, Vue)** -- Maintain a virtual representation of the DOM. When state changes, re-render the component tree virtually, diff the old and new virtual DOM, then apply the minimal set of real DOM mutations. The diffing engine ships as part of your bundle and runs on every update.

```text
State change → re-render virtual DOM → diff old vs new → patch real DOM
                                        ^^^^^^^^^^^^^^^^
                                        runtime cost on every update
```

**Compile-time reactivity (Svelte, Solid)** -- The compiler analyzes your code at build time, identifies which DOM nodes depend on which state variables, and generates targeted update instructions. No virtual DOM, no diffing at runtime.

```text
State change → compiler-generated code updates specific DOM nodes
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
               direct mutations, no diffing overhead
```

### Comparison

| Dimension                | Runtime (React, Vue)                               | Compile-Time (Svelte, Solid)                             |
| ------------------------ | -------------------------------------------------- | -------------------------------------------------------- |
| **Bundle size baseline** | Larger (ships diffing runtime: ~40-45KB for React) | Smaller (no runtime; output scales with component count) |
| **Update cost**          | O(tree size) diffing per update                    | O(1) targeted DOM mutations                              |
| **Mental model**         | "Re-render everything, framework finds the diff"   | "Declare dependencies, compiler wires the updates"       |
| **Debugging**            | DevTools show virtual DOM state                    | DevTools show direct DOM, closer to output               |
| **Ecosystem maturity**   | Massive (React), large (Vue)                       | Growing (Svelte), smaller (Solid)                        |
| **Escape hatches**       | `useMemo`, `useCallback`, `React.memo` for perf    | Rarely needed; updates are already granular              |

### Signals: The Convergent Pattern

The industry is converging on **signals** -- fine-grained reactive primitives that track dependencies automatically and trigger only the updates that need to happen. This is a middle path between wholesale virtual DOM diffing and full compile-time analysis.

| Framework   | Signal Implementation                     | Status                                         |
| ----------- | ----------------------------------------- | ---------------------------------------------- |
| **Solid**   | Signals are the core primitive            | Shipped (foundational)                         |
| **Angular** | `signal()` API                            | Shipped (v16+)                                 |
| **Preact**  | `@preact/signals`                         | Shipped                                        |
| **Svelte**  | Runes (`$state`, `$derived`)              | Shipped (Svelte 5)                             |
| **Vue**     | `ref()` / `reactive()` in Composition API | Shipped (signal-like since Vue 3)              |
| **React**   | React Compiler (auto-memoization)         | In development (different approach, same goal) |

The direction is clear: explicit dependency tracking over wholesale re-rendering. React's approach is distinct -- rather than adopting signals directly, the React Compiler aims to auto-memoize components so developers don't manually optimize. Different mechanism, same goal of eliminating unnecessary work.

### When the Model Matters

For most applications, the reactivity model is not the bottleneck. A well-written React app and a well-written Svelte app will both feel fast for typical CRUD interfaces. The model matters when:

- **Large lists or frequent updates** -- Compile-time reactivity avoids diffing overhead that compounds with DOM size
- **Bundle size budget is tight** -- Mobile-first or bandwidth-constrained environments benefit from smaller baselines
- **Team size and conventions** -- React's ecosystem of performance escape hatches (`useMemo`, `useCallback`) creates a surface area for mistakes that compile-time approaches avoid

---

## Team and Hiring as Selection Criteria

Technical benchmarks compare frameworks on performance and features. But in practice, most framework selection decisions are organizational: can we hire for this? Can the team learn it? Does the structure match how we work?

### Framework-Organization Fit

| Framework   | Hiring Pool        | Learning Curve                      | Team Size Fit       | Organizational Pattern                          |
| ----------- | ------------------ | ----------------------------------- | ------------------- | ----------------------------------------------- |
| **React**   | Very large         | Moderate (hooks, ecosystem choices) | Any                 | Flexible; team defines its own conventions      |
| **Angular** | Large (enterprise) | Steep (many concepts upfront)       | Large teams (10+)   | Opinionated; enforces structure and consistency |
| **Vue**     | Moderate           | Gentle (progressive adoption)       | Small to mid (3-15) | Approachable; scales conventions gradually      |
| **Svelte**  | Smaller            | Easy (less to learn)                | Small to mid (2-10) | Productive; fast ramp-up for juniors            |
| **Solid**   | Niche              | Moderate (signals, JSX)             | Experienced teams   | Performance-first; requires reactive expertise  |

### The Hiring Market Reality

React dominates the hiring pool not because it's technically superior, but because network effects compound: more developers know React, so more jobs require React, so more developers learn React. This creates a practical advantage that no benchmark can counter.

| Factor                      | React     | Angular             | Vue        | Svelte            |
| --------------------------- | --------- | ------------------- | ---------- | ----------------- |
| **Job postings**            | Dominant  | Strong (enterprise) | Moderate   | Growing but small |
| **Bootcamp coverage**       | Universal | Common              | Occasional | Rare              |
| **Contractor availability** | High      | High                | Moderate   | Low               |
| **Senior developer pool**   | Deep      | Deep (enterprise)   | Moderate   | Shallow           |

For a startup that needs to hire three frontend developers in a mid-size market, choosing Solid over React isn't a technical decision -- it's a recruiting constraint.

### Angular Is an Organizational Design Choice

Angular gets compared to React on technical merits, but the real comparison is organizational. Angular ships with:

- Dependency injection, routing, forms, HTTP client, testing utilities
- Opinionated project structure and module system
- Style guide enforced by tooling (Angular CLI)

A 50-person development team benefits from these constraints. When 10 teams are building features in the same codebase, enforced consistency prevents the codebase from diverging into 10 different architectural styles. React gives you freedom; Angular gives you governance. Neither is wrong -- they solve different problems.

The question isn't "is Angular better than React?" It's: "does this organization need enforced consistency more than it needs flexibility?"

### Decision Checklist

When evaluating a framework for an organization, ask in this order:

1. **Can we hire for it in our market?** If the local (or remote) talent pool is thin, the best framework technically is the wrong choice practically.
2. **Does the team structure match?** Large, distributed teams benefit from opinionated frameworks. Small, senior teams can handle flexibility.
3. **What's the ramp-up cost?** A framework the team can't learn quickly is a framework that delays shipping. Svelte's low learning curve is a real advantage for teams with junior developers.
4. **Is the ecosystem sufficient?** React's ecosystem covers almost any need. Svelte's is growing but you may build things React has off-the-shelf.
5. **Does the rendering strategy align?** This loops back to the first section -- the rendering model constrains your framework options.

---

## Quick Reference: Framework to Meta-Framework

The framework handles reactivity and component model. The meta-framework handles rendering strategy, routing, and deployment.

| Framework      | Meta-Framework | Rendering Strategies | Key Differentiator                                         |
| -------------- | -------------- | -------------------- | ---------------------------------------------------------- |
| **React**      | Next.js        | CSR, SSR, SSG, ISR   | Largest ecosystem, Vercel-backed                           |
| **React**      | Remix          | SSR, CSR             | Web standards focus, nested routing                        |
| **Vue**        | Nuxt           | CSR, SSR, SSG, ISR   | Vue ecosystem, auto-imports                                |
| **Svelte**     | SvelteKit      | CSR, SSR, SSG        | Compile-time, smallest bundles                             |
| **Solid**      | SolidStart     | CSR, SSR, SSG        | Signals-native, fine-grained reactivity                    |
| **Any / None** | Astro          | Islands, SSG, SSR    | Content-first, partial hydration, bring-your-own-framework |
| **Custom**     | Qwik City      | Resumability, SSR    | Zero-hydration, lazy-loading by default                    |
