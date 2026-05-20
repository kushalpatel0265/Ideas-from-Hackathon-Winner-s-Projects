# Midstream Project Learning Notes

## Project Repository

Original GitHub Repository: https://github.com/chainaim3003/midstream

## Project Overview

**Midstream** is a hackathon-winning AI project built around a simple but powerful idea:

> Pay for useful AI output, not just tokens.

Most AI tools charge users for the full request, even if the final answer is incorrect, incomplete, or low quality. Midstream changes this by breaking AI generation into small chunks. After every chunk, the system checks the quality of the output. If the quality drops below a set threshold, the buyer stops paying and the AI generation stops.

This creates a smarter payment system where users only continue paying while the output remains useful.

## Why This Project Is Interesting

This project combines three important areas:

1. **AI inference**
2. **Quality checking**
3. **Blockchain-based nanopayments**

Instead of treating AI generation as one full request, Midstream treats it as a sequence of small paid actions.

Each chunk of output is paid for separately using Circle Gateway Nanopayments on Arc Testnet. After every chunk, a quality checker decides whether the output is still good enough to continue.

## Simple Explanation

The project works like this:

```text
User sends a prompt
        ↓
Seller generates a small chunk of AI output
        ↓
Buyer pays a tiny amount for that chunk
        ↓
Quality checker reviews the output
        ↓
If quality is good → continue
If quality is bad → stop generation
```

In simple words:

```text
Pay a little → receive a little → check quality → continue or stop
```

## Main Problem Solved

Traditional AI pricing has a problem:

```text
You pay even if the answer is bad.
```

Midstream tries to solve this by allowing the buyer to stop paying as soon as the AI output becomes low quality.

This does not refund already delivered chunks. Instead, it prevents wasting more money on future bad output.

## Main Features

- Pay-per-chunk AI generation
- Quality-based stopping system
- Buyer-controlled payment flow
- Stateless seller API
- Anthropic Claude for text/code generation
- Gemini-based text quality checking
- TypeScript compiler and Node test runner for code quality checking
- Circle Gateway x402 nanopayments
- Arc Testnet blockchain settlement
- Demo scripts for wallet generation, deposits, and transaction verification

## Technology Stack

| Area | Technology |
|---|---|
| Language | TypeScript |
| Runtime | Node.js |
| Backend | Express.js |
| AI Generation | Anthropic Claude |
| Text Quality Judge | Gemini 2.5 Flash |
| Code Quality Judge | TypeScript compiler + Node test runner |
| Payments | Circle Gateway Nanopayments |
| Protocol | x402 |
| Blockchain | Arc Testnet |
| Wallet / Chain Tools | Viem |
| Deployment | Railway |

## Important Project Files

| File / Folder | Purpose |
|---|---|
| `server/seller.ts` | Main Express server that hosts paid AI chunk routes |
| `server/routes/text-chunk.ts` | Generates one paid text chunk using Anthropic |
| `server/routes/code-chunk.ts` | Generates one paid code chunk using Anthropic |
| `client/buyer.ts` | Main buyer logic: pay, receive chunk, check quality, continue or stop |
| `client/kill-gate.ts` | Decides whether to stop generation based on quality scores |
| `client/quality/text-monitor.ts` | Uses Gemini to judge text quality |
| `client/quality/code-monitor.ts` | Uses TypeScript compiler and tests to judge code quality |
| `shared/config.ts` | Loads and validates environment variables |
| `scripts/` | Utility scripts for wallet creation, deposits, demo runs, and verification |
| `docs/` | Design notes, quality checker explanation, pitch framing, and analysis |

## How the System Works

### 1. Seller Side

The seller runs an Express server with paid routes such as:

```text
POST /chunk/text
POST /chunk/code
```

Each route requires payment before generating output. The Circle Gateway middleware handles the payment verification.

The seller does not store the full session state. Instead, the buyer sends the previous output back with every request.

This makes the seller simpler and easier to scale.

### 2. Buyer Side

The buyer controls the session.

For every chunk, the buyer:

1. Checks the remaining budget
2. Pays for the next chunk
3. Receives generated output
4. Adds the new chunk to the full answer
5. Runs a quality monitor
6. Uses the kill gate to decide whether to continue

This makes the buyer responsible for deciding whether the AI output is still worth paying for.

### 3. Quality Gate

The kill gate decides when to stop the session.

It uses three rules:

| Rule | Meaning |
|---|---|
| Warmup period | Do not stop too early because initial chunks may not have enough context |
| Rolling average | Stop if recent quality scores stay below the threshold |
| Catastrophic failure | Stop immediately if one chunk is extremely bad |

This prevents the buyer from continuing to pay for poor-quality output.

### 4. Text Quality Checking

For text output, the project uses Gemini as a judge.

The quality checker looks for:

- Topic relevance
- Prompt adherence
- Drift from the original task
- Whether exact required identifiers are preserved

It also uses deterministic checks for backtick keywords. For example, if the prompt requires `signTypedData`, the output should not replace it with something incorrect like `signMessage`.

### 5. Code Quality Checking

For code output, the project uses a more deterministic approach.

The system checks:

1. Whether the code appears incomplete
2. Whether it compiles using `tsc --strict`
3. Whether tests pass using `node --test`

This is a strong design because the compiler becomes the judge instead of relying only on another AI model.

## Key Engineering Lessons

### 1. Keep the Server Stateless

The seller does not need to remember the full conversation. The buyer sends the cumulative output back each time.

This makes the backend easier to scale and debug.

### 2. Separate Payment From Quality Logic

The payment system and quality checker are separate. This keeps the project modular.

The seller handles generation and payment verification.

The buyer handles quality checking and continuation decisions.

### 3. Use Deterministic Checks When Possible

For code generation, the project uses real tools like:

```bash
tsc --strict
node --test
```

This is better than only asking another AI model if the code is correct.

## Takeaways

The biggest takeaway from this project is that AI products can be designed around **trust and quality**, not just generation speed.

Midstream shows that AI output can be treated as a measurable service where the buyer can stop paying when the result is no longer useful.

This idea can be extended to many future AI products, such as:

- AI coding assistants
- Research agents
- Image generation systems
- AI workflow automation
- Agent marketplaces
- Pay-per-task AI services

## Credit

This README is based on my personal review and learning from the original Midstream project.

Original project by: chainaim3003  
Original repository: https://github.com/chainaim3003/midstream
