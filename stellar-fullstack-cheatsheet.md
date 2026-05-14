# Stellar Fullstack App — 90-Minute Cheatsheet

> Build a working Stellar payment app from zero to demo-ready in 90 minutes. Every step is timed. Follow in order. Skip nothing in the first pass.
>
> **You do not need to be an expert.** If you can copy, paste, and run a command, you can finish this. Every step has the exact code you need. If you get stuck, use AI (see below) — that is the intended way to do this.

---

## The Easiest Way to Do This (read first — 2 min)

You have two ways to work through this cheatsheet. Both reach the same finish line.

### Path A — Copy-paste (no AI needed)
Every step below gives you the **exact code and exact commands**. Create the file at the path shown, paste the code, move on. That is the whole job. You never have to write code from scratch.

### Path B — AI-assisted (recommended if anything breaks)
Use an AI coding assistant to do the typing and fix errors for you. This is the **intended** way — it is faster and you will get unstuck quickly.

**How to use AI with this cheatsheet:**
1. Open an AI coding tool — **Claude Code**, **Cursor**, or a free option.
2. Tell it: *"I'm following a 90-minute cheatsheet to build a Stellar payment app. Here is Step 1: [paste the step]. Do this step for me."*
3. When something breaks, paste the **exact error message** and ask: *"Fix this. I'm on Stellar testnet using @stellar/stellar-sdk v14."*
4. Use the ready-made prompt at the bottom of this file ("Starter AI Prompt for Claude Code") to kick things off.

**No paid AI subscription?** This repo has you covered:
- `Free_AI_Setup.md` — free and low-cost ways to get a strong coding model (OpenRouter, Groq, Google AI Studio, Ollama for local models, cheap GPU rental).
- `Recommended_AI_Tools.md` — which AI tool to pick and why.

### This repo has more help — use it
| If you want... | Open this file |
|---|---|
| The right way to prompt AI for a Stellar build | `Starter_prompts.md` |
| A free AI setup (no paid subscription) | `Free_AI_Setup.md` |
| Which AI tools to use | `Recommended_AI_Tools.md` |
| Plan mode, parallel agents, browser testing | `Claude_code_Guide.md` |
| Testnet addresses, gotchas, deeper setup | `dev_setup` |
| Ecosystem links and reference projects | `Resources` |
| 300 ideas for what to actually build | `stellar-300-ideas.md` |

**Tip:** Ask the AI to read `dev_setup` and `Starter_prompts.md` from this repo first — it will then know all the Stellar gotchas before it writes a line.

---

## Before You Start (5 min)

### What you will build
A fullstack Stellar **payment app** that:
- Creates or connects a wallet
- Shows XLM and USDC balance
- Sends a payment to any Stellar address
- Polls for transaction finality and shows the result

### What you need
- Node.js 18+ installed
- `npm` or `pnpm` available
- A browser (Chrome preferred)
- A code editor
- Internet connection

### Choose your wallet pattern before writing a single line

| Pattern | When to use |
|---|---|
| **Self-custodial** (app manages keys) | Building a wallet product; no browser extension needed |
| **Freighter connect** (user brings wallet) | Building a payment app; user owns keys in Freighter extension |

This cheatsheet uses the **Freighter connect** pattern — fastest for a demo.  
If you want self-custodial, see `Starter_prompts.md` for that exact prompt.

---

## Step 1 — Environment Setup (10 min)

### 1.1 Create the project

```bash
npx create-next-app@latest stellar-pay-app --typescript --tailwind --eslint --app --src-dir --no-turbopack
cd stellar-pay-app
```

### 1.2 Install Stellar SDK and Freighter API

```bash
npm install @stellar/stellar-sdk @stellar/freighter-api
```

### 1.3 Create your environment config file

Create `.env.local` at the project root:

```env
NEXT_PUBLIC_NETWORK=testnet
NEXT_PUBLIC_SOROBAN_RPC=https://soroban-testnet.stellar.org
NEXT_PUBLIC_NETWORK_PASSPHRASE=Test SDF Network ; September 2015
NEXT_PUBLIC_USDC_ISSUER=GBBD47IF6LWK7P7MDEVSCWR7DPUWV3NY3DTQEVFL4NAT4AQH3ZLLFLA5
```

### 1.4 Create a Stellar config module

Create `src/lib/stellar.ts`:

```typescript
import { rpc, Networks, Asset, Keypair, TransactionBuilder, Operation, BASE_FEE } from '@stellar/stellar-sdk';

export const NETWORK_PASSPHRASE = Networks.TESTNET;
export const RPC_URL = process.env.NEXT_PUBLIC_SOROBAN_RPC!;
export const server = new rpc.Server(RPC_URL);

export const USDC = new Asset(
  'USDC',
  process.env.NEXT_PUBLIC_USDC_ISSUER!
);

export const XLM = Asset.native();

export async function fundTestnetAccount(publicKey: string): Promise<void> {
  const res = await fetch(`https://friendbot.stellar.org?addr=${publicKey}`);
  if (!res.ok) throw new Error('Friendbot funding failed');
}

export async function getAccount(publicKey: string) {
  return server.getAccount(publicKey);
}
```

**Gotcha:** Do not use `SorobanRpc` — that is the old namespace. v14 uses `rpc`.

---

## Step 2 — Wallet Connection (15 min)

### 2.1 Create the wallet hook

Create `src/hooks/useWallet.ts`:

```typescript
'use client';
import { useState, useCallback } from 'react';

export function useWallet() {
  const [publicKey, setPublicKey] = useState<string | null>(null);
  const [connecting, setConnecting] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const connect = useCallback(async () => {
    setConnecting(true);
    setError(null);
    try {
      // Dynamic import prevents SSR issues
      const { isConnected, getAddress } = await import('@stellar/freighter-api');

      // Timeout wrapper in case extension is missing
      const connected = await Promise.race([
        isConnected(),
        new Promise<{ isConnected: boolean }>(resolve =>
          setTimeout(() => resolve({ isConnected: false }), 3000)
        )
      ]);

      if (!connected.isConnected) {
        throw new Error('Freighter not installed. Install the browser extension first.');
      }

      const addressResult = await getAddress();
      if (addressResult.error) throw new Error(addressResult.error);
      setPublicKey(addressResult.address);
    } catch (e: any) {
      setError(e.message);
    } finally {
      setConnecting(false);
    }
  }, []);

  const disconnect = useCallback(() => setPublicKey(null), []);

  return { publicKey, connecting, error, connect, disconnect };
}
```

**Gotcha:** Always use dynamic imports for `@stellar/freighter-api` in Next.js. Static imports break SSR.

### 2.2 Create wallet connect component

Create `src/components/ConnectWallet.tsx`:

```tsx
'use client';
import { useWallet } from '@/hooks/useWallet';

export default function ConnectWallet() {
  const { publicKey, connecting, error, connect, disconnect } = useWallet();

  if (publicKey) {
    return (
      <div className="flex items-center gap-3">
        <span className="text-sm font-mono bg-gray-100 px-3 py-1 rounded">
          {publicKey.slice(0, 6)}...{publicKey.slice(-6)}
        </span>
        <button
          onClick={disconnect}
          className="text-sm text-red-500 hover:underline"
        >
          Disconnect
        </button>
      </div>
    );
  }

  return (
    <div>
      <button
        onClick={connect}
        disabled={connecting}
        className="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 disabled:opacity-50"
      >
        {connecting ? 'Connecting...' : 'Connect Freighter'}
      </button>
      {error && <p className="text-red-500 text-sm mt-2">{error}</p>}
    </div>
  );
}
```

---

## Step 3 — Balance Display (10 min)

### 3.1 Create balance fetcher

Create `src/lib/balances.ts`:

```typescript
import { Horizon } from '@stellar/stellar-sdk';

const horizon = new Horizon.Server('https://horizon-testnet.stellar.org');

export interface Balances {
  xlm: string;
  usdc: string;
}

export async function fetchBalances(publicKey: string): Promise<Balances> {
  const account = await horizon.loadAccount(publicKey);
  let xlm = '0';
  let usdc = '0';

  for (const balance of account.balances) {
    if (balance.asset_type === 'native') {
      xlm = parseFloat(balance.balance).toFixed(2);
    }
    if (
      balance.asset_type === 'credit_alphanum4' &&
      balance.asset_code === 'USDC'
    ) {
      usdc = parseFloat(balance.balance).toFixed(2);
    }
  }
  return { xlm, usdc };
}
```

### 3.2 Create balance display component

Create `src/components/BalanceCard.tsx`:

```tsx
'use client';
import { useState, useEffect } from 'react';
import { fetchBalances, type Balances } from '@/lib/balances';

export default function BalanceCard({ publicKey }: { publicKey: string }) {
  const [balances, setBalances] = useState<Balances | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchBalances(publicKey)
      .then(setBalances)
      .catch(console.error)
      .finally(() => setLoading(false));
  }, [publicKey]);

  if (loading) return <p className="text-gray-400">Loading balances...</p>;
  if (!balances) return <p className="text-red-400">Failed to load balances</p>;

  return (
    <div className="grid grid-cols-2 gap-4 mt-4">
      <div className="bg-gray-50 border rounded p-4">
        <p className="text-xs text-gray-500 uppercase">XLM</p>
        <p className="text-2xl font-bold">{balances.xlm}</p>
      </div>
      <div className="bg-gray-50 border rounded p-4">
        <p className="text-xs text-gray-500 uppercase">USDC</p>
        <p className="text-2xl font-bold">{balances.usdc}</p>
      </div>
    </div>
  );
}
```

---

## Step 4 — Send Payment (20 min)

### 4.1 Create the transaction builder

Create `src/lib/payment.ts`:

```typescript
import {
  TransactionBuilder,
  Operation,
  Asset,
  BASE_FEE,
  Memo,
} from '@stellar/stellar-sdk';
import { server, NETWORK_PASSPHRASE } from './stellar';

export async function buildPaymentTransaction(
  senderPublicKey: string,
  destinationPublicKey: string,
  amount: string,
  assetCode: string,
  assetIssuer?: string
): Promise<string> {
  const asset = assetCode === 'XLM'
    ? Asset.native()
    : new Asset(assetCode, assetIssuer!);

  // Always load account fresh for current sequence number
  const account = await server.getAccount(senderPublicKey);

  const tx = new TransactionBuilder(account, {
    fee: BASE_FEE,
    networkPassphrase: NETWORK_PASSPHRASE,
  })
    .addOperation(
      Operation.payment({
        destination: destinationPublicKey,
        asset,
        amount,
      })
    )
    .setTimeout(30)
    .build();

  return tx.toXDR();
}

export async function submitSignedTransaction(signedXdr: string): Promise<string> {
  const { TransactionBuilder } = await import('@stellar/stellar-sdk');
  const tx = TransactionBuilder.fromXDR(signedXdr, NETWORK_PASSPHRASE);
  const result = await server.sendTransaction(tx);

  if (result.status === 'ERROR') {
    throw new Error(`Transaction error: ${JSON.stringify(result.errorResult)}`);
  }

  return result.hash;
}

export async function pollTransaction(hash: string): Promise<string> {
  for (let i = 0; i < 60; i++) {
    await new Promise(r => setTimeout(r, 1000));
    const result = await server.getTransaction(hash);
    if (result.status !== 'NOT_FOUND') {
      if (result.status === 'SUCCESS') return 'SUCCESS';
      throw new Error(`Transaction failed: ${result.status}`);
    }
  }
  throw new Error('Transaction timed out after 60 seconds');
}
```

**Gotcha:** `sendTransaction` returning `PENDING` is NOT success. You must poll until `SUCCESS`.

### 4.2 Create the send payment form

Create `src/components/SendPayment.tsx`:

```tsx
'use client';
import { useState } from 'react';
import { buildPaymentTransaction, submitSignedTransaction, pollTransaction } from '@/lib/payment';

type Status = 'idle' | 'building' | 'signing' | 'submitting' | 'polling' | 'success' | 'error';

export default function SendPayment({ publicKey }: { publicKey: string }) {
  const [destination, setDestination] = useState('');
  const [amount, setAmount] = useState('');
  const [asset, setAsset] = useState('XLM');
  const [status, setStatus] = useState<Status>('idle');
  const [txHash, setTxHash] = useState('');
  const [errorMsg, setErrorMsg] = useState('');

  const handleSend = async () => {
    setStatus('building');
    setErrorMsg('');
    try {
      const xdr = await buildPaymentTransaction(
        publicKey,
        destination,
        amount,
        asset,
        asset === 'USDC' ? process.env.NEXT_PUBLIC_USDC_ISSUER : undefined
      );

      setStatus('signing');
      const { signTransaction } = await import('@stellar/freighter-api');
      const result = await signTransaction(xdr, {
        networkPassphrase: process.env.NEXT_PUBLIC_NETWORK_PASSPHRASE,
      });

      // Freighter v6 returns an object, not a string
      const signedXdr = typeof result === 'string' ? result : (result as any).signedTxXdr;

      setStatus('submitting');
      const hash = await submitSignedTransaction(signedXdr);
      setTxHash(hash);

      setStatus('polling');
      await pollTransaction(hash);
      setStatus('success');
    } catch (e: any) {
      setErrorMsg(e.message);
      setStatus('error');
    }
  };

  const statusMessages: Record<Status, string> = {
    idle: '',
    building: 'Building transaction...',
    signing: 'Waiting for Freighter signature...',
    submitting: 'Submitting to network...',
    polling: 'Waiting for confirmation...',
    success: 'Payment successful!',
    error: '',
  };

  const isLoading = ['building', 'signing', 'submitting', 'polling'].includes(status);

  return (
    <div className="mt-6 border rounded p-6 bg-white">
      <h2 className="text-lg font-semibold mb-4">Send Payment</h2>

      <div className="space-y-4">
        <div>
          <label className="block text-sm text-gray-600 mb-1">Asset</label>
          <select
            value={asset}
            onChange={e => setAsset(e.target.value)}
            className="border rounded px-3 py-2 w-full"
          >
            <option value="XLM">XLM</option>
            <option value="USDC">USDC</option>
          </select>
        </div>

        <div>
          <label className="block text-sm text-gray-600 mb-1">Destination Address</label>
          <input
            type="text"
            placeholder="G..."
            value={destination}
            onChange={e => setDestination(e.target.value)}
            className="border rounded px-3 py-2 w-full font-mono text-sm"
          />
        </div>

        <div>
          <label className="block text-sm text-gray-600 mb-1">Amount</label>
          <input
            type="number"
            placeholder="0.00"
            value={amount}
            onChange={e => setAmount(e.target.value)}
            className="border rounded px-3 py-2 w-full"
          />
        </div>

        <button
          onClick={handleSend}
          disabled={isLoading || !destination || !amount}
          className="w-full bg-green-600 text-white py-3 rounded hover:bg-green-700 disabled:opacity-50"
        >
          {isLoading ? statusMessages[status] : 'Send'}
        </button>
      </div>

      {status === 'success' && (
        <div className="mt-4 p-3 bg-green-50 border border-green-200 rounded">
          <p className="text-green-700 font-medium">Payment confirmed!</p>
          <a
            href={`https://stellar.expert/explorer/testnet/tx/${txHash}`}
            target="_blank"
            rel="noopener noreferrer"
            className="text-blue-600 text-sm hover:underline break-all"
          >
            View on Stellar Expert →
          </a>
        </div>
      )}

      {status === 'error' && (
        <div className="mt-4 p-3 bg-red-50 border border-red-200 rounded">
          <p className="text-red-700 text-sm">{errorMsg}</p>
        </div>
      )}
    </div>
  );
}
```

---

## Step 5 — Wire It All Together (10 min)

### 5.1 Create the testnet funding utility component

Create `src/components/FundAccount.tsx`:

```tsx
'use client';
import { useState } from 'react';
import { fundTestnetAccount } from '@/lib/stellar';

export default function FundAccount({ publicKey }: { publicKey: string }) {
  const [loading, setLoading] = useState(false);
  const [done, setDone] = useState(false);
  const [error, setError] = useState('');

  const fund = async () => {
    setLoading(true);
    try {
      await fundTestnetAccount(publicKey);
      setDone(true);
    } catch (e: any) {
      setError(e.message);
    } finally {
      setLoading(false);
    }
  };

  if (done) return <p className="text-green-600 text-sm">Account funded with 10,000 XLM testnet</p>;

  return (
    <div>
      <button
        onClick={fund}
        disabled={loading}
        className="text-sm bg-yellow-400 px-3 py-1 rounded hover:bg-yellow-500 disabled:opacity-50"
      >
        {loading ? 'Funding...' : 'Fund with Friendbot (testnet)'}
      </button>
      {error && <p className="text-red-500 text-sm mt-1">{error}</p>}
    </div>
  );
}
```

### 5.2 Replace your main page

Replace `src/app/page.tsx`:

```tsx
'use client';
import { useState, useCallback } from 'react';
import ConnectWallet from '@/components/ConnectWallet';
import BalanceCard from '@/components/BalanceCard';
import SendPayment from '@/components/SendPayment';
import FundAccount from '@/components/FundAccount';
import { useWallet } from '@/hooks/useWallet';

function AppContent() {
  const { publicKey, connecting, error, connect, disconnect } = useWallet();
  const [balanceKey, setBalanceKey] = useState(0);

  const refreshBalances = useCallback(() => setBalanceKey(k => k + 1), []);

  return (
    <main className="min-h-screen bg-gray-50">
      <div className="max-w-lg mx-auto px-4 py-12">
        <div className="flex justify-between items-center mb-8">
          <h1 className="text-2xl font-bold">Stellar Pay</h1>
          <ConnectWallet />
        </div>

        {!publicKey && !connecting && (
          <div className="text-center py-16 text-gray-500">
            <p className="mb-4">Connect your Freighter wallet to get started.</p>
            <p className="text-sm">
              Don't have Freighter?{' '}
              <a
                href="https://freighter.app"
                target="_blank"
                rel="noopener noreferrer"
                className="text-blue-600 hover:underline"
              >
                Install it here
              </a>
            </p>
          </div>
        )}

        {publicKey && (
          <>
            <div className="mb-4">
              <FundAccount publicKey={publicKey} />
            </div>

            <BalanceCard key={balanceKey} publicKey={publicKey} />

            <SendPayment publicKey={publicKey} />

            <button
              onClick={refreshBalances}
              className="mt-4 text-sm text-gray-500 hover:text-gray-700 underline"
            >
              Refresh balances
            </button>
          </>
        )}
      </div>
    </main>
  );
}

export default function Home() {
  return <AppContent />;
}
```

---

## Step 6 — Run and Test (10 min)

### 6.1 Start dev server

```bash
npm run dev
```

Open `http://localhost:3000`

### 6.2 Test checklist (do in order)

- [ ] Install Freighter extension from freighter.app if needed
- [ ] Create a testnet account in Freighter (or use existing)
- [ ] Click "Connect Freighter" — approve in extension
- [ ] Click "Fund with Friendbot" — wait for confirmation
- [ ] Verify XLM balance appears (should show ~10,000 XLM)
- [ ] Create a second testnet account to send to (use Stellar Laboratory)
- [ ] Send 10 XLM to that second account
- [ ] Approve in Freighter popup
- [ ] Watch status: Building → Signing → Submitting → Polling → Success
- [ ] Click the Stellar Expert link and verify the transaction onchain

### 6.3 Get a second test address

Go to `https://laboratory.stellar.org/#account-creator?network=test`  
Create a second account there. Use that address as your payment destination.

---

## Step 7 — Polish for Demo (10 min)

### Quick polish items

**Add a transaction history section**

After a successful payment, save the hash to local state and show a table:

```tsx
const [history, setHistory] = useState<Array<{hash: string; amount: string; dest: string}>>([]);
// After pollTransaction succeeds:
setHistory(prev => [...prev, { hash: txHash, amount, dest: destination }]);
```

**Add loading skeleton for balances**

```tsx
if (loading) return (
  <div className="grid grid-cols-2 gap-4 mt-4 animate-pulse">
    <div className="h-16 bg-gray-200 rounded" />
    <div className="h-16 bg-gray-200 rounded" />
  </div>
);
```

**Add a copy-address button**

```tsx
<button onClick={() => navigator.clipboard.writeText(publicKey)}>
  Copy Address
</button>
```

---

## Critical Gotchas Reference Card

Paste this in your `CLAUDE.md`:

```markdown
## Stellar Gotchas

1. Always use `rpc` namespace, NOT `SorobanRpc` (v14 SDK)
2. Always simulate Soroban txs before sending
3. `sendTransaction` → PENDING is NOT success — always poll
4. Poll pattern: getTransaction every 1s for up to 60s
5. Network passphrase: use `Networks.TESTNET` — never hardcode string
6. Freighter: dynamic import only (SSR crashes on static import)
7. Freighter v6: signTransaction returns object → use `.signedTxXdr`
8. Add 2000ms timeout wrapper for all Freighter API calls
9. Trustlines required before receiving any non-XLM asset
10. USDC issuer differs per protocol on testnet — check which one
11. Soroswap amounts must be BigInt
12. Use Soroban RPC for contract calls, Horizon for balance/history
```

---

## Full Dependency Reference

```json
{
  "dependencies": {
    "@stellar/stellar-sdk": "^14.x",
    "@stellar/freighter-api": "^4.x",
    "next": "^15.x",
    "react": "^19.x",
    "tailwindcss": "^3.x"
  }
}
```

---

## Testnet Reference Card

| Resource | Value |
|---|---|
| Soroban RPC | `https://soroban-testnet.stellar.org` |
| Horizon (testnet) | `https://horizon-testnet.stellar.org` |
| Friendbot | `https://friendbot.stellar.org?addr=YOUR_KEY` |
| Network passphrase | `Test SDF Network ; September 2015` |
| USDC issuer (Soroswap/SDEX) | `GBBD47IF6LWK7P7MDEVSCWR7DPUWV3NY3DTQEVFL4NAT4AQH3ZLLFLA5` |
| USDC SAC contract | `CBIELTK6YBZJU5UP2WWQEUCYKLPU6AUNZ2BQ4WWFEIE3USCIHMXQDAMA` |
| XLM SAC contract | `CDLZFC3SYJYDZT7K67VZ75HPJVIEUVNIXF47ZG2FB2RMQQVU2HHGCYSC` |
| Explorer | `https://stellar.expert/explorer/testnet` |
| Laboratory | `https://laboratory.stellar.org` |

---

## Common Errors and Fixes

| Error | Cause | Fix |
|---|---|---|
| `tx_bad_auth` | Wrong network passphrase | Use `Networks.TESTNET` not a hardcoded string |
| `op_no_destination` | Destination account doesn't exist | Fund destination first with Friendbot |
| Freighter call hangs | Extension missing | Add 2000ms timeout wrapper |
| Balance shows 0 after funding | Need to refresh | Add manual refresh or re-fetch after Friendbot |
| `result.signedTxXdr` is undefined | Old Freighter API pattern | Freighter v6 returns object, access `.signedTxXdr` |
| Transaction stays NOT_FOUND | Network congestion | Poll for 60s minimum, not 10s |
| Import error on SSR | Static freighter import | Use `await import('@stellar/freighter-api')` |
| USDC balance not showing | Missing trustline | Create trustline for USDC asset first |

---

## Starter AI Prompt for Claude Code

Paste this to start building with AI assistance:

```text
Use plan mode first.

I am building a Stellar payment app with the following spec:
- Framework: Next.js 15, TypeScript, Tailwind CSS
- Stellar SDK: @stellar/stellar-sdk v14. Use the `rpc` namespace, NOT `SorobanRpc`
- Network: testnet
- RPC URL: https://soroban-testnet.stellar.org
- Wallet pattern: Freighter wallet connection (NOT self-custodial)
- Product type: payment app (not a wallet infrastructure product)
- Primary user flow: connect Freighter → view XLM and USDC balances → send payment to another address → confirm onchain result

Critical constraints:
- Always use dynamic imports for @stellar/freighter-api (SSR)
- Freighter v6: signTransaction returns object, use .signedTxXdr
- Always poll for transaction finality (poll getTransaction for 60s)
- sendTransaction result is NOT transaction success — always poll
- Use Networks.TESTNET for network passphrase

Build the smallest strong demo-ready version. Core flow only. No dashboard extras until the core works.
```

---

## 90-Minute Time Budget

| Step | Task | Time |
|---|---|---|
| 0 | Read "The Easiest Way" + this cheatsheet, choose wallet pattern, open your AI tool | 5 min |
| 1 | Environment setup, SDK install, config | 10 min |
| 2 | Wallet connection (Freighter hook + component) | 15 min |
| 3 | Balance display | 10 min |
| 4 | Send payment (build → sign → submit → poll) | 20 min |
| 5 | Wire everything into main page | 10 min |
| 6 | Run, test, fix issues | 10 min |
| 7 | Polish: empty states, error messages, tx history | 10 min |
| **Total** | | **90 min** |

> **Falling behind?** That is normal. Hand the step you are on to your AI tool with the instruction *"do this step for me"* and let it catch you up. The goal is a working demo of the core flow — not typing every character yourself.

---

## What to Build Next (Beyond 90 Minutes)

Once the core payment flow works, extend it with:

| Feature | Complexity | Stellar concept practiced |
|---|---|---|
| Transaction history from Horizon | Low | Horizon operations API |
| USDC trustline creation | Low | Classic asset trustlines |
| QR code for receiving address | Low | Frontend only |
| Swap XLM → USDC via Soroswap | Medium | Soroban contract calls |
| PHP/USD price feed via Reflector | Medium | Oracle reads |
| Lending via Blend | High | Soroban DeFi composability |
| Passkey wallet (no Freighter) | High | WebAuthn + Soroban auth |
| Self-custodial wallet | High | Mnemonic, key derivation, local signing |

---

*Built for StellarX Philippines. Reference: `Starter_prompts.md`, `dev_setup`, `Resources`, `Claude_code_Guide.md`*
