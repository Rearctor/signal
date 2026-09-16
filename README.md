# Rearctor Signal

**Status: Concept / Research**

This repository documents a design direction. Nothing described here is implemented,
deployed, or scheduled.

## Overview

Rearctor Signal would be a telemetry and reputation layer derived from observable
onchain data. Reactions produce a public record as they trade - launches, ignitions,
volume, fee generation, holder distribution - and Signal would read that record and
surface it in a legible form.

Signal would describe what has happened. It would not predict what will happen.

## Design principles

- **Observable inputs only.** Every signal derives from onchain data that anyone can
  independently verify.
- **No proprietary score.** Signal would not reduce a reaction or a creator to a single
  opaque number.
- **Legible labels over rankings.** A signal states a condition in plain terms rather
  than asserting quality.
- **Reproducible.** A signal's inputs and derivation should be documented well enough
  that a third party can recompute it.

## Creator signals

Derived from a creator's history across reactions:

| Signal | Observable basis |
| :--- | :--- |
| Launches created | Count of reactions deployed. |
| Ignitions reached | Count of reactions that crossed the 5,042 USDC threshold. |
| Historical volume | Cumulative traded volume across reactions. |
| Fee generation | Fees produced across reactions. |
| Buyback execution history | Buybacks executed from reaction allocations. |
| Liquidity history | Liquidity reinvestment across reactions. |

## Reaction signals

Derived from a single reaction's current and historical state:

| Signal | Observable basis |
| :--- | :--- |
| Ignition progress | Reserves accumulated against the 5,042 USDC threshold. |
| Holder concentration | Distribution of supply across holding addresses. |
| Unique holders | Count of distinct holding addresses. |
| Buy/sell activity | Directional trade composition over a window. |
| Reaction velocity | Rate of change in reserves over time. |
| Creator exposure | The creator's holdings relative to the reaction. |

## Telemetry model

Signals would be expressed as explicit, named conditions rather than as a rating:

```
NEAR CRITICAL MASS
ACCELERATING
HIGH HOLDER CONCENTRATION
BUY PRESSURE
CREATOR HISTORY
```

Each label states a specific, checkable condition. A reader can go to the chain and
confirm it. Several labels may apply to one reaction at once, and a label carries no
implied endorsement - `HIGH HOLDER CONCENTRATION` is a fact about distribution, not a
judgement about the reaction.

## What Signal is not

- **Not a score.** No composite number, no grade, no star rating.
- **Not a recommendation.** Signals do not constitute advice or an endorsement.
- **Not a prediction.** Signals describe observed state, not expected outcomes.
- **Not a whitelist.** Presence or absence of a signal does not qualify or disqualify a
  reaction.
- **Not off-chain reputation.** Signals derive from onchain activity, not from social
  standing.

## Open research questions

- Which thresholds should trigger a label, and how are they chosen without becoming an
  implicit rating?
- Over what windows should velocity and pressure signals be measured?
- How should holder concentration account for contracts, pools, and custodial
  addresses?
- How should a creator's history be attributed across addresses without introducing
  an identity claim?
- How can labels be published so that they remain independently reproducible?

## Research workspace

Current design work is organized across architecture notes, a signal catalogue and
draft specifications. Everything below is concept and research: no implementation
exists, and no threshold has been agreed.

**Architecture**
- [Signal model](docs/signal-model.md) - observable, reproducible, descriptive, non-predictive
- [Telemetry pipeline](docs/telemetry-pipeline.md) - chain events through to public telemetry

**Catalogue**
- [Signal catalogue](catalog/signals.md) - candidate signals with inputs, limitations and unresolved thresholds

**Draft specifications**
- [Signal schema](specs/signal.schema.json) - draft JSON Schema for a signal snapshot
- [Example: signal snapshot](examples/signal-snapshot.json) - a snapshot conforming to the draft schema

**Open research**
- [Creator attribution](research/creator-attribution.md) - associating addresses with a creator without unsupported identity assumptions

## Links

[Website](https://rearctor.io) · [Docs](https://rearctor.io/docs) · [GitHub](https://github.com/Rearctor) · [X](https://x.com/JoinRearctor) · [Telegram](https://t.me/rearctor)
