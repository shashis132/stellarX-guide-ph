# StellarX Philippines — 3-Hour Stellar Workshop

A complete, ready-to-run **3-hour workshop** that takes developers from "what is Stellar?" to a working fullstack Stellar app deployed on testnet.

> **60 minutes learning Stellar → 90 minutes building → 15 minutes demo & reflect.**

---

## What This Is

This repo is everything you need to **run or attend** a 3-hour Stellar developer workshop:

- A minute-by-minute **facilitator plan** for the full 3 hours
- A step-by-step **build cheatsheet** for the 90-minute hands-on block
- The **setup, AI, and reference material** the workshop depends on

The workshop is built for the Philippines context — remittance, payments, and financial inclusion — but the technical content applies to building on Stellar anywhere.

---

## The 3 Hours at a Glance

| Block | Time | What happens |
|---|---|---|
| **1. Learn Stellar** | 60 min | Core concepts, the developer stack, product patterns — with live demos |
| **Break** | 15 min | Coffee, form teams, finish environment setup |
| **2. Build** | 90 min | Hands-on: build a working Stellar payment app on testnet |
| **3. Demo & Reflect** | 15 min | Teams demo, group reflection, what's next |

Full breakdown, facilitator scripts, mentor notes, and emergency fixes are in **[`stellar-workshop-3hr-plan.md`](./stellar-workshop-3hr-plan.md)**.

---

## Start Here

### If you are a participant (building in the workshop)
1. **[`dev_setup`](./dev_setup)** — get your environment ready *before* the build block. Five minutes here saves hours.
2. **[`stellar-fullstack-cheatsheet.md`](./stellar-fullstack-cheatsheet.md)** — the step-by-step guide you follow during the 90-minute build. Every step has exact code.
3. **[`Starter_prompts.md`](./Starter_prompts.md)** — how to prompt AI correctly if you build with an AI assistant.
4. **[`Free_AI_Setup.md`](./Free_AI_Setup.md)** — no paid AI subscription? Free and low-cost ways to get a strong coding model.

### If you are a facilitator (running the workshop)
1. **[`stellar-workshop-3hr-plan.md`](./stellar-workshop-3hr-plan.md)** — your minute-by-minute run-of-show, including the pre-workshop checklist, live-demo scripts, mentor talking points, and emergency fixes.
2. **[`stellar-fullstack-cheatsheet.md`](./stellar-fullstack-cheatsheet.md)** — what participants build in Block 2. Know it well.
3. **[`dev_setup`](./dev_setup)** — the gotchas you should warn participants about before they hit them.

---

## Repo Map

### Core workshop files
| File | What it is |
|---|---|
| **[`stellar-workshop-3hr-plan.md`](./stellar-workshop-3hr-plan.md)** | The full 3-hour facilitator plan: learn block, build block, demo block, plus adaptations for 2hr / 4hr / beginner / advanced formats. |
| **[`stellar-fullstack-cheatsheet.md`](./stellar-fullstack-cheatsheet.md)** | Step-by-step 90-minute build guide for a Stellar payment app — full code, gotchas, testnet reference, AI-assisted path. |

### Supporting material (the workshop and cheatsheet depend on these)
| File | What it is |
|---|---|
| **[`dev_setup`](./dev_setup)** | Stellar testnet setup, contract addresses, SDK patterns, and the critical gotchas that waste build time. |
| **[`Starter_prompts.md`](./Starter_prompts.md)** | How to prompt AI for a Stellar build — context blocks, wallet-vs-app framing, `CLAUDE.md` template, corrective prompts. |
| **[`Free_AI_Setup.md`](./Free_AI_Setup.md)** | Free and low-cost AI setup paths — OpenRouter, Groq, Google AI Studio, Ollama, cheap GPU rental. |

### Going further (after the workshop)
| File | What it is |
|---|---|
| **[`stellar-300-ideas.md`](./stellar-300-ideas.md)** | 300 build ideas across 13 categories, cross-checked against 8,000+ existing Stellar repos for novelty. |
| **[`HACKATHON.md`](./HACKATHON.md)** | Hackathon hub — for teams taking their workshop project into a competition. |
| **[`TRACKS.md`](./TRACKS.md)** | 6 hackathon tracks, mapped to the 300-ideas categories. |
| **[`JUDGING.md`](./JUDGING.md)** | Hackathon judging rubric — 6 criteria, 100 points, bonus points. |
| **[`SUBMISSION_GUIDELINES.md`](./SUBMISSION_GUIDELINES.md)** | How to submit a hackathon project. |
| `stellar_repos.txt` / `stellar.jsonl` / `stellar_repos.csv` / `stellar_breakdown.txt` | The Stellar ecosystem repo export used to novelty-check the 300 ideas. |

---

## What You Build in the Workshop

A fullstack Stellar **payment app** running on testnet that can:
- Connect a wallet
- Show XLM and USDC balances
- Send a payment to any Stellar address
- Poll for transaction finality and show the result

It is intentionally small — the goal is a **strong working demo of a core flow**, not a finished product. The cheatsheet gets you there in 90 minutes with exact, copy-paste code at every step.

---

## Requirements

**Participants need:**
- Node.js 18+ and `npm`
- A browser (Chrome preferred)
- A code editor
- An AI coding assistant — paid or free (see [`Free_AI_Setup.md`](./Free_AI_Setup.md))

**Facilitators need:** see the pre-workshop checklist at the top of [`stellar-workshop-3hr-plan.md`](./stellar-workshop-3hr-plan.md).

---

## Adapting the Workshop

The workshop plan includes appendices for running it as a **2-hour** or **4-hour** session, and for **beginner** (no-code, demo-along) or **advanced** (skip basics, go deeper on Soroban) audiences. See the appendix in [`stellar-workshop-3hr-plan.md`](./stellar-workshop-3hr-plan.md).

---

*Build fast. Launch bigger.*
