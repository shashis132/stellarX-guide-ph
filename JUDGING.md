# Judging — StellarX Philippines

How projects are evaluated, scored, and selected.

> **Organizers:** fields marked `[ORGANIZER: ...]` are placeholders. Adjust weightings, prize amounts, and process details to fit your event.

---

## Judging Philosophy

StellarX Philippines rewards **real products that solve real problems with meaningful use of Stellar** — not the most lines of code, and not the most ambitious slide deck.

A rough but working demo tied to genuine market demand beats a polished pitch with no usable flow. Judges are looking for teams that scoped well, executed cleanly, and used the Stellar ecosystem intelligently.

---

## Scoring Criteria

Every project is scored on **6 criteria, 100 points total**. The same rubric applies across all tracks.

| # | Criterion | Weight | What judges ask |
|---|---|---|---|
| 1 | **Meaningful Use of Stellar** | 25 | Is Stellar core to the product? Does it use Stellar primitives (payments, assets, trustlines, path payments, claimable balances, Soroban, anchors, SEPs) in a way that genuinely matters — not just as a checkbox? |
| 2 | **Problem & Real-World Relevance** | 20 | Does this solve a real problem, ideally one relevant to the Philippines (remittance, payments, financial inclusion, underbanked users)? Is there genuine demand? |
| 3 | **Functionality & Completeness** | 20 | Does the core user flow actually work? Is it deployed and demonstrable on Stellar testnet (or mainnet)? Can a judge use it? |
| 4 | **Technical Execution & Code Quality** | 15 | Is the code well-structured, documented, and maintainable? Is the architecture sound? Is transaction handling done correctly (simulation, polling for finality)? |
| 5 | **Product Thinking & UX** | 10 | Is the product usable, not just conceptual? Are empty states, errors, and loading states handled? Would a real user understand it? |
| 6 | **Presentation & Demo Clarity** | 10 | Does the demo video clearly show the core flow? Is the pitch focused? Does the README explain scope and how to run it? |

**Total: 100 points.**

---

## Bonus Points (up to +10)

Awarded at judges' discretion, on top of the 100-point base:

- **+3 — Ecosystem composability:** meaningful integration with an existing Stellar protocol (Soroswap, Blend, Aquarius, Reflector, an anchor, etc.).
- **+3 — Genuine novelty:** the idea is not already well-covered in the Stellar ecosystem. Teams that checked their concept against [`stellar-300-ideas.md`](./stellar-300-ideas.md) and `stellar_repos.txt` and built something genuinely new should say so in their README.
- **+2 — Underused Stellar primitives:** correct use of claimable balances, path payments, sponsored reserves, clawback, multiplexed accounts, or SEP-7/SEP-31.
- **+2 — Testnet-to-mainnet readiness:** clear, credible path to production, or already deployed on mainnet.

---

## Scoring Bands

| Score | Band | Meaning |
|---|---|---|
| 85–110 | Outstanding | Demo-ready, real product, strong Stellar use. Prize contender. |
| 70–84 | Strong | Works well, clear value, minor gaps. |
| 55–69 | Solid | Core flow works, needs polish or deeper Stellar integration. |
| 40–54 | Early | Partial flow, promising direction, incomplete execution. |
| < 40 | Incomplete | Core flow does not run, or Stellar use is superficial. |

---

## Track Judging

Each of the 6 tracks (see [`TRACKS.md`](./TRACKS.md)) is judged independently using the rubric above. A project competes only in the **one track it selects** at submission.

Judges may, at their discretion, recommend moving a project to a track that fits it better — but will notify the team.

`[ORGANIZER: define per-track prizes, e.g. 1st / 2nd / 3rd per track, or a single overall winner per track.]`

### Cross-Track Awards (optional)
`[ORGANIZER: optionally define special awards, e.g.:]`
- **Best Use of Soroban** — strongest smart contract work across all tracks.
- **Best Philippines-Impact Project** — most credible real-world impact for Filipino users.
- **Best Solo Builder** — strongest project from a one-person team.
- **People's Choice** — voted by fellow participants.

---

## Judging Process

1. **Submission review.** Judges confirm each submission meets the requirements in [`SUBMISSION_GUIDELINES.md`](./SUBMISSION_GUIDELINES.md). Incomplete submissions may be disqualified.
2. **Demo video review.** Judges watch the 2–4 minute demo before any live session.
3. **Live demo / table judging.** `[ORGANIZER: describe — e.g. each team demos live to a small committee of mentors at their table, or presents on stage for N minutes + Q&A.]`
4. **Scoring.** Each judge scores independently on the 100-point rubric. Scores are averaged per project.
5. **Deliberation.** Judges reconcile close scores and confirm bonus points.
6. **Results.** `[ORGANIZER: announcement time and place.]`

---

## Eligibility & Disqualification

A project **must**, to be judged:

- Be built **on Stellar** — Stellar must be core to the product.
- Have **all code in the submitted public GitHub repository** (not just a template fork).
- Be **demonstrably running** on Stellar testnet or mainnet.
- Include a **README** explaining scope and how to run it.
- Include a **demo video** (2–4 minutes).
- Be submitted **before the deadline** via the official method.

A project **will be disqualified** if it:

- Re-uses or plagiarizes a pre-existing project. Substantially pre-built work submitted as new is not allowed.
- Was not built during the hackathon period. `[ORGANIZER: confirm whether prior work / boilerplate is allowed and to what extent.]`
- Does not actually use Stellar in any meaningful way.
- Cannot be run or demonstrated by judges.

> Originality is checked. Projects that re-used or plagiarized other works may be reported and have any award cancelled. Building *on top of* open Stellar protocols (Soroswap, Blend, etc.) is encouraged — copying another team's or another hackathon's project as your own is not.

---

## Tips to Score Well

- **Scope tight.** Build the smallest strong version of your core flow. Judges reward a working demo over a half-built grand vision.
- **Make Stellar matter.** If your project would work identically without Stellar, you will lose 25 points on criterion 1.
- **Test the golden path on testnet** before recording your demo. Judges will try to use it.
- **Handle transactions correctly.** Simulate Soroban txs, poll for finality — `sendTransaction` is not success. See [`dev_setup`](./dev_setup).
- **Write the README for a stranger.** Judges should be able to run your project from the README alone.
- **Keep the demo video focused.** Show the core flow working. Skip the long intro.
- **Check novelty.** Cross-reference your idea against [`stellar-300-ideas.md`](./stellar-300-ideas.md) and `stellar_repos.txt` — and tell judges what makes yours different.

---

*See [`TRACKS.md`](./TRACKS.md) to choose your track and [`SUBMISSION_GUIDELINES.md`](./SUBMISSION_GUIDELINES.md) for how to submit.*
