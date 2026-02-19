# Web Application Hosting Decision Playground

Build an interactive decision-making playground for web application hosting. The goal is not to list hosting providers — it's to help engineers make a defensible hosting choice given their specific rendering strategy, scale, budget, and operational constraints.

The audience is engineers who have already made their framework and rendering strategy decisions and now need to figure out where to deploy. They understand CI/CD, containers, and cloud concepts. They don't need marketing copy — they need honest tradeoff analysis and cost reality checks.

---

## Core Interactive Elements

### 1. Rendering Strategy → Hosting Compatibility Matrix

The first gate: not all hosting platforms support all rendering strategies. Start here before anything else.

An interactive matrix where the user selects their rendering strategy and sees which platforms are valid options, which require workarounds, and which are incompatible:

| Strategy | Valid Platforms | Workarounds Required | Incompatible |
| --- | --- | --- | --- |
| SSG (static only) | Everything | None | None |
| CSR (static shell + client JS) | Everything | None | None |
| SSR / Universal Rendering (Node.js) | Vercel, Render, Railway, Fly, AWS, VPS | Cloudflare (Workers runtime, not Node) | GitHub Pages, Netlify static |
| Edge SSR | Vercel Edge, Cloudflare Workers, Netlify Edge | AWS Lambda@Edge (limited) | Traditional VPS, Railway |
| Islands (Astro) | Most platforms (output is mostly static) | Varies by SSR island usage | None for pure SSG mode |

Clicking a cell explains *why* — what the technical constraint is, not just the yes/no.

**Key insight to surface:** Cloudflare Workers is not a Node.js environment. Code that runs fine on Vercel or Railway may not run on Cloudflare without modification. This catches engineers by surprise.

### 2. Cost Reality Calculator

The most important tool in the playground. Vercel's pricing looks cheap until it doesn't. Show the real numbers.

**Inputs:**

- Monthly pageviews (slider: 10K → 100K → 1M → 10M)
- Rendering strategy (affects compute vs bandwidth costs)
- Average response size
- SSR vs SSG ratio (what % of requests hit a serverless function vs CDN cache)
- Team size (affects which plan tier is required for features like team collaboration, preview environments)

**Output:** Monthly cost estimate for each platform at the given inputs, shown as a bar chart that updates live:

- **Vercel** — free tier, Pro ($20/member/mo + usage), Enterprise
- **Netlify** — similar structure to Vercel
- **Cloudflare Pages + Workers** — extremely cheap at scale (100K free requests/day on Workers)
- **Railway** — $5/mo base + resource usage, predictable
- **Render** — flat instance pricing, easier to reason about
- **Fly.io** — per-VM pricing, scales to zero
- **AWS (App Runner / Lambda + CloudFront)** — complex but potentially cheapest at high scale
- **VPS (Hetzner / DigitalOcean)** — flat monthly, you manage everything

**Show the crossover points explicitly:** At what request volume does Vercel become more expensive than Cloudflare? When does a $6/mo Hetzner VPS beat Railway on cost? These numbers are the actual decision inputs most engineers are missing.

**Show the Vercel pricing cliff** — the jump from hobby to Pro to Enterprise is nonlinear and catches teams off guard. Make it visible.

### 3. Lock-in Exposure Visualizer

A feature-by-feature breakdown of which platform capabilities have no portable equivalent. Framed as: "if you use this feature today, here's what migration looks like in 18 months."

For each major platform, show:

**Vercel:**

- Edge Middleware → portable (it's standard Request/Response)
- Image Optimization API → proprietary, no equivalent on other platforms
- ISR (Incremental Static Regeneration) → Next.js-specific, works on Vercel natively, requires workarounds elsewhere
- Analytics → proprietary, replaceable with Plausible/PostHog
- Preview Deployments → similar feature exists on Netlify, Render, Railway

**Cloudflare:**

- Workers KV → proprietary key-value store, migration cost is real
- Durable Objects → no equivalent anywhere else
- R2 (S3-compatible) → portable by API design
- Workers runtime → not Node.js, code may not be portable

**Netlify:**

- Forms → proprietary, easy to replace
- Identity → proprietary
- Edge Functions → similar to Vercel Edge

Color-code by migration difficulty: green (easy to replace), yellow (some work), red (significant migration cost).

**The point:** Lock-in is not inherently bad. Vercel's ISR is worth the lock-in for some teams. But engineers should make that choice consciously, not discover it during a cost crisis.

### 4. Hosting Selector — Guided Questionnaire

A multi-step wizard that produces a ranked recommendation. Questions in sequence:

1. **What's your rendering strategy?** — Static / CSR / SSR / Edge SSR / Mixed
2. **What's your expected monthly traffic?** — Under 100K requests / 100K–1M / 1M–10M / 10M+
3. **How important is zero-ops?** — I want to think about infrastructure as little as possible / I'm comfortable with some configuration / I'll manage my own infra for cost control
4. **Do you have compliance or data residency requirements?** — No / Specific regions required / On-prem or private cloud required
5. **What's your monthly hosting budget?** — Under $20 / $20–$100 / $100–$500 / Cost scales with revenue, not a fixed budget
6. **Do you need preview deployments per PR?** — Yes, it's part of our workflow / Nice to have / No

**Output:** Top recommendation + 2 alternatives with:

- Why it fits their answers
- The one thing most likely to bite them later
- A specific warning if their answers suggest a common mistake (e.g., choosing Vercel at 10M+ monthly requests without an Enterprise agreement, or choosing a VPS with zero-ops preference)

### 5. Operational Complexity Spectrum

A visual spectrum from zero-ops to full control, with platforms positioned honestly:

```text
Zero-ops ←————————————————————————————————→ Full control

GitHub Pages  Vercel  Netlify  Railway  Render  Fly.io  App Runner  EC2/VPS
    |            |        |       |        |       |         |          |
  Static       Best     Good   Great    Good   Good      Flexible   You own
  only         DX       DX     balance  DX     control   AWS        everything
```

For each position on the spectrum, show:

- What you don't have to manage (the benefit)
- What you give up (flexibility, cost efficiency, control)
- Team profile this fits (solo dev, startup, scale-up, enterprise)

**Key insight:** "Zero-ops" is not free — you pay for it in dollars at scale and in lock-in over time. "Full control" is not free either — you pay in engineering time and operational risk. Neither extreme is wrong; the tradeoff should be explicit.

### 6. The Vercel vs Cloudflare Decision

These two dominate the modern hosting conversation and the choice between them deserves its own focused tool.

A side-by-side comparison focused on the specific decision points:

| Factor | Vercel | Cloudflare |
| --- | --- | --- |
| **DX for Next.js** | Native, first-class | Good, but not the same |
| **Cost at 1M req/mo** | $$ | $ |
| **Cost at 10M req/mo** | $$$+ | $ |
| **Runtime** | Node.js (familiar) | V8 isolates (not Node) |
| **Cold starts** | Occasional | Near-zero (isolates) |
| **Global edge** | ~30 regions | 300+ locations |
| **Lock-in** | Moderate | Moderate (different features) |
| **Analytics / observability** | Built-in | Requires Workers Analytics or third-party |

With a decision output: given the user's framework, scale, and budget, which one fits better and why.

---

## Style & UX

- Dark theme — `#0a0e17` background, `#38bdf8` / `#a78bfa` accents, `#34d399` / `#fb923c` secondary accents
- Cost calculator updates in real-time as sliders move — no submit button
- Lock-in visualizer uses color coding, not just text — green / yellow / red is faster to scan than reading
- Every recommendation exposes its reasoning — no black box outputs
- Tooltips on terms engineers may not know: ISR, edge runtime, V8 isolates, cold start, data residency
- Platform logos or wordmarks where possible to make scanning faster

## What This Is Not

- Not a sponsored comparison — no platform gets favored treatment
- Not a beginner's guide — don't explain what a CDN is
- Not a benchmark — latency numbers date quickly, focus on architectural tradeoffs
- Not a replacement for reading pricing pages — the calculator gives directional estimates, not invoices
