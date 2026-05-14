# 3-Hour Stellar Developer Workshop Plan

> **Format:** In-person or hybrid | **Audience:** Developers with basic web dev skills, minimal blockchain experience | **Goal:** Leave with a deployed Stellar app on testnet

---

## Workshop Overview

| Block | Duration | What happens |
|---|---|---|
| Block 1: Learn Stellar | 60 min | Concepts, architecture, live demos |
| Break | 15 min | Coffee, questions, group into teams, pick an idea + track |
| Block 2: Build | 90 min | Hands-on build of the team's chosen idea, on the cheatsheet scaffold |
| Block 3: Demo & Submit | 15 min | Each team demos, submits their project, lessons learned |
| **Total** | **~3 hours** | |

> **The workshop ends in a submission.** Each team picks an idea from `stellar-300-ideas.md` and a track from `TRACKS.md` at the break, builds it during Block 2 using `stellar-fullstack-cheatsheet.md` as the scaffold, and submits it in Block 3 per `SUBMISSION_GUIDELINES.md`. Whatever the team has built at the end of Block 2 is what gets submitted.

---

## Pre-Workshop Setup (Organizer Checklist)

Do these **before the room fills up**:

- [ ] Share this link in the chat: `https://freighter.app` (attendees install Freighter)
- [ ] Confirm WiFi is working and share credentials
- [ ] Have a Stellar testnet address pre-funded and visible on screen for live demos
- [ ] Open Stellar Laboratory at `https://laboratory.stellar.org` on presenter laptop
- [ ] Open Stellar Expert at `https://stellar.expert/explorer/testnet`
- [ ] Verify that `https://friendbot.stellar.org` is reachable on the network
- [ ] Ensure at least one mentor has Freighter installed and an account ready
- [ ] Print or share the `stellar-fullstack-cheatsheet.md` link with all attendees
- [ ] Share `stellar-300-ideas.md`, `TRACKS.md`, and `SUBMISSION_GUIDELINES.md` links — attendees should skim ideas before the break
- [ ] Confirm the submission channel is open (the GitHub repo's Issues tab — see `SUBMISSION_GUIDELINES.md`)
- [ ] Have Node.js 18+ install instructions ready as a backup slide

---

## Block 1: Learn Stellar — 60 Minutes

### Module 1.1 — What is Stellar and Why Does It Matter? (15 min)

**Facilitator notes:** Lead this conversationally. Do not just read slides. Ask the room questions.

---

**Opening question (2 min)**

Ask the room:
> "Who has sent money internationally in the last year? How long did it take? How much did you pay in fees?"

Let 2–3 people answer. Write the numbers on the board.

Then say:
> "What if it took 5 seconds and cost $0.0001? That's what we're building on today."

---

**What is Stellar? (5 min)**

Key points to cover:

- Stellar is a **layer-1 blockchain** designed for financial applications — not general-purpose computation first
- Native support for **assets** (tokens, stablecoins, real-world assets) baked into the protocol
- **Finality in ~5 seconds**, fees under $0.0001 per transaction
- Used in production by: MoneyGram, Wirex, Circle (USDC), and dozens of anchors globally
- In the Philippines context: largest OFW remittance market in Southeast Asia → Stellar is directly relevant

**Stellar vs. other blockchains (quick chart, 2 min)**

| | Stellar | Ethereum | Solana |
|---|---|---|---|
| Focus | Payments, assets | Smart contracts | High throughput |
| Finality | ~5 seconds | ~15 seconds | ~0.4 seconds |
| Fee | < $0.0001 | $0.50–$50+ | ~$0.00025 |
| USDC native? | Yes (Circle anchor) | Yes (ERC-20) | Yes (SPL) |
| DeFi maturity | Growing (Soroban) | Mature | Mature |

The story: Stellar is **the best chain for real-money financial products** in emerging markets.

**Relevant Philippine context (3 min)**

- $36B+ in annual remittances to the Philippines
- 70% of Filipinos are underbanked or unbanked
- GCash and Maya show that mobile-first finance works here
- Stellar enables builders to create the *infrastructure layer* those products need

---

**Module 1.2 — How Stellar Works: Core Concepts (20 min)**

Walk through each concept with a live demo on Stellar Laboratory.

---

**Accounts (4 min)**

- Every Stellar account has a **keypair**: public key (G...) and secret key (S...)
- Public key = your address. Share freely.
- Secret key = never share, never commit to git, never log
- Accounts need a **minimum balance of 1 XLM** to exist (base reserve)

**Live demo:**
```
Go to https://laboratory.stellar.org/#account-creator?network=test
Generate a keypair. Show the public key starts with G, secret with S.
Copy the public key and fund it via Friendbot.
```

---

**Assets (4 min)**

Three types of assets on Stellar:

1. **XLM** — native asset, used for fees and reserves
2. **Classic assets** — issued on Stellar (e.g., USDC, EURC, PHP tokens)
3. **Soroban tokens** — ERC-20-like tokens via smart contracts

Key concept: **trustlines**
- Before a wallet can receive a non-XLM asset, it must explicitly add a trustline for it
- This is a one-time operation per asset per account

**Live demo:**
```
On Stellar Laboratory → Build Transaction → Add Change Trust operation.
Show the structure: asset code + issuer address.
```

---

**Transactions (4 min)**

- A transaction contains one or more **operations**
- Operations: Payment, Change Trust, Create Account, Manage Offer, Invoke Contract, etc.
- Transactions are **signed** with the sender's secret key
- Then **submitted** to the network
- You get a transaction hash → poll until confirmed

The flow:
```
Load account → Build transaction → Simulate (Soroban only) → Sign → Submit → Poll for result
```

**Gotcha to highlight:**
> "Submitting a transaction does NOT mean it succeeded. You must poll for the final status. This trips up almost everyone their first time."

---

**The Horizon API vs Soroban RPC (4 min)**

| | Horizon API | Soroban RPC |
|---|---|---|
| What it is | REST API for classic Stellar operations | JSON-RPC for smart contracts |
| Use for | Balances, history, classic assets | Contract calls, simulations, modern flow |
| SDK class | `Horizon.Server` | `rpc.Server` |
| URL (testnet) | `https://horizon-testnet.stellar.org` | `https://soroban-testnet.stellar.org` |

**Rule of thumb:**  
Use Soroban RPC for everything contract-related. Use Horizon for balance history and legacy lookups.

---

**Soroban Smart Contracts (4 min)**

- Soroban = Stellar's smart contract layer
- Contracts written in **Rust**
- Much cheaper and faster than EVM contracts
- You invoke contracts via the Soroban RPC

Key protocols built on Soroban:
- **Soroswap** — DEX and aggregator
- **Blend** — Lending and borrowing
- **Aquarius** — AMM and liquidity
- **Phoenix** — Swap protocol
- **Reflector** — On-chain price oracle

You don't need to write Rust to use Soroban today. You call existing contracts from your frontend using the SDK.

---

**Module 1.3 — The Developer Stack (10 min)**

**SDK Overview (3 min)**

```bash
npm install @stellar/stellar-sdk
```

The only SDK you need. In v14, the namespace structure is:
```typescript
import { rpc, Networks, Asset, TransactionBuilder, Operation } from '@stellar/stellar-sdk';

// Correct v14 pattern
const server = new rpc.Server('https://soroban-testnet.stellar.org');

// WRONG — old pattern (still in many examples online)
// const server = new SorobanRpc.Server(...)
```

**Wallets (2 min)**

- **Freighter** — Browser extension. Best for apps where the user brings their own keys.
- **Self-custodial pattern** — Your app generates and encrypts a mnemonic locally. No extension needed.
- **Passkey wallets** — WebAuthn-based. No seed phrases. Most user-friendly.

For the workshop today, we use **Freighter** — fastest to integrate.

**Tools quick tour (5 min)**

Show these briefly in the browser:

| Tool | URL | What it's for |
|---|---|---|
| Stellar Laboratory | `https://laboratory.stellar.org` | Build & inspect transactions |
| Stellar Expert | `https://stellar.expert/explorer/testnet` | Block explorer |
| Friendbot | `https://friendbot.stellar.org?addr=G...` | Fund testnet accounts |
| Stellar Docs | `https://developers.stellar.org` | Official reference |
| Stella (AI) | On docs site | Stellar-specific AI assistant |

**Live demo:**
```
1. Paste the pre-funded testnet address into Stellar Expert.
2. Show the account, balances, and transaction history.
3. Go to Friendbot and fund a fresh account live.
4. Show the funded account appear on Stellar Expert.
```

---

**Module 1.4 — Product Patterns (15 min)**

Walk through the four main product patterns devs build on Stellar.

---

**Pattern 1: Payment App (3 min)**

```
User → Connect wallet → Enter destination + amount → Sign tx → Confirm onchain
```

Example use cases: merchant payments, bill splitting, OFW sending money home

Key Stellar primitives: `Operation.payment`, classic assets, Freighter signing

---

**Pattern 2: Remittance Flow (4 min)**

```
Sender (PHP) → Anchor (fiat → USDC) → Stellar rails → Receiver → Anchor (USDC → local currency)
```

What makes Stellar good for this:
- Pathfinding: Stellar finds the best conversion path automatically
- SEP-24 / SEP-6: Standardized anchor deposit/withdrawal protocols
- 5-second settlement vs. 2-5 days for SWIFT

Key Stellar primitives: `Operation.pathPaymentStrictSend`, anchor integration, SEP-24

**Draw this on the whiteboard:**
```
[Alice in PH] → [Anchor PH] → [XLM/USDC] → [Anchor US] → [Bob in US]
                  USDC on Stellar               
                  ←————— 5 seconds ——————→
```

---

**Pattern 3: Savings / DeFi (4 min)**

```
User → Deposit asset → Protocol (Blend/Soroswap) → Earn yield → Withdraw
```

Key Stellar primitives: Soroban contract invocation, token approvals, Blend or Soroswap SDK

Example: A savings app that deposits idle USDC into Blend to earn interest, shown in a simple UI.

---

**Pattern 4: Wallet Product (4 min)**

Self-custodial wallets are different from apps:
- App generates the mnemonic locally
- Password → key → AES encryption of mnemonic → stored in localStorage
- On login: decrypt mnemonic → derive keypair → sign all transactions in-browser

This is more complex but gives the user full custody. No browser extension dependency.

Passkey variant:
- WebAuthn passkey → Soroban auth entry → Smart account
- Most user-friendly: no seed phrase, no extension

---

**Q&A Pause (5 min)**

Open the floor for questions. Common ones:
- "Can I use Stellar on mobile?" → Yes, via native SDKs or PWA
- "How do I add USDC specifically?" → Via trustline, then use the right issuer address
- "Do I need to know Rust for Soroban?" → Only if writing contracts. Calling them from JS requires no Rust.
- "What's the difference between mainnet and testnet?" → Testnet is free (Friendbot), reset periodically, for development

---

## BREAK — 15 Minutes

Organizer actions during break:
- [ ] Share the cheatsheet file/link in the group chat
- [ ] Ask people to form teams of 2–3 if going team-based
- [ ] **Have every team pick an idea from `stellar-300-ideas.md` and a track from `TRACKS.md`** — this is what they build and submit
- [ ] Tell teams to scope tight: the smallest strong demo of their idea, not the whole product
- [ ] Install Freighter and create a testnet account if anyone hasn't already
- [ ] Confirm everyone has Node.js installed
- [ ] Circulate and answer quick questions — help teams that are stuck choosing an idea

Slide/screen to show during break:
```
While you take a break:
1. Install Freighter at freighter.app
2. Switch Freighter to Testnet mode (Settings → Network → Testnet)
3. Create or import an account
4. You'll fund it via Friendbot in the build session

Pick what you'll build:
5. Open stellar-300-ideas.md — pick ONE idea
6. Open TRACKS.md — pick the track it fits
7. Scope it down: what is the smallest version you can demo in 90 min?
   That scoped-down version is what you submit at the end.
```

**Facilitator note on idea selection:** Teams that can't decide should default to a Track 1 (Remittance) or Track 2 (Payments) idea — these map most directly onto the cheatsheet scaffold. The cheatsheet builds a wallet + payment flow; most ideas are a variation or extension of that. Steer teams away from anything needing a Soroban contract written from scratch in 90 minutes.

---

## Block 2: Build — 90 Minutes

> Participants follow the `stellar-fullstack-cheatsheet.md` step by step to get a working scaffold, then bend it toward the idea they picked at the break. Mentors circulate and help with blockers. Whatever a team has at 1:30 is what they submit.

---

### Setup (0:00 – 0:10)

**What happens:**
- Everyone runs `npx create-next-app@latest stellar-pay-app` in their terminal
- While it installs, facilitator walks through what they are about to build
- Install `@stellar/stellar-sdk` and `@stellar/freighter-api`
- Create `.env.local` with testnet config from the cheatsheet

**Facilitator script:**
> "You have 90 minutes. By the end of it, you will have a working app on the Stellar testnet — and you will submit it. The cheatsheet gets you a working wallet-and-payment scaffold fast. Once that runs, spend your remaining time bending it toward the idea you picked at the break. Follow the cheatsheet first, then make it yours. If you get stuck, raise your hand — that's what the mentors are here for."

**Remind teams:** the cheatsheet scaffold is the *starting point*, not the deliverable. Their submission is the scaffold shaped toward their chosen idea — even a small twist (a purpose-locked payment, a multi-recipient send, a savings-goal UI) is enough for a strong submission.

**Common early blockers:**
- Node.js not installed → have the install link ready
- `npx` not found → have `npm install -g npx` ready
- Permission errors → have the `--prefix` or `sudo` workarounds ready

---

### Wallet Connection (0:10 – 0:25)

**What participants build:**
- `src/lib/stellar.ts` — network config, server instance, Friendbot helper
- `src/hooks/useWallet.ts` — Freighter connect hook with timeout wrapper
- `src/components/ConnectWallet.tsx` — connect button component

**Key mentor talking points at this stage:**

1. Dynamic import pattern for Freighter — don't let anyone use a static import
2. The timeout wrapper — Freighter hangs indefinitely if the extension is missing
3. Make sure Freighter is set to **Testnet** mode, not Mainnet

**Common blockers at this stage:**
- Freighter extension not installed → direct to freighter.app
- Freighter on wrong network → Settings → Network → Testnet
- `isConnected` returns false → check extension is logged in

**Mini-checkpoint:** Everyone should see the "Connect Freighter" button and be able to click it.

---

### Balance Display (0:25 – 0:35)

**What participants build:**
- `src/lib/balances.ts` — balance fetcher using Horizon
- `src/components/BalanceCard.tsx` — balance display with loading state

**Key mentor talking points:**

1. Horizon is used here for balances (simpler API for classic assets)
2. The balance loop — iterate `account.balances`, match by `asset_type` and `asset_code`
3. Parse + format the balance string (Horizon returns strings, not numbers)

**Mini-checkpoint:** After clicking "Connect" and then "Fund with Friendbot," the XLM balance should appear.

**If Friendbot fails:**
- The account might already be funded (Friendbot errors on duplicate)
- Network issue → try again after 30 seconds

---

### Send Payment (0:35 – 0:55)

**What participants build:**
- `src/lib/payment.ts` — transaction build, sign, submit, poll
- `src/components/SendPayment.tsx` — form with status states

**This is the most complex step. Walk it slowly.**

The five-step payment flow on the board:

```
1. Load account (get sequence number)
2. Build transaction (TransactionBuilder + Operation.payment)
3. Sign with Freighter (user approves in extension popup)
4. Submit to RPC (sendTransaction)
5. Poll for finality (getTransaction every 1s until not NOT_FOUND)
```

**Critical concept to reinforce:**

> "Step 4 gives you a hash and says PENDING. That is NOT success. Step 5 is where you find out if it actually worked. Always poll."

**Common blockers at this stage:**
- Freighter popup doesn't appear → check the account is connected and unlock Freighter
- Transaction fails with `tx_bad_auth` → wrong network passphrase → use `Networks.TESTNET`
- Payment fails with `op_no_destination` → destination doesn't exist → fund it first with Friendbot
- `signedXdr` is an object, not a string → Freighter v6 returns `{ signedTxXdr: string }` → use `.signedTxXdr`

**Mini-checkpoint:** Send 10 XLM to a second test address. See "Payment confirmed!" and the Stellar Expert link.

---

### Wire and Polish (0:55 – 1:10)

**What participants build:**
- Main `page.tsx` — wires all components together
- Optional: `FundAccount.tsx` — Friendbot button

**Key mentor talking point:**
- Show how state flows from the hook through props to child components
- The `balanceKey` pattern for forcing a re-render after payments

**Mini-checkpoint:** Full app flow works end to end in the browser.

---

### Make It Your Idea + Prep Submission (1:10 – 1:30)

This is where the scaffold becomes a submittable project. By now teams should have the wallet + payment flow working — now spend the time making it match the idea picked at the break.

**Shape the scaffold toward the chosen idea.** Most ideas are a small twist on the payment flow. Suggestions by difficulty:

**Easy (10 min each)**
- Add transaction history (Horizon `/accounts/{id}/payments`)
- Add a QR code display for the receive address (`qrcode.react` package)
- Add copy-to-clipboard for the wallet address
- Re-theme the UI and copy around the chosen idea (e.g. "Send money home", "Pay your sari-sari supplier")

**Medium (20–30 min)**
- Add USDC trustline creation
- Show USD equivalent of XLM balance using Reflector oracle price feed
- Multi-recipient payment (split a send across addresses)
- Purpose-locked / memo-tagged payment

**Stretch (if very fast)**
- Swap XLM → USDC via Soroswap
- Add path payment with automatic conversion
- Claimable balance with a time predicate

**Last 5 minutes — prep the submission.** Each team should, before Block 3:
- [ ] Push their code to a public GitHub repo
- [ ] Write a short README (project name, the idea, how Stellar is used, how to run it) — template in `SUBMISSION_GUIDELINES.md`
- [ ] Have their track picked and their core flow working on testnet

Mentors: circulate in the last 5 minutes specifically to check submission-readiness, not code.

---

## Block 3: Demo and Submit — 15 Minutes

### Team Demos (6 min)

Each team: 60 seconds, no slides needed.

Facilitator asks:
> "Show us your core flow working on testnet — and tell us which idea and track you built for."

If a team didn't finish:
> "Show us what you built and what you got stuck on." — they still submit what they have.

No shame in not finishing — the learning is real either way, and a partial submission still counts.

---

### Submit (5 min)

This is the step that closes the workshop. Walk the room through it live.

Facilitator:
> "Open `SUBMISSION_GUIDELINES.md`. Every team: open a GitHub Issue on the workshop repo using the submission template. Paste your repo link, your track, your demo flow. Whatever you built is what you submit — submit it now, before you leave."

- [ ] Each team opens a submission Issue (template in `SUBMISSION_GUIDELINES.md`)
- [ ] Each team has its public repo link, chosen track, and a one-line description ready
- [ ] Mentors confirm every team has submitted before moving on
- [ ] Submissions are scored against `JUDGING.md` — point teams to the rubric so they know what mattered

Teams continuing into the full hackathon: point them to `HACKATHON.md`.

---

### Group Reflect (2 min)

Ask the room:

1. "What surprised you most about building on Stellar?"
2. "What gotcha cost you the most time?"

Write the answers on the board. Useful for future workshops and helps participants crystallize what they learned.

---

### What's Next (2 min)

Brief rapid-fire:

- **StellarX Philippines** — the program you can apply to right now
- **Stellar Development Foundation grants (SCF)** — up to $150,000 for strong projects
- **Stellar Hackathons** — run throughout the year globally
- **Discord** — Stellar developer community
- **Stella AI** — ask Stellar-specific questions at developers.stellar.org

---

## Facilitator Cheatsheet

### Top Gotchas to Proactively Mention

Mention these before participants hit them:

| Timing | Gotcha | What to say |
|---|---|---|
| Before Freighter setup | Extension not set to Testnet | "Switch Freighter to Testnet before connecting — go to Settings → Network" |
| Before writing payment code | sendTransaction ≠ success | "Always poll after submitting. Never assume the tx worked just because submit returned." |
| Before sign step | Freighter v6 returns object | "When you get the result from signTransaction, access `.signedTxXdr` — not the raw result" |
| Before any SSR work | Static import breaks SSR | "Always import Freighter with `await import(...)` — never at the top of the file" |
| Before balance display | USDC needs trustline | "To see a USDC balance, the account needs a trustline for that specific issuer" |

---

### Emergency Fixes

**"Nothing is showing up / blank page"**
- Open browser DevTools → Console
- Most likely: import error or environment variable missing

**"Freighter won't connect"**
- Is extension installed? `freighter.app`
- Is it unlocked? Click the extension icon
- Is it on Testnet? Settings → Network → Testnet

**"Transaction always fails"**
- Log the full error object: `console.log(JSON.stringify(error))`
- Check `tx_bad_auth` → network passphrase wrong
- Check `op_no_destination` → fund destination with Friendbot
- Check `op_low_reserve` → source account needs more XLM

**"Everything installed but TypeScript errors everywhere"**
- Check `tsconfig.json` has `"paths": { "@/*": ["./src/*"] }` if using the `@/` import alias

---

### Room Setup Checklist

**For in-person:**
- [ ] Power strips at every table (laptops need charging)
- [ ] Monitor or second screen for presenter to show Stellar Explorer
- [ ] Whiteboard or flip chart for diagrams
- [ ] WiFi credentials on every table
- [ ] Printed or posted QR code linking to the cheatsheet
- [ ] At least 1 mentor per 8 participants during build block

**For virtual:**
- [ ] Screen share ready with Stellar Laboratory open
- [ ] Breakout rooms configured for small groups
- [ ] Discord or Slack channel for dropping code snippets and links
- [ ] Facilitator can see chat while presenting
- [ ] Recording is on if participants consent

---

## Resource Links (Share at Start)

| Resource | Link |
|---|---|
| Cheatsheet | (this repo's `stellar-fullstack-cheatsheet.md`) |
| Freighter install | `https://freighter.app` |
| Stellar Laboratory | `https://laboratory.stellar.org` |
| Stellar Expert (testnet) | `https://stellar.expert/explorer/testnet` |
| Friendbot | `https://friendbot.stellar.org?addr=YOUR_KEY` |
| Stellar Docs | `https://developers.stellar.org` |
| Soroban RPC (testnet) | `https://soroban-testnet.stellar.org` |
| Stellar Hackathon FAQ | `https://github.com/briwylde08/stellar-hackathon-faq` |
| DeFi Gotchas | `https://github.com/kaankacar/stellar-defi-gotchas` |
| Stellar DeFi App (reference) | `https://github.com/kaankacar/stellar-defi-app` |

---

## Appendix: Adapting This Workshop

### For a 2-hour format
- Cut Module 1.4 (Product Patterns) to 5 min overview only
- Cut the "Make It Your Idea" buffer — teams submit the cheatsheet scaffold themed to their idea
- Shorten demos to 30 seconds per team, but **keep the submission step** — the workshop still ends in a submission

### For a 4-hour format
- Add a 30-min "Soroban basics" module after 1.3
- Add USDC trustline + Soroswap swap as guided exercises in the build block
- Add a full 20-min team demo session with Q&A

### For a beginner audience (no coding experience)
- Skip the build block entirely
- Focus all 90 minutes on Block 1 (extended) + live demos by facilitator
- Have them follow along on Stellar Laboratory instead of building an app

### For an advanced audience (experienced Web3 devs)
- Skip Module 1.1 and 1.2 (basic concepts)
- Go directly to architecture patterns and SDK in Module 1.3 and 1.4
- In the build block, have them extend to Soroswap integration or passkey wallet
- Show the `stellar-dev` Claude Code skill as a power tool

---

*Built for StellarX Philippines. Refs: `stellar-fullstack-cheatsheet.md`, `stellar-300-ideas.md`, `TRACKS.md`, `SUBMISSION_GUIDELINES.md`, `JUDGING.md`, `Starter_prompts.md`, `dev_setup`*
