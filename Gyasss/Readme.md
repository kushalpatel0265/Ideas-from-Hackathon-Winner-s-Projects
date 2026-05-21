# Gas Oracle Project Learning Notes

## Project Repository

Original GitHub Repository: https://github.com/lsarta/gas-oracle

## Project Overview

**Gas Oracle** is a hackathon-winning project that builds a transaction-verified gas price oracle.

The product appears under the name **Gyasss** in the application UI. The main idea is simple:

> Users report real gas prices and earn USDC rewards. The system uses those reports to keep gas station prices fresh and recommend cheaper fill-up opportunities.

Instead of relying only on stale gas price data, this project creates a user-powered oracle where people are financially rewarded for confirming real-world fuel prices.

## Simple Explanation

The project works like this:

```text
User signs in
        ↓
User reports a gas station price
        ↓
System calculates price per gallon
        ↓
Oracle checks freshness and consensus
        ↓
User earns USDC reward
        ↓
App updates gas station price data
        ↓
Other users get better gas-saving recommendations
```

In simple words:

```text
Report gas price → verify data → earn USDC → help others save money
```

## Main Problem Solved

Gas prices change frequently, but many apps show outdated or unreliable prices.

This project tries to solve that by creating an incentive system:

```text
Fresh gas price data becomes valuable.
Users get paid to provide it.
Drivers use the data to save money.
```

## Why This Project Is Interesting

This project is interesting because it combines:

1. Real-world data collection
2. User incentives
3. Blockchain payments
4. Location-based recommendations
5. Oracle-style consensus logic

It is not just a normal gas price app. It is a system where users help maintain accurate data and receive USDC rewards for doing so.

## Main Features

- User login with wallet support
- Gas price reporting
- USDC payout rewards
- Freshness-based reward calculation
- Consensus-based gas price updates
- Outlier detection for suspicious reports
- Gas station recommendation engine
- Detour cost calculation
- Map-based route preview
- User earnings and activity tracking
- Database-backed station, report, user, and oracle query records

## Technology Stack

| Area | Technology |
|---|---|
| Frontend | Next.js |
| Language | TypeScript |
| UI | React, Tailwind CSS, Framer Motion |
| Authentication | Privy |
| Database | Vercel Postgres / PostgreSQL |
| Payments | Circle / USDC |
| Blockchain Tools | Viem, x402 |
| Maps / Routing | Mapbox |
| Validation | Zod |
| Styling | Tailwind CSS, shadcn-style components |
| Deployment Target | Vercel-style Next.js deployment |

## Important Project Files

| File / Folder | Purpose |
|---|---|
| `src/app/page.tsx` | Main landing page and signed-in dashboard entry |
| `src/components/OpportunityCard.tsx` | Shows gas-saving opportunities to users |
| `src/app/api/opportunity/route.ts` | API route that finds the best gas station recommendation |
| `src/app/api/reports/route.ts` | API route for submitting gas price reports and triggering payouts |
| `src/lib/oracle/recommend.ts` | Core recommendation engine for finding worthwhile gas station detours |
| `src/lib/oracle/pricing.ts` | Calculates implied price and freshness-based payout |
| `src/lib/oracle/consensus.ts` | Computes consensus price and detects outlier reports |
| `src/lib/db/schema.sql` | Database schema for users, stations, reports, and oracle queries |
| `scripts/db-migrate.ts` | Applies database schema and migrations |
| `scripts/db-seed.ts` | Seeds initial database data |
| `scripts/test-recommend.ts` | Tests recommendation logic |
| `scripts/test-consensus.ts` | Tests oracle consensus logic |
| `scripts/test-stakes.ts` | Tests staking-related logic |

## How the System Works

### 1. User Signs In

Users sign in using the app. The project uses wallet-based identity so users can receive USDC rewards.

The homepage explains the product as:

```text
Get paid to confirm gas prices.
```

The UI also communicates that fresher data pays more.

### 2. User Reports Gas Price

A user submits:

```text
Gas station
Transaction amount
Gallons pumped
Wallet address
```

The system calculates the implied price:

```text
price per gallon = transaction amount / gallons
```

Example:

```text
$45 transaction / 10 gallons = $4.50 per gallon
```

### 3. System Validates the Report

The project checks whether the submitted data makes sense.

For example:

- Gallons must be greater than 0
- Gallons cannot be unrealistically high
- Implied gas price must be within a reasonable range
- Wallet address must be valid
- Station must exist in the database

This prevents obviously bad data from entering the system.

### 4. Freshness-Based Payout

The older the current gas price data is, the more valuable a new report becomes.

Example payout logic:

| Data Age | Payout |
|---|---|
| Very fresh | Small payout |
| 15+ minutes old | Higher payout |
| 1+ hour old | Higher payout |
| 6+ hours old | Higher payout |
| 12+ hours old | Higher payout |
| 24+ hours old | Highest payout |

This is smart because it rewards users more when the oracle needs fresh data the most.

### 5. Consensus System

The project does not blindly trust one report.

It looks at recent reports for the same station and calculates a consensus price.

The consensus system considers:

- Recent reports
- Weighted average price
- Number of reports
- Report age
- Price spread
- Confidence level

The confidence level can be:

```text
low
medium
high
```

### 6. Outlier Detection

If a new report is very different from a high-confidence consensus, it can be treated as an outlier.

This helps protect the oracle from fake or incorrect gas price submissions.

For example:

```text
Consensus price: $4.50/gal
New report: $7.00/gal
Difference is too large → possible outlier
```

Outlier reports may receive reduced rewards.

### 7. Station Price Update

After a valid report is submitted, the station price is updated using the latest consensus result.

This keeps the gas station database fresh and useful for other users.

### 8. USDC Payout

After the report is saved, the app sends USDC to the user’s wallet.

The user earns money for helping update the gas price oracle.

### 9. Gas-Saving Recommendation

The app also recommends gas stations where the user can save money.

The recommendation engine considers:

- User home location
- Optional work location
- Nearby stations
- Current station gas prices
- Baseline gas price
- Detour distance
- Detour time
- User’s hourly value of time
- Vehicle MPG
- Typical fill-up gallons

It calculates:

```text
raw savings
- detour time cost
- detour gas cost
= net savings
```

Only if the net savings is positive does the app recommend the detour.

## Example Recommendation Logic

```text
Cheaper station saves: $6.00
Extra time cost:       $2.00
Extra gas cost:        $1.00
--------------------------------
Net savings:           $3.00
```

If net savings is positive, the app may show:

```text
Save $3.00 on your next fillup
```

If the detour costs more than the savings, the app honestly says:

```text
No detours worth taking right now
```

## Database Design

The project uses a PostgreSQL database with tables such as:

| Table | Purpose |
|---|---|
| `stations` | Stores gas station name, address, location, and current price |
| `reports` | Stores user-submitted gas price reports |
| `users` | Stores user wallet, email, earnings, and saved amount |
| `oracle_queries` | Stores oracle query history and payment information |

This makes the project more realistic because it stores actual users, reports, station prices, and oracle activity.

## What I Learned From This Project

While reviewing this project, I learned:

- How a real-world oracle project can be designed
- How user incentives can improve data freshness
- How to combine Web2 data with Web3 payments
- How gas station price reports can be validated
- How to calculate implied price from transaction amount and gallons
- How to use consensus logic to reduce fake or incorrect reports
- How to detect outlier reports
- How to design a reward system based on freshness
- How to build location-based recommendations
- How to calculate whether a gas station detour is actually worth it
- How to structure a full-stack Next.js application
- How database schema supports product logic

## Key Engineering Lessons

### 1. Incentives Can Improve Data Quality

The project rewards users when they provide useful gas price data.

This is a strong idea because users are more likely to report prices when they receive a direct benefit.

### 2. Fresh Data Is More Valuable Than Old Data

The payout system increases rewards when station data is stale.

This means the system does not pay equally for every report. It pays more when the report is more useful.

### 3. Consensus Protects the Oracle

The app does not fully trust a single report. It uses recent reports to calculate a consensus price.

This helps prevent incorrect or fake data from immediately controlling the system.

### 4. Recommendations Should Include Real Costs

The app does not simply recommend the cheapest gas station.

It checks whether the cheaper station is worth the detour by calculating:

```text
fuel savings
time cost
extra gas cost
net savings
```

This makes the recommendation more useful and realistic.

### 5. A Strong Hackathon Project Solves a Real Problem

This project solves a problem that everyday drivers understand:

```text
Where can I get cheaper gas, and is it worth driving there?
```

Then it adds a Web3 layer:

```text
Get paid in USDC for keeping the data accurate.
```

That makes the idea easy to explain and technically interesting.

## Takeaway

The biggest takeaway from this project is:

> A good Web3 app should not use blockchain just for hype. It should use blockchain where payments, rewards, ownership, or verification actually improve the product.

Gas Oracle uses USDC rewards to motivate users to submit fresh and useful gas price data.

That makes the blockchain part connected to the product’s core value.

## Credit

This README is based on my personal review and learning from the original Gas Oracle project.

Original project by: lsarta  
Original repository: https://github.com/lsarta/gas-oracle