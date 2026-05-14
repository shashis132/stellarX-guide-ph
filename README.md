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
| **Break** | 15 min | Coffee, form teams, **pick an idea and a track**, finish environment setup |
| **2. Build** | 90 min | Hands-on: build your chosen idea on testnet, using the cheatsheet as the scaffold |
| **3. Demo & Submit** | 15 min | Teams demo, **submit their project**, group reflection, what's next |

Full breakdown, facilitator scripts, mentor notes, and emergency fixes are in **[`stellar-workshop-3hr-plan.md`](./stellar-workshop-3hr-plan.md)**.

**Whatever you build in the 90-minute block is what you submit.** You pick an idea from [`stellar-300-ideas.md`](./stellar-300-ideas.md), choose a [track](./TRACKS.md), build it on the cheatsheet scaffold, and submit it per [`SUBMISSION_GUIDELINES.md`](./SUBMISSION_GUIDELINES.md) before the workshop ends.

---

## Start Here

### If you are a participant (building in the workshop)
1. **[`dev_setup`](./dev_setup)** — get your environment ready *before* the build block. Five minutes here saves hours.
2. **[`stellar-300-ideas.md`](./stellar-300-ideas.md)** — browse this *before* the workshop and shortlist what you might build. You pick your idea at the break.
3. **[`stellar-fullstack-cheatsheet.md`](./stellar-fullstack-cheatsheet.md)** — the step-by-step scaffold you follow during the 90-minute build. Every step has exact code.
4. **[`Starter_prompts.md`](./Starter_prompts.md)** — how to prompt AI correctly if you build with an AI assistant.
5. **[`Free_AI_Setup.md`](./Free_AI_Setup.md)** — no paid AI subscription? Free and low-cost ways to get a strong coding model.
6. **[`SUBMISSION_GUIDELINES.md`](./SUBMISSION_GUIDELINES.md)** — how to submit what you build. Read this so you know the target before you start.

### If you are a facilitator (running the workshop)
1. **[`stellar-workshop-3hr-plan.md`](./stellar-workshop-3hr-plan.md)** — your minute-by-minute run-of-show, including the pre-workshop checklist, live-demo scripts, mentor talking points, and emergency fixes.
2. **[`stellar-fullstack-cheatsheet.md`](./stellar-fullstack-cheatsheet.md)** — the scaffold participants build on in Block 2. Know it well.
3. **[`stellar-300-ideas.md`](./stellar-300-ideas.md)** / **[`TRACKS.md`](./TRACKS.md)** — what participants pick from at the break. Help them scope tight.
4. **[`SUBMISSION_GUIDELINES.md`](./SUBMISSION_GUIDELINES.md)** / **[`JUDGING.md`](./JUDGING.md)** — how projects get submitted and scored. Run the submission step in Block 3.
5. **[`dev_setup`](./dev_setup)** — the gotchas you should warn participants about before they hit them.

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

### Pick, build, submit (used during the workshop)
| File | What it is |
|---|---|
| **[`stellar-300-ideas.md`](./stellar-300-ideas.md)** | 300 build ideas across 13 categories, cross-checked against 8,000+ existing Stellar repos for novelty. **You pick your build idea from here at the break.** |
| **[`TRACKS.md`](./TRACKS.md)** | 6 tracks, mapped to the 300-ideas categories. You choose one track for your submission. |
| **[`SUBMISSION_GUIDELINES.md`](./SUBMISSION_GUIDELINES.md)** | How to submit what you build — submission happens in Block 3, before the workshop ends. |
| **[`JUDGING.md`](./JUDGING.md)** | The rubric your submission is scored against — 6 criteria, 100 points, bonus points. |
| **[`HACKATHON.md`](./HACKATHON.md)** | Hackathon hub — for teams taking their workshop project further into a competition. |
| `stellar_repos.txt` / `stellar.jsonl` / `stellar_repos.csv` / `stellar_breakdown.txt` | The Stellar ecosystem repo export used to novelty-check the 300 ideas — search it to confirm your idea is fresh. |

---

## What You Build in the Workshop

**You build your chosen idea — and submit it.** The flow:

1. **Pick an idea** from [`stellar-300-ideas.md`](./stellar-300-ideas.md) and a [track](./TRACKS.md) at the break.
2. **Build it** in the 90-minute block. The [cheatsheet](./stellar-fullstack-cheatsheet.md) gives you a working scaffold — a fullstack testnet app that connects a wallet, shows XLM/USDC balances, sends a payment, and polls for finality. You bend that scaffold toward your idea.
3. **Submit it** in Block 3 per [`SUBMISSION_GUIDELINES.md`](./SUBMISSION_GUIDELINES.md), scored against [`JUDGING.md`](./JUDGING.md).

Keep scope tight — the goal is a **strong working demo of one core flow**, not a finished product. The cheatsheet gets you to a running app fast with exact, copy-paste code so your remaining time goes into the part that makes your idea *yours*.

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
