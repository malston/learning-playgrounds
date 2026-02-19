# Results

1. Framework Selector Wizard -- 5-step conversational questionnaire. Clicking an option auto-advances. Shows top recommendation + 2 alternatives with scores, tradeoffs, warnings, and a "What drove this decision" scoring breakdown with visual bars. Includes copy-to-clipboard.

2. Rendering Strategy Visualizer -- 5 strategies (CSR, SSR, SSG, Islands, Qwik) with animated timelines across Server/Network/Browser swim lanes. FCP and TTI markers appear as the animation sweeps. A mini viewport preview transitions between blank/visible/interactive states. SSR's 850ms hydration gap is viscerally obvious.

3. Reactivity Models Side-by-Side -- React/Svelte/Solid columns with 10x10 node grids. "Trigger Update" flashes all React nodes orange (VDOM scan) then green on changed ones, while Svelte/Solid only flash the changed nodes. Slider from 100-10,000 items shows React's performance bar growing linearly while Solid stays flat.

4. Elimination Bracket -- 6 constraint toggles. "Team knows React" eliminates Nuxt, SvelteKit, SolidStart, Qwik with one-line reasons. "Need islands" leaves only Astro. Status shows remaining count. "Vercel preferred" adds a BEST FIT badge to Next.js without eliminating others.

5. Org Structure Test -- The provocative question with two paths. "Consistency" reveals the Angular value proposition; "Flexibility" reveals the React tradeoff. Both include a "what goes wrong" callout for the wrong org type.
