# Matthew Goluba

Software engineer and network engineer. I am building **[ThothOS](https://thothos.net)** — business software that builds itself to fit the customer — solo and self-funded, on AI agent infrastructure of my own design.

📫 **mkgoluba@outlook.com**

---

## Background

I am an L3 engineer in enterprise network virtualization support (NSX, HCX, Aria Operations, vCenter, ESXi). Before that I was a Network Virtualization Engineer at VMware, and before that I designed and operated ISP and MSP infrastructure: a wireless ISP serving 800+ subscribers over a redundant 11 GHz OSPF backhaul loop, an LTE deployment on 3.65 GHz, FCC license filings, a regional fiber backbone upgrade from 100 Mbps to 1 Gbps, and the on-premise migration of 100+ hosting customers.

I hold a B.S. in Information Technology, but the working skill set is self-taught — Linux, bash, and routing protocols learned during the ISP years because production networks required it. In 2023–24 I took a deliberate career break and hired a professional engineer at $50/hour for one-on-one instruction in software engineering, funded entirely out of my own pocket. Everything below is the result.

Beyond the public portfolio, the production systems I have designed and operated span several categories: high-throughput PII detection and redaction pipelines (built in Python, converted to Rust for performance, with self-training feedback loops in which user-reported misses propagate automatically back into the detection layer); scheduling and approval platforms serving hundreds of users across global regions; runtime emulators that make cloud-only execution environments fully testable locally, enabling thousands of backend tests to run in seconds against production source; and browser extensions with machine-enforced requirements coverage, where an unmapped requirement fails the build.

---

## AI usage, at scale

I operate a fleet of maxed-out AI subscriptions rather than a metered API budget:

| Subscription | Monthly cost |
|---|---|
| 4× Claude Max 20x | $800 |
| 1× Claude Max 5x | $100 |
| Cursor Pro+ and Cursor Pro | $80 |
| **Total** | **~$1,000** |

Every account runs at its daily, weekly, and total limits each cycle. A Max 20x account driven to its limits works out to **$2,000–3,000 per week** in metered-equivalent usage; four of them plus the Max 5x is **$8,500–12,750 per week — $40–50k per month on the conservative end, roughly one trillion tokens over the past six months** — for about $1,000 in subscriptions. The fleet was five Max 20x accounts until recently and scales to five to seven in September, which puts the metered-equivalent ceiling near $90k per month. This is not token maxxing; it is pushing AI to the edge of what is possible without seed funding.

The output is measurable: **10,578 commits to ThothOS in 2026 alone** (15,868 total since 2023) — more code shipped this year than in all prior years combined — across roughly **1.8 million lines of maintained code**: ~1.5M TypeScript, ~215K C++/CUDA, ~36K Go.

---

## ThothOS — [thothos.net](https://thothos.net)

**Business software that builds itself to fit you.** The published roadmap is three phases: build a complete business platform; teach it to build itself; launch it publicly. The first two phases are delivered and live; the public launch is in progress for 2026.

Current state, built solo:

- **~1.1 million lines of TypeScript across 23 integrated workspaces** — invoicing, contracts with e-signature, scheduling, inventory, point-of-sale on Stripe Terminal, email marketing, learning management, IPAM/network administration, and customer and employee portals over one shared data layer.
- **218 entity models declared once, with everything derived.** Each domain object is a single manifest; code generation emits the Mongoose model, GraphQL resolvers and routes, cached server readers, TanStack Query hooks, and Zod validation. The model, API, types, and forms cannot structurally drift.
- **First-party authentication** — passwordless email/SMS/TOTP, 384-bit session tokens SHA-256-hashed at rest, OIDC and SAML 2.0 SSO with SCIM provisioning. No Auth0, Clerk, or NextAuth.
- **The homepage demo is the production builder.** An anonymous visitor's build request runs the real builder tools against a disposable sandbox, constrained by a 21-tool allowlist verified per-tool by write target, a denylist covering everything that publishes, writes records, moves money, or reaches the network, and a build-time check that the two sets never intersect.
- **AI-built workspaces are identical to hand-built ones, and a machine verifies it.** The builder writes configuration that activates the same canonical routes, components, and server data paths as the shipped product — not generated lookalikes. A parity harness compares the chat-built company, employee, and customer experience against the hand-built reference: **105 parity cells, green, deterministic across consecutive full sweeps.**
- **The agent operates the business as well as building it** — roughly 66 tool modules spanning accounting, contracts, POS, shipping, network administration, loyalty, inventory forecasting, and scheduling.

The architecture is deliberately built ahead of current AI economics. Model selection is centralized in one configuration file, the LLM transport is swappable, AI usage is metered and billed per tier, and capability boundaries are policy objects. Those dials are set where today's inference costs make sense; as costs fall, they turn up. The end state — a full AI-builder platform — is designed and wired now, and expands on a price curve rather than a rewrite.

---

## cat-annihilation — [github.com/goobz22/cat-annihilation](https://github.com/goobz22/cat-annihilation)

This project is two complete implementations of the same game, sharing nothing but the design.

The first is a finished browser game built on **Three.js / React Three Fiber** (React 19, Zustand): wave-based enemy AI with projectile and collision systems, procedurally placed terrain with its own collision layer, a day/night cycle, an NPC and story-encounter layer with quest tracking and dialog, elemental spells with an inventory hotbar, character customization with equipment, mobile support with a virtual joystick, and persistent save state — in roughly 10K lines across 45 modules, including defensive details like per-entity error boundaries that recover a crashed enemy without tearing down the running wave.

The second is a **from-scratch C++20 / Vulkan / CUDA engine** — no Unity, Unreal, or third-party renderer — built to run the same game natively and to understand the full path from draw call to pixel. The web build serves as the specification: every gameplay constant in the native engine is cited to the exact web source line, and rendered output is pixel-compared against the running web game.

The engine includes: a render graph with per-resource barrier insertion (topological pass sorting, transient resource lifetimes); clustered deferred PBR with a 16×9×24 compute-assigned light grid; cascaded shadow maps with a Guillotine-packed atlas whose pack density is asserted by unit test; CUDA rigid-body physics (spatial-hash broadphase, GJK/EPA narrow phase) beyond 10,000 bodies; a million-particle CUDA simulation with full stream compaction; a custom ECS on C++20 concepts; a work-stealing job scheduler; SIMD math; custom allocators with zero dynamic allocation in the render loop; and GPU profiling via Vulkan timestamp queries.

**452 C++/CUDA source files; 7.7 million test assertions across 1,200+ deterministic, replayable cases**, with a fully headless test harness that drives the engine through scripted input and screen-state assertions.

---

## Engineering methodology

Spec-, test-, and bug-driven, enforced by tooling rather than convention:

- **Bugs are treated as classes, not instances.** Confirming a bug means first writing a detection script that enumerates the same shape across the codebase — each script carries a self-test proving it flags the defective pattern and stays silent on the compliant one, and prints its scan denominator so zero findings can never mean zero coverage. The class is then fixed at the root, architecturally where possible, and the detector joins the permanent build gate. ThothOS runs **468 such guards**.
- **Tests must fail first.** A regression test is written against the defect and confirmed red before the fix; tests are never edited to reach green; the reviewer of a change is a fresh context, never its author.
- **Every commit and push is AI-reviewed.** Machine-wide git hooks run a headless model review over each commit and push, with content-addressed verdict caching and diff sub-batching so large changes cannot silently bypass the gate. One month of telemetry: 562 reviews, 25 blocks.
- **Release readiness is a computed number, not a judgment.** The ThothOS readiness scoreboard declares **58 gates across a 524-dimension audit universe**; a declared but unmeasured gate is counted as unknown and blocks shipment, skipped tests never count as green, and launch is an RC go/no-go on a tagged release candidate (deterministic repeats, severity tiers, soak, drills) — not a calendar streak of green nights.
- **The measuring instruments are audited with the same rigor as the product** — verified against planted defects, checked for unreachable verdicts and vacuous scans.

The same methodology runs across unrelated stacks: a Next.js platform, browser extensions, a Go network monitor, and a C++/CUDA engine.

---

## Agent infrastructure

The volume above is only sustainable because the agents run inside engineered constraints:

- **A hardened harness for Claude Code**: enforcement hooks that hard-block destructive git operations (verified by a 63-case bypass test suite), a three-tier memory architecture, and a shared-tree protocol allowing multiple agents to work one repository concurrently with automatic checkpointing.
- **Autonomous programs** for long-running work: sweep-and-fix (root-cause every failure), product-truth evaluation (drive each user persona through the real product and grade delivered value, not test results), and security auditing (threat-model-driven tenant-isolation testing) — with durable, committed backlogs so findings survive context loss.
- **openclaw**, an autonomous engineering orchestrator built on the Claude Agent SDK: ~102K lines, **583,026 orchestration events, 86,261 completed goals**, a reward-hacking monitor with 116 confirmed detections, and an independent verifier that overrode **56 of 111 agent-claimed successes**.
- **[@goobz22/claude-runner](https://www.npmjs.com/package/@goobz22/claude-runner)** — unattended, resumable route-by-route browser testing.

---

## Open source

| Project | Description |
|---|---|
| [Network-Monitor](https://github.com/Technologies-Unlimited/Network-Monitor) | Distributed network monitoring in Go — ~36K lines, gRPC node mesh with heartbeat lifecycle, 10+ Gbps distributed bandwidth testing, SNMPv3, raw ICMP, 4,424 OIDs across 122 vendor templates, 14 diagnostic tools. |
| [goobs-frontend](https://github.com/goobz22/goobs-frontend) | A complete React design system on [npm](https://www.npmjs.com/package/goobs-frontend) — 100+ components, 264 icons, CSS-module design tokens, a zod-native form engine, seven runtime dependencies. Consumed from npm in ThothOS production. [Storybook.](https://storybook.technologiesunlimited.net/) |
| [cat-annihilation](https://github.com/goobz22/cat-annihilation) | The C++20/Vulkan/CUDA engine described above. |
| [data-redactor](https://github.com/goobz22/data-redactor) | Presidio-backed PII redaction for text, images (OCR), and PDF before data reaches an AI system — token, mask, and format-preserving strategies; reversible encrypted redaction. [Live demo.](https://data-redactor-ui.vercel.app/) |
| [next-smart-hmr](https://github.com/Technologies-Unlimited/next-smart-hmr) | Route-aware HMR for Next.js ([npm](https://www.npmjs.com/package/next-smart-hmr)) — dependency-graph-driven so only affected tabs refresh; measured at ~25ms full graph build over 7,785 edges on a 2,301-file application. |
| [HomeSchoolApp](https://github.com/Technologies-Unlimited/HomeSchoolApp) | Event-management platform for homeschool communities (Next.js 16, React 19, MongoDB). |

I also learn by building: **Flake**, a social application for connecting groups around shared interests, was the vehicle for learning Expo and React Native.

---

## On funding

Mount Rushmore was designed to the waist — Borglum's final model included torsos, an inscription, and a Hall of Records. Because Congress funded it, Congress set the scope: in 1939 it directed that federal money be spent on the faces only, and in 1941 the funding ended and the monument was declared complete at $989,992.32. Seventeen miles away, Korczak Ziolkowski twice declined $10 million in federal funding for Crazy Horse; the project is slower, but no outside party can redefine its scope, and the full figure remains the plan.

That is the reasoning behind self-funding ThothOS. Outside capital without contractual protection of scope means the funder ultimately decides what "finished" looks like. I will take investment when its terms guarantee the complete vision gets built; until then, roughly $1,000 per month in subscriptions buys full sovereignty over scope — with two advantages neither monument had: the cost of the work falls every quarter as inference prices drop, and completion is not open-ended, because launch is defined by a machine-computed RC go/no-go on a tagged release candidate — not a calendar streak of green nights.

Verified, not asserted.

---

**Matthew Goluba** · Technologies Unlimited · **mkgoluba@outlook.com**
