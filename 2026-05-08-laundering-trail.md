# Stolen-Funds Laundering Trail - On-Chain Evidence Pack

**Filed**: 2026-05-08
**Reporter**: STG GROUP AG (Stablegate)
**Entity Reg**: CHE-282.984.669, Zug, Switzerland
**Regulatory status**: Swiss VASP, VQF SRO Member 100702 (FINMA/VQF SRO oversight)
**Capacity**: Acting in AML/CFT capacity under Swiss AMLA
**MLRO contact**: gk@stablegate.com

---

## 1. Summary

A stolen-funds laundering operation has been traced through the following Ethereum addresses between 2026-05-04 and 2026-05-08. The operator routed funds in 3 hops on Ethereum, then off-ramped to Bitcoin via THORChain Router v4.1.1 (legitimate cross-chain swap protocol used as the off-ramp). Approximately 443,000 DAI remains staged for additional cross-chain swaps as of the time of writing.

---

## 2. Subject addresses (Ethereum mainnet)

| Role | Address |
|---|---|
| Primary laundering wallet | `0x118C80Bd57D89DF96a7DC4f6c096Ac07D35feFeA` |
| Hop 1 | `0x14a88277239dcf197408861465ef0409168f0fa6` |
| Hop 2 | `0xa09a78a79146b8f0d920886e7817cb0b918075a4` |

**Excluded** (legitimate protocol, false-positive risk):
- `0xd37bbe5744d730a1d98d8dc97c42f0ca46ad7146` - **THORChain Router v4.1.1**, decentralized cross-chain swap protocol with thousands of legitimate daily users. Used by the operator as an off-ramp but should NOT be tagged as a laundering wallet.

## 3. Subject addresses (Bitcoin mainnet)

| Role | Address |
|---|---|
| Operator BTC destination | `bc1q986dy509crwj2ylp0vp5t7zqls2yfmx6lwn4rm` |

---

## 4. Funds-flow chain

### 4.1 Initial victim transfer
**2026-05-04 14:58 UTC** - victim transferred 105,290.33 USDT to primary laundering wallet `0x118C80Bd...feFeA` under false pretences (impersonation / fake destination).
- Probe tx (1 USDT): `0xb2a66e358b2a14c8991c2dd762e6d66afc3f04cd1d18c591e140ba944a7d42de`
- Main tx (105,290.33 USDT): `0xeccac2726663de3fff6c7a5a668660a239d41700023d4932e1bf737695a074d0` (this is the swap tx; main USDT inbound was earlier in the same window)
- Confirmation tx (1 USDT): `0x0ded9322a29a31a2d3dc2a5cc03a191d71841f747525c502f44d26a0b07e65ce`

The 1-USDT probe + confirmation pattern is consistent with manually operated laundering, not automated service.

### 4.2 Asset conversion
**2026-05-05 03:17 UTC** - laundering wallet swapped USDT to DAI via Uniswap V4 Pool Manager `0x000000000004444c5dc75cb358380d2e3de08a90` and Uniswap V3 DAI/USDT 0.03% pool `0x48da0965ab2d2cbf1c17c09cfb5cbe67ad5b1406`:
- Tx: `0xecaa02ffea0ce0d07f5f110cf8771ce0bdd6eab9ff5f10be5a3b4b79d16c3e63`
- Result: 105,291.33 USDT -> 105,284.56 DAI

The choice of DAI as terminal asset (versus USDT/USDC, which are issuer-freezable) is consistent with deliberate freeze-evasion.

### 4.3 Inbound aggregation 2026-05-06
The laundering wallet received additional inbound DAI on 2026-05-06 (likely additional victims or operator pre-positioning):
- 6 transfers from `0x01261fb8d98f4f7308db89217270bf4e169e221f` totaling ~230k DAI (within 25 minutes)
- 8.5k DAI from `0xd37bbe5744d730a1d98d8dc97c42f0ca46ad7146` (THORChain return swap)
- 10.7k USDT from `0xcd49a5be5502f2afa83cc710fdaa9f6762d1bd0f` on 2026-05-07

### 4.4 Outflow today (2026-05-08)
**2026-05-08 10:24-10:48 UTC** - 530,000 DAI outflow through 3 hops:

| Time UTC | Amount | From | To | Tx |
|---|---|---|---|---|
| 10:24:59 | 0.0125 ETH (gas) | 0x118C80Bd... | 0x14a88277... | `0xcda25d774158f1c17d9fa3f15a3a7d3075d239c40c90051d6e8ad9b34ea3e130` |
| 10:32:59 | 2,636 DAI (probe) | 0x118C80Bd... | 0x14a88277... | `0x08d84ca652d236c4bc06b931d2ad486c9fe2f441254b4441ed915f55377dccb0` |
| 10:37:59 | **530,000 DAI** | 0x118C80Bd... | 0x14a88277... | `0xc212fa0e6b975c1cd3e8f43f6cbd5de61af4c8144ba3acd4bd30b77b5d019f50` |
| 10:50:59 | 0.00375 ETH | 0x14a88277... | 0xa09a78a7... | `0x369222e687265dd...` |
| 10:52:47 | 89,650 DAI | 0x14a88277... | 0xa09a78a7... | `0x9d529e634a6d...` |
| 10:57:11 | 89,650 DAI | 0xa09a78a7... | THORChain Router | `0xd593e079386c10154b5de84dc06c86d9ac63e280e51a57e0f0861bcec3c5acce` |
| 11:02:11 | 76,873 DAI | 0x14a88277... | 0xa09a78a7... | `0x00dd42ae5e03...` |
| 11:12:59 | 79,872 DAI | 0x14a88277... | 0xa09a78a7... | `0x8b79fcc77ce4...` |

### 4.5 THORChain memo decoded
The Deposit event log of tx `0xd593e079386c10154b5de84dc06c86d9ac63e280e51a57e0f0861bcec3c5acce` contains the following memo:

```
=:b:bc1q986dy509crwj2ylp0vp5t7zqls2yfmx6lwn4rm:110096843:sto:0
```

THORChain memo format: `=:CHAIN_SHORTCODE:DESTINATION_ADDRESS:LIMIT:AFFILIATE:FEE_BPS`. `b` is THORChain's shortcut for `BTC.BTC`. So 89,650 DAI was swapped to BTC and sent to operator address `bc1q986dy509crwj2ylp0vp5t7zqls2yfmx6lwn4rm` on Bitcoin mainnet.

### 4.6 Bitcoin mempool confirmation
**2026-05-08 ~10:50 UTC** - THORChain Asgard vault `bc1qe73xw0nmvn695vh8rnkge0f753cy7rezw8sz32` paid out 1.1124 BTC (~USD 111k) to operator destination `bc1q986dy509crwj2ylp0vp5t7zqls2yfmx6lwn4rm`:
- BTC mempool tx: `47d822dd7a5f353878c61ddf0cecac89c595e93d994b493987ae1239e7ecedb8`
- mempool.space view: https://mempool.space/tx/47d822dd7a5f353878c61ddf0cecac89c595e93d994b493987ae1239e7ecedb8

### 4.7 Vanity-prefix decoy wallets
The operator created visually-similar EOA addresses (vanity prefix matching) to confuse blockchain analysts:
- `0x14ada450c3ce69a9e7fcdd359844c2af448f0fa6` (nonce 0, unused) - lookalike of `0x14a88277...` (both end in `...0fa6`, 1 hex char different mid-string)
- `0xa095f03aac46358a7450ca16d7098aee728075a4` (nonce 0, unused) - lookalike of `0xa09a78a7...` (both end in `...8075a4`)

These have not been activated but suggest deliberate operator preparation.

---

## 5. Pattern analysis

| Indicator | Detail |
|---|---|
| Probe + confirmation pattern | 1-USDT probe before main USDT inbound, 1-USDT confirmation after; same pattern in DAI outbound (2,636 DAI probe before 530,000 DAI) |
| Freeze-resistant terminal asset | USDT -> DAI swap (DAI cannot be issuer-frozen) |
| Multi-hop layering | 3 EOA hops with no other purpose, all funded same day, before THORChain off-ramp |
| Cross-chain off-ramp | THORChain (decentralized, non-custodial) chosen over CEX deposit |
| Vanity decoy creation | 2 lookalike addresses pre-created |
| Staged execution | 530k moved in tranches, ~443k still pending swap |
| Operator-controlled BTC | bc1q986dy... is a destination address, not a CEX deposit |

---

## 6. Cross-reports filed

| Platform | Reference | Status |
|---|---|---|
| Chainabuse | report IDs `03481efd-c709-464b-b31c-ae903a0270d3`, `ad69c664-134e-42d0-8262-7d605695b72b`, `88864653-00c6-4302-a2b6-341081c2e5c8`, `4955d98d-d4f1-4968-bba2-ded88e15a28d` | Submitted 2026-05-08 |
| Nine Realms (THORChain ops) | email to security@ninerealms.com | Sent 2026-05-08 |
| ScamSniffer scam-database | PR #566 at scamsniffer/scam-database | Open 2026-05-08 |
| Etherscan abuse | this submission | In progress |

---

## 7. Reporter details

- **STG GROUP AG** (Stablegate)
- Swiss federal company registration: **CHE-282.984.669**, Zug, Switzerland
- Regulatory: Swiss VASP supervised by **VQF SRO** (FINMA-recognized self-regulatory organization)
- **VQF Member 100702**
- Acting in AML/CFT capacity under Swiss AMLA (Anti-Money Laundering Act, RS 955.0)
- **MLRO contact**: gk@stablegate.com
- Compliance head copied: vk@stablegate.com

---

*This document is a public on-chain evidence pack. All transaction hashes, addresses, and timestamps are publicly verifiable on Etherscan / mempool.space. No PII beyond reporter identity is included.*
