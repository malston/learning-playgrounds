# Results

**Hosting Decision Playground -- single-file HTML, no dependencies, 6 interactive sections:**

1. Compatibility Matrix -- Select rendering strategy (SSG/CSR/SSR/Edge/Islands), see which platforms are compatible, workaround, or incompatible. Click any card for the technical reason why.
2. Cost Reality Calculator -- Sliders for requests (10K-10M), SSR ratio, team size. Live bar chart across 8 platforms. Shows crossover insights ("Vercel costs 74x Cloudflare" at 10M req) and the Vercel pricing cliff warning.
3. Lock-in Exposure -- Feature-by-feature breakdown for Vercel, Cloudflare, Netlify, Railway. Color-coded Easy/Moderate/Significant migration difficulty.
4. Hosting Selector Wizard -- 6-question guided questionnaire producing ranked recommendations with reasoning, warnings, and common mistake detection (e.g., "10M+ requests + zero-ops + free budget doesn't exist").
5. Ops Complexity Spectrum -- Visual left-to-right from GitHub Pages (zero-ops) to EC2/VPS (full control). Click any platform for benefits, tradeoffs, and team fit.
6. Vercel vs Cloudflare -- Side-by-side comparison table with contextual verdict based on framework, scale, and budget priority.
