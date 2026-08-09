# Matthew Goluba

**I'm building a business platform that builds itself — solo, self-funded, on an AI agent fleet I engineered myself. This page is the war plan, and the receipts.**

I'm a network engineer by trade — L3 at Broadcom (VMware VCF), NSX/HCX, permanent seat on the P1 critical-outage team. Everything I know is self-taught. Before AI existed I stayed up all night learning bash, Linux, and routing protocols because a wireless ISP had to work in the morning, and I programmed microwave radios in production. I love RF. I love this field. In 2023 I took a year off and went into debt paying an engineer $50/hour to teach me to write software the same way. This year, the career that apprenticeship earned me paid off the debt that funded it — the bet closed its own loop. Now I'm ramping.

📫 **mkgoluba@outlook.com**

---

## The war chest

I don't have seed funding. I have a paycheck and arithmetic.

| What I pay for | Roughly |
|---|---|
| 4× Claude Max 20x ($200/mo each) | $800/mo |
| 1× Claude Max 5x | $100/mo |
| Cursor Pro+ and Cursor Pro | $80/mo |
| **Total subscriptions** | **~$1,000/mo** |

Every one of those accounts is pinned at its daily, weekly, **and** total limits, every cycle. Last week it was five Max 20x accounts; by September it'll be five to seven. Run the math on what that usage would cost metered and it's **$40–50k/month of inference, low-side estimate — roughly a trillion tokens over the last six months** — driven for about a thousand dollars.

This isn't token maxxing. **This is pushing AI to the edge of what is possible without seed funding.**

And it shows up in the git history: **10,578 commits to ThothOS in 2026 alone** (15,868 total since 2023) — I have shipped more code this year than every previous year of my life combined. Across active projects I write and maintain roughly **two million lines of code**: ~1.5M TypeScript, ~215K C++/CUDA, ~200K Google Apps Script (app + tests), ~36K Go.

---

## The objective: ThothOS → [thothos.net](https://thothos.net)

**Business software that builds itself to fit you.**

The plan, verbatim from the [Master Plan](https://thothos.net/website/master-plan): *build a complete business platform; teach it to build itself; then launch it to the world.* Phases one and two are delivered and live. Phase three — public launch — is in progress, 2026.

What exists today, solo-built:

- **~1.1 million lines of TypeScript. 23 integrated workspaces. One conversation to assemble them.** Invoicing, contracts with e-signature, scheduling, inventory, point-of-sale on Stripe Terminal, email marketing, learning management, IPAM/network administration, customer and employee portals — one shared data layer, nothing glued together.
- **218 entity models declared once, everything derived.** Each domain object is a single manifest; codegen emits the Mongoose model, the GraphQL resolvers and routes, the cached server readers, the TanStack Query hooks, and the Zod form validation. The model, API, types, and forms structurally cannot drift.
- **Custom auth, no Auth0/Clerk/NextAuth**: passwordless email/SMS/TOTP, 384-bit tokens SHA-256-hashed at rest, first-party OIDC and SAML 2.0 SSO with SCIM provisioning.
- **The homepage try-it is the real builder.** Type "I run a dog grooming business" or paste your website's URL and the same agent loop that powers the in-app builder assembles a working preview. It runs the real builder tools against a disposable sandbox: a 21-tool allowlist where every entry is code-verified by its *write target* (tool names lie — a draft-sounding tool that writes published state is denied), a denylist of everything that publishes, writes records, touches money, or reaches the network, and a build-time check that the two sets can never intersect.
- **Chat-built means identical, and a machine proves it.** When the AI activates a workspace, it doesn't generate a lookalike — it writes configuration that switches on the same canonical route, the same components, the same server data path as the hand-built product. A parity harness drives the builder against a fresh tenant and machine-compares the resulting company, employee, and customer experience against the hand-built reference: **105 parity cells, green, deterministic across consecutive full sweeps.** One sentence like "I need a permit tracker" produces staff CRUD, a customer self-view, and an employee view over the *same* records, every request re-checking tenant and audience.
- **The AI already operates the business, not just the builder.** The agent tool surface spans accounting, contracts, POS, shipping labels, network administration, loyalty, gift cards, inventory forecasting, scheduling, webhooks — ~66 tool modules, with the core builder module alone at 11,362 lines.

Here's the part that matters strategically: **the platform is built to meet AI where it is, with the dials exposed.** Model selection lives in one config file. The LLM transport is swappable. AI usage is metered and billed per tier. The sandbox allowlist is a policy object. Today those knobs are set where current inference costs make sense. Every time the cost curve drops, I turn them up — and the endgame is a full AI-builder platform whose capabilities expand on a price curve I don't control but have already designed for. I'm not waiting for the future to build for it; the mountain is already mapped.

---

## The proving ground: [cat-annihilation](https://github.com/goobz22/cat-annihilation)

I wrote a wave-survival game in Three.js/React Three Fiber — one cat versus escalating hordes of dogs. Finished it, shipped it. Then I decided the only honest way to understand what happens between a draw call and a pixel was to build the entire engine underneath it myself.

So I'm converting my own finished Three.js game into a **from-scratch C++20 / Vulkan / CUDA engine**. No Unity, no Unreal, no bgfx. The browser build is the spec; the native engine is the implementation under test — every gameplay constant is cited to the exact web source line, and the rendered output is pixel-matched against the running web game (the ground color is within one unit of green).

What "from scratch" means here:

- A render graph with real per-resource barrier insertion (Kahn topological sort, transient lifetimes, GraphViz export of the frame)
- Clustered deferred PBR — 16×9×24 light clusters assigned in a compute shader, hand-written Cook-Torrance BRDF
- Cascaded shadow maps with a Guillotine-packed shadow atlas whose ≥80% pack density is asserted by a unit test
- CUDA rigid-body physics (spatial-hash broadphase, GJK/EPA narrow phase) scaling past 10,000 bodies, plus a million-particle CUDA sim with full stream compaction
- Custom ECS on C++20 concepts, work-stealing job scheduler, SSE4.1 SIMD math, custom allocators — zero dynamic allocation in the render loop
- Real GPU timing via Vulkan timestamp queries — not CPU timing pretending to be GPU

**452 C++/CUDA source files. 7.7 million test assertions across 1,200+ cases, deterministic and replayable.** The forest has 18,931 individually simulated tree colliders, because I told the agent to "really hone in" and that directive is now a load-bearing code comment. The audio engine is fully built and disabled by default, because a mid-render crash used to leave a buffer screaming through the speakers. There is a Claude subagent in the repo called the *TODO Annihilation Commander*, and the codebase contains zero TODOs.

---

## The day job: Broadcom

L3 Technical Support Engineer, VCF Global Support — NSX, HCX, Aria Operations, vCenter, ESXi. P1 critical-outage team. One of two engineers on the org's technical-support coding team. The day job funds the war. It also gets the same engineering:

- **A customer-data redaction service for the CRM platform** — built on Microsoft Presidio in Python, then **successfully converted to Rust** for performance, with a **self-training recursive loop**: when a user flags a missed redaction, the miss propagates back into the detection layer automatically, so the system learns to redact what its users catch. (The same obsession runs in my public [data-redactor](https://github.com/goobz22/data-redactor) — Presidio-backed, text/image/PDF, reversible encrypted redaction, [live demo](https://data-redactor-ui.vercel.app/).)
- **The org's production time-off and scheduling platform**, live across three global regions for ~200 engineers. It's Google Apps Script — and it has a purpose-built **emulator of the GAS runtime** (a fake Apps Script VM over SQLite) so the real production source runs 2,500+ backend tests locally in seconds, against code Google normally only executes in its own cloud. **83,919 lines of test code — more test than app.** Every release passes three gates: a 12,795-line static analyzer I wrote (40 rules, zero errors/warnings/info required), the full test suite, and a 39-golden-DOM Playwright pass.
- **A Manifest V3 Chrome extension** that restores Bugzilla-grade advanced search inside Jira Data Center — generates JQL and translates JQL back to plain English. Zero build step, zero runtime dependencies, ~13K lines of plain JS, a **599-check smoke suite**, machine-enforced requirements coverage (an unmapped requirement fails the build), and its own zero-dependency fake Jira server that models the traps: form-urlencoded-only endpoints, session expiry as a 302-into-HTML-200, HTTP-date `Retry-After`.

---

## Doctrine: how I build

Spec, test, and bug driven — enforced by machinery, not by discipline.

- **A bug is a class until a script proves it a one-off.** On confirming any bug, I write the detection script *first* — with a self-test proving it flags the bad shape and stays quiet on the good one, and a printed denominator so "0 findings" can never mean "0 files scanned." Then I fix the entire class at the root, architecturally where possible, and wire the detector into the build gate as a permanent fence. ThothOS runs **468 of these bespoke guards**; nearly every one tests itself before it tests the codebase.
- **Anti-reward-hacking is written into the loop.** A test must fail before the fix — that's the proof it tests the real thing. Never edit a test to reach green. **The grader is never the writer**: an adversarial agent in a fresh context reviews every diff against the spec.
- **Every commit and push on my machine is AI-reviewed.** Global git hooks run a headless Claude review over every commit and every push — content-addressed verdict caching, big diffs sub-batched so a timeout can't silently pass the gate, verdicts parsed defensively after a live incident where a review *quoting* "VERDICT: BLOCK" in prose false-blocked a pass. One month of telemetry: 562 reviews, 25 blocks.
- **"Ready to ship" is a machine-computed number.** ThothOS's readiness scoreboard declares a universe of **58 gates and 524 audit dimensions** — and a declared-but-unmeasured gate materializes as unknown, counted, and score-dragging. It caught its own earlier version printing 83% while four launch-blocking gates were wired to nothing. Skipped tests never count as green. The launch requires every gate green and *held* for 14 consecutive nightly runs.
- **The instrument gets audited too.** Every dominant product bug class gets re-run against the measuring apparatus itself — gates computing verdicts nothing reads, parsers matching one header, vacuous guards scanning zero files. Detectors for the detectors, wired into the same gate.

One methodology, five very different stacks: a Next.js platform, a Google Apps Script monolith, a Chrome extension, a Go network monitor, a C++/CUDA engine.

---

## The fleet

Most people prompt AI. I built the governance so a fleet of agents can ship unsupervised:

- **A hardened harness around Claude Code**: enforcement hooks as hard walls (destructive git operations are blocked outright, with a 63-case bypass test suite proving the walls hold), a three-tier memory architecture (always-on domain model, path-scoped rules, an indexed learnings corpus), and a shared-tree collaboration protocol so multiple agents and I can work one repo simultaneously — auto-save checkpoints, mandatory commits, session-scoped safety nets.
- **Autonomous loop programs** that run for hours: sweep-and-fix (root-cause every failure, never just re-run), product-truth (drive every persona through the real product and grade whether *value* lands, not whether tests pass), and security audit (threat-model-driven tenant-isolation attacks). Findings land in a committed backlog so nothing evaporates with the context window.
- **openclaw** — my autonomous engineering orchestrator on the Claude Agent SDK: ~102K lines in three months, **583,026 orchestration events, 86,261 completed goals**, a reward-hack trajectory monitor with 116 real firings, and an untamperable verifier that **overrode 56 of 111 agent-claimed successes**. Agents lie; the verifier doesn't.
- **[@goobz22/claude-runner](https://www.npmjs.com/package/@goobz22/claude-runner)** (npm) — unattended route-by-route browser testing driven by Claude: self-recovering, resumable, runs overnight.

The telemetry works both directions — it once caught a feedback loop duplicating ~430K tokens per two-hour campaign. You don't run a fleet on vibes.

---

## Open source

| Project | What it is |
|---|---|
| [Network-Monitor](https://github.com/Technologies-Unlimited/Network-Monitor) | Distributed network monitoring in Go — ~36K lines, gRPC node mesh with heartbeat lifecycle, **10+ Gbps bandwidth testing** over parallel streams, SNMPv3, raw ICMP, **4,424 OIDs across 122 vendor templates**, 14 diagnostic tools. Runs standalone or as a proxy for ThothOS's network workspace. |
| [goobs-frontend](https://github.com/goobz22/goobs-frontend) | My own React design system on [npm](https://www.npmjs.com/package/goobs-frontend) — 100+ components, 264 icons, CSS-module tokens (no MUI, no Tailwind), a zod-native form engine (no Formik/RHF), IPAM field types, 7 runtime deps. Consumed from npm in ThothOS production. [Live Storybook.](https://storybook.technologiesunlimited.net/) |
| [cat-annihilation](https://github.com/goobz22/cat-annihilation) | The C++20/Vulkan/CUDA engine above. |
| [data-redactor](https://github.com/goobz22/data-redactor) | Presidio-backed PII redaction before your data reaches an AI — text, images (OCR), PDF; token/mask/format-preserving strategies; reversible encrypted redaction. [Live demo.](https://data-redactor-ui.vercel.app/) |
| [next-smart-hmr](https://github.com/Technologies-Unlimited/next-smart-hmr) | Route-aware HMR for Next.js ([npm](https://www.npmjs.com/package/next-smart-hmr)) — dependency-graph-driven, so only affected tabs refresh. Measured on a 2,301-file app: ~25ms full graph build over 7,785 edges, <10ms incremental. Built because my agent fleet edits one tree concurrently while I watch other tabs. |
| [HomeSchoolApp](https://github.com/Technologies-Unlimited/HomeSchoolApp) | Full event-management platform for homeschool communities (Next 16, React 19, MongoDB) — co-built with my sister. |

I learn by shipping: **Flake**, a social app for connecting groups around shared interests, exists because I wanted to teach myself Expo. That's the same pattern as the radios, the $50/hour lessons, and everything above it.

---

## The strategy: two mountains

Mount Rushmore was designed to the waist. Borglum's final plaster model shows four presidents with torsos, suits, and hands, an 80×120-foot inscription, and a Hall of Records carved into the canyon behind the heads. Congress funded it — so Congress set the scope. In 1939 it directed that federal money be spent on the faces only. Borglum died in March 1941; that October the money stopped and the funder declared the monument complete, at $989,992.32. The most famous monument in America is an unfinished project whose funder decided the heads were the vision.

Seventeen miles away, Korczak Ziolkowski — who had worked under Borglum and watched him fight for every penny — started carving Crazy Horse, and **twice refused $10 million in federal funding**. Admission fees and donations only. It's slower. There's no completion date. But nobody on earth can order it finished at the heads, and the whole 563-foot figure is still the plan, generations later.

That is exactly why ThothOS is self-funded. Taking money without a guarantee is putting Congress on your cap table: whoever holds the gold eventually says "faces only," and you ship the heads while the funder calls it the monument. I'd take funding the day the treaty is guaranteed — terms under which the full body gets carved no matter whose patience runs out. Until then, my thousand dollars a month of maxed-out subscriptions is the admissions booth, and it buys me the one thing better-capitalized builders don't have: **nobody can truncate the vision.**

And my mountain has an advantage neither monument had — **the cost of carving falls every quarter.** The full body is already in the plaster model: the AI dials in ThothOS are designed, wired, and waiting on a price curve. Where Rushmore's funding decided the heads were enough, and Crazy Horse trades speed for sovereignty, I keep sovereignty *and* the curve. And unlike a monument with no completion date, mine has a machine-computed definition of finished — a readiness scoreboard that must hold green for 14 consecutive nights before launch.

Verified, not asserted. That's the whole doctrine — the monument, the platform, the engine, all of it.

---

**Matthew Goluba** · Technologies Unlimited · **mkgoluba@outlook.com**

