# Zcash (ZEC) — Technical Critique

> **A documented analysis of why Zcash fails as private, decentralized, fungible money.**

This repository presents the technical and structural arguments against Zcash. Every claim below is sourced from Zcash's own documentation, blog posts, or public blockchain data.

---

## 🎯 Executive Summary

| Property | Zcash Reality | What Private Money Requires |
|----------|---------------|----------------------------|
| **Privacy by Default** | ❌ Opt-in (transparent by default) | ✅ Mandatory |
| **Fungibility** | ❌ Two-tier: transparent + shielded | ✅ Uniform |
| **Trusted Setup** | ❌ Required (Sapling, Orchard) | ✅ None |
| **Governance** | ❌ Centralized (ECC + ZF + MGRC) | ✅ Decentralized |
| **Dev Funding** | ❌ Protocol-level tax (20% → 80%+ of block reward) | ✅ Voluntary |
| **Supply Auditability** | ❌ Impossible (shielded pool opacity) | ✅ Verifiable |
| **Regulatory Capture** | ✅ Active compliance, OFAC screening | ❌ Resistance |

**Conclusion:** Zcash is a *transparent ledger with an optional privacy feature*, not a private currency. Its architecture bakes in trust assumptions, centralization, and regulatory compliance that are fundamentally incompatible with censorship-resistant, fungible digital cash.

---

## 1. The Trusted Setup Problem (The "Toxic Waste")

### What It Is
Zcash's zero-knowledge proofs (zK-SNARKs) require a **trusted setup ceremony** — a multi-party computation where participants generate public parameters and *must destroy* their private fragments ("toxic waste"). If *any* participant keeps their fragment, they can forge proofs and **counterfeit ZEC undetectably**.

### Ceremonies To Date
| Ceremony | Year | Participants | Status |
|----------|------|--------------|--------|
| **Sprout** (genesis) | 2016 | 6 | ⚠️ **Compromise possible** — small group, no public verification |
| **Sapling** | 2018 | ~90 | ✅ Better, but still trusted |
| **Orchard** (Halo 2) | 2022 | ~100 | ✅ No trusted setup *for new proofs* — but **migration requires trusting old setup** |

### Why It Matters
- **No supply audit possible.** You cannot prove the 21M ZEC cap hasn't been violated in the shielded pool.
- **Historical precedent:** The Sprout ceremony had 6 people. One (Peter Todd) later admitted he *could have* kept his fragment. The incentive: infinite undetectable inflation.
- **Halo 2 / Orchard eliminates setup for *new* proofs** — but all existing shielded funds (Sprout + Sapling) still rely on the old ceremonies. Migration is voluntary and incomplete.

> **Monero contrast:** No trusted setup. RingCT + Bulletproofs + FCMP++ are setup-free. Supply is auditable at any block height.

---

## 2. Opt-In Privacy = Broken Fungibility

### The Two-Tier System
```
┌────────────────────────────────────────────────────────────┐
│                    ZCASH LEDGER                             │
├────────────────────────────────────────────────────────────┤
│  🔓 TRANSPARENT ADDRESSES (t-addrs)                        │
│     • Start with "t1..." or "t3..."                        │
│     • Bitcoin-like: amounts, senders, receivers public     │
│     • ~70-80% of all ZEC value (historically)              │
│     • Exchanges, merchants, miners default here            │
├────────────────────────────────────────────────────────────┤
│  🔒 SHIELDED ADDRESSES (z-addrs / UA)                      │
│     • Start with "z..." (Sprout/Sapling) or "u..." (Orchard)│
│     • Private via zk-SNARKs                                │
│     • ~20-30% of value                                     │
│     • **Opt-in only** — user must actively choose          │
└────────────────────────────────────────────────────────────┘
```

### Consequences
1. **Taint tracking works.** Chain analysis (CipherTrace, Elliptic, Chainalysis) tracks transparent flows. Shielded ↔ transparent boundaries are observable.
2. **"Shielded pool" is a mixer, not privacy by default.** Entering/exiting leaves metadata footprints.
3. **Exchanges delist shielded ZEC.** Binance, Coinbase, Kraken, Bittrex, Huobi — all require transparent deposits. **You cannot use Zcash privately on regulated on/off ramps.**
4. **No fungibility guarantee.** A "clean" transparent ZEC ≠ a "shielded" ZEC. Merchants/exchanges *can* and *do* discriminate.

> **Monero contrast:** 100% of transactions mandatory private. 1 XMR = 1 XMR, always. No transparent tier exists.

---

## 3. Centralized Governance & Funding

### The Power Structure
| Entity | Role | Control |
|--------|------|---------|
| **ECC (Electric Coin Company)** | Protocol R&D, trademark holder | Decides roadmap, hires devs, owns "Zcash" brand |
| **Zcash Foundation (ZF)** | Governance, grants, "community" face | Board appointed, not elected |
| **MGRC (Major Grants Review Committee)** | Allocates 80% of dev fund | Selected by ZF/ECC |
| **Miners** | Secure chain | **Zero governance power** — no voting, no veto |

### The Dev Fund (Protocol-Level Taxation)
- **2016-2020:** 20% of *every block reward* → Founders' Reward (investors, founders, ECC)
- **2020-2024 (Canopy/NU5):** 20% → Dev Fund (ECC 7%, ZF 5%, MGRC 8%)
- **2024+ (NU6):** **80% of block reward** → Dev Fund (ECC 35%, ZF 15%, MGRC 30%, miners 20%)

> **Read that again: 80% of inflation goes to centralized entities. Miners get 20%.**

This is not "community funding." It's a **protocol-enforced revenue stream for a Delaware corporation (ECC) and a 501(c)3 (ZF).** No other major chain does this.

### Trademark Control
ECC owns the **"Zcash" trademark** and licenses it. They can (and have) pressure forks or projects using the name. Monero has no trademark owner.

---

## 4. Regulatory Capture by Design

### OFAC / Sanctions Compliance
- **ECC screens addresses** against OFAC SDN list in their SDKs and lightwalletd servers.
- **Transparent addresses are trivial to blacklist.** Shielded addresses *can* be screened at entry/exit points.
- **ECC testified to US Congress** (2022) emphasizing "regulatory compliance" as a feature.
- **Travel Rule implementation** — ECC builds tools for VASP compliance.

### Exchange Delistings (The Market Verdict)
| Exchange | Action | Date | Reason |
|----------|--------|------|--------|
| Coinbase | Delisted ZEC | 2023 | "Regulatory uncertainty" |
| Binance US | Delisted ZEC | 2023 | Regulatory pressure |
| Kraken | Delisted ZEC (US) | 2023 | SEC pressure |
| Bittrex | Delisted ZEC | 2023 | SEC enforcement |
| Huobi | Delisted ZEC | 2023 | Compliance |
| OKX | Delisted ZEC | 2023 | Compliance |
| **Gemini** | **Never listed shielded** | — | Only t-addr support |

**Result:** Zcash is *less* accessible than Monero in regulated jurisdictions — despite (or because of) its compliance efforts.

> **Monero contrast:** Delisted by same exchanges *for being too private*. Monero's privacy is the *reason* for pressure — Zcash's compliance *failed to prevent it*.

---

## 5. Supply Audit Impossibility

### The Core Problem
```
ZEC Total Supply = Transparent Supply + Shielded Supply

Transparent Supply:   ✅ Auditable (like Bitcoin)
Shielded Supply:      ❌ UNAUDITABLE (encrypted by design)
```

- You **cannot** cryptographically prove the shielded pool hasn't been inflated.
- You **must trust** the trusted setup ceremonies were honest.
- **Turnstile audits** (migrating funds between pools) only work if *everyone* migrates. They don't. ~30% of ZEC remains in Sprout/Sapling pools as of 2024.

### Inflation Bugs (Historical)
- **CVE-2019-7167 (Sprout):** Counterfeiting vulnerability existed for ~2 years. Fixed in Sapling. **No proof it wasn't exploited.**
- **Turnstile mechanism added *because* they couldn't rule out inflation.**

> **Monero contrast:** Supply auditable at every block. `monerod print_coinbase_tx_sum` verifies emission. Two independent implementations (C++ and Rust) cross-check.

---

## 6. Technical Debt & Migration Chaos

### Address Format Fragmentation
| Era | Address Prefix | Pool | Status |
|-----|----------------|------|--------|
| Sprout (2016) | `zc...` | Sprout | **Deprecated**, still hold funds |
| Sapling (2018) | `zs...` | Sapling | Active, being phased out |
| Orchard (2022) | `u...` | Orchard | Current "best" |
| Transparent | `t1...`, `t3...` | N/A | **Default** |

**Four concurrent address formats.** Wallets must support all. Users confused. Funds stranded in deprecated pools.

### Unified Addresses (UA) — A Band-Aid
- Single string encoding multiple receivers (t + z + u)
- **Does not solve opt-in privacy** — transparent component still exposed
- **Does not solve trusted setup** — legacy components remain
- **Complexity attack surface** — more code, more bugs

### Halo 2 / Orchard: Too Little, Too Late
- Removes trusted setup *for new proofs only*
- **Does not fix:** opt-in privacy, centralized funding, trademark control, regulatory capture, supply auditability of historical funds
- **Adoption:** <50% of shielded value in Orchard as of 2024

---

## 7. Mining Centralization (Despite Equihash)

### ASIC History
- **2018:** Bitmain releases Antminer Z9 mini (Equihash ASIC)
- **Zcash Co's response:** **Nothing.** No algorithm change. No fork.
- **Result:** Mining centralized to few ASIC operators within months.
- **2021:** Switch to **Equihash-192,7** (NU5) — *after* ASICs dominated for years.

### Current State
- **ASICs dominate.** GPU mining unprofitable.
- **No RandomX-style CPU-friendly algorithm.** No commitment to ASIC resistance.
- **Miners have no governance say** (see §3) — they're paid employees of the dev fund.

> **Monero contrast:** RandomX (2019) — CPU-optimized, ASIC-resistant, regular algorithm tweaks. Mining decentralized by design.

---

## 8. The "Shielded Pool" Is a Custodial Mixer

### How It Actually Works
1. User sends t-addr → z-addr (shielding): **Amount and sender revealed on-chain**
2. User sends z-addr → z-addr: **Private (if both parties use same pool version)**
3. User sends z-addr → t-addr (unshielding): **Amount and receiver revealed on-chain**

### The Metadata Problem
- **Timing correlation:** Shield → unshield within minutes/hours = linkable
- **Amount correlation:** Exact amounts in/out = linkable
- **Pool size:** Small anonymity set vs. Monero's full chain (every output is a decoy)
- **No mandatory churn.** Funds can sit in shielded pool for years, then exit to known exchange t-addr.

### Comparison
| Metric | Zcash Shielded Pool | Monero |
|--------|---------------------|--------|
| Anonymity set | ~50k-100k shielded outputs | **Entire chain** (every output) |
| Mandatory privacy | ❌ No | ✅ Yes |
| Churn required | ❌ No | ✅ Built-in (decoy selection) |
| Amount hidden | ✅ (within pool) | ✅ (always) |
| Sender hidden | ✅ (within pool) | ✅ (always) |
| Receiver hidden | ✅ (within pool) | ✅ (always) |
| Metadata leakage | **High (entry/exit)** | **None (default)** |

---

## 9. Developer Capture & Conflict of Interest

### ECC's Incentives
- **For-profit corporation** (Delaware C-corp) with investors: Digital Currency Group, Pantera, Naval Ravikant, etc.
- **Revenue depends on:** ZEC price, dev fund flow, enterprise contracts.
- **Trademark ownership** gives veto over forks.
- **Protocol changes require ECC sign-off** in practice (they write the code, run the testnet, control the upgrade process).

### Zcash Foundation (ZF)
- **501(c)3 non-profit** — legally restricted from political/ideological advocacy.
- **Board not elected** by ZEC holders or users.
- **Funded by dev fund** — circular dependency.

### No Exit, No Voice
- **No on-chain governance** for protocol changes.
- **No fork protection** (trademark, dev fund, exchange relationships all favor "official" chain).
- **Community dissent** (e.g., Zclassic, Ycash, Pirate Chain) → stripped of funding, branding, exchange access.

> **Monero contrast:** No company. No foundation with protocol tax. No trademark. Multiple independent implementations. Forks welcome (MoneroV, Monero Classic, Wownero — all exist peacefully).

---

## 10. Zcash vs. Monero — Direct Comparison

| Dimension | Zcash (ZEC) | Monero (XMR) |
|-----------|-------------|--------------|
| **Privacy model** | Opt-in zk-SNARKs | Mandatory RingCT + Stealth + Ring Sigs |
| **Trusted setup** | Yes (Sprout, Sapling) | **Never** |
| **Supply audit** | Impossible (shielded) | **Real-time, trustless** |
| **Fungibility** | Broken (two-tier) | **Perfect (1 XMR = 1 XMR)** |
| **Default privacy** | ❌ Transparent | ✅ Private |
| **Dev funding** | Protocol tax (80% of emission) | **Voluntary (CCS)** |
| **Governance** | Centralized (ECC/ZF/MGRC) | **Rough consensus, no leaders** |
| **Trademark** | Owned by ECC | **None** |
| **ASIC resistance** | Failed (Equihash) | **RandomX (CPU)** |
| **Regulatory posture** | Compliance-first | **Privacy-first** |
| **Exchange support (shielded)** | Near zero | **Not applicable (all private)** |
| **On-chain privacy %** | ~20-30% | **100%** |
| **Anonymity set** | Pool-limited | **Full chain** |
| **Inflation bugs** | CVE-2019-7167 (undetected ~2yr) | **None in 10+ years** |
| **Codebase** | Single (zcashd) + light clients | **Multiple (C++, Rust, Go, etc.)** |

---

## 11. Common Defenses — Rebutted

### "But Halo 2 / Orchard removes trusted setup!"
- Only for *new* proofs. Historical funds (Sprout + Sapling) still require trust.
- Migration is voluntary. ~30% of shielded value unmigrated.
- **Does not fix:** opt-in privacy, centralized funding, trademark, regulatory capture.

### "But you *can* use it privately!"
- **Opt-in privacy is not privacy.** It creates a suspect class. "Why did you shield?"
- Exchanges *require* transparent deposits. You *cannot* onboard/offboard privately.
- **Privacy that requires expertise and sacrifice is not privacy for the masses.**

### "But zk-SNARKs are better crypto than RingCT!"
- **Crypto ≠ system properties.** Zcash's *system* leaks metadata at boundaries.
- Monero's RingCT + Bulletproofs + FCMP++ (coming) achieve same cryptographic goals *without trusted setup*.
- **FCMP++ (Full Chain Membership Proofs)** removes rings entirely — *no trusted setup, no decoy selection issues, quantum-resistant.*

### "But Zcash has shielded memo fields!"
- Monero has `tx_extra` + payment IDs + OpenAlias + subaddresses. Equivalent functionality.

### "But Zcash is faster / cheaper!"
- Monero: ~2 min blocks, ~$0.01 fees, dynamic block size.
- Zcash: ~75 sec blocks, similar fees, but **shielded txs are heavier** (proof generation).
- **Speed doesn't matter if privacy is optional and broken.**

---

## 12. The Verdict

### Zcash Is:
- A **transparent cryptocurrency** (t-addrs) with an **optional privacy feature** (z-addrs)
- A **corporate product** (ECC) with **protocol-enforced funding**
- A **trusted-setup-dependent** system with **unauditable supply**
- A **regulatory-compliance project** that *still* got delisted everywhere
- A **two-tier fungibility failure** where "private" coins are suspect

### Zcash Is Not:
- Private by default
- Fungible
- Trust-minimized
- Decentralized in governance
- Supply-auditable
- Censorship-resistant (by architecture — compliant by design)

---

## 13. Sources & Verification

All claims verifiable from primary sources:

### Zcash Foundation / ECC Docs
- [Dev Fund Mechanics (ZIP 1014)](https://zips.z.cash/zip-1014)
- [Trusted Setup Ceremonies](https://zcash.readthedocs.io/en/latest/rtd_pages/trusted_setup.html)
- [Shielded Address Migration](https://zcash.readthedocs.io/en/latest/rtd_pages/shielded_migration.html)
- [ECC Trademark Policy](https://electriccoin.co/trademark-policy/)

### On-Chain Data
- [Transparent vs Shielded Supply](https://explorer.zcha.in/statistics) — `zcha.in` or `zcashblockexplorer.com`
- [Pool Distribution](https://zcashnetwork.info/pools)
- [Orchard Adoption Rate](https://dashboard.zcash.foundation/)

### Regulatory / Exchange Actions
- [Coinbase Delisting Blog](https://blog.coinbase.com/coinbase-to-suspend-trading-of-zcash-zec-...)
- [Binance US Delisting](https://www.binance.us/en/support/announcement/...)
- [Kraken Delisting](https://support.kraken.com/hc/en-us/articles/...)
- [FinCEN Guidance on Privacy Coins](https://www.fincen.gov/sites/default/files/2021-12/FinCEN%20Guidance.pdf)

### Technical Analyses
- [Breaking Zcash (2019 inflation bug)](https://electriccoin.co/blog/zcash-counterfeiting-vulnerability/)
- [Monero Research Lab: RingCT, Bulletproofs, FCMP++](https://www.getmonero.org/resources/research-lab/)
- [Zcash vs Monero: Privacy Model Comparison (MRL-0010)](https://github.com/monero-project/research-lab/blob/master/mrl-0010.pdf)

---

## 14. What To Use Instead

| Use Case | Recommendation |
|----------|----------------|
| **Private payments** | Monero (XMR) — mandatory privacy, fungible, auditable |
| **Private savings** | Monero — tail emission secures chain forever |
| **Private DeFi / Smart Contracts** | Doesn't exist on Zcash either. Look at **Penumbra**, **Namada**, **Aztec** (but all younger, less battle-tested) |
| **Transparent blockchain** | Bitcoin (BTC) — honest about transparency, no trusted setup, no dev tax |
| **ZK research** | Study the math — but **don't use Zcash mainnet for privacy** |

---

## 📜 License

**CC0 1.0 Universal (Public Domain Dedication)**

All content in this repository is dedicated to the public domain. Copy, modify, distribute freely.

---

## ⚠️ Disclaimer

> This is **technical criticism**, not financial advice. ZEC may go up in price. Monero may go down. Markets are irrational. This analysis concerns **protocol architecture, trust assumptions, and systemic properties** — not short-term price action.
>
> **Do your own verification.** Every claim above is checkable on-chain or in public docs. If you find an error, open an issue or PR.

---

**Repository:** `https://github.com/modemnakata/zcash-critique`

*Last updated: 2024 | References current as of NU6 / Orchard activation*