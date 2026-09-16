# Signal Catalog

**Status: Concept / Research.** Candidate signals only. **No threshold in this
catalogue has been agreed.** Every one is marked unresolved, deliberately: choosing a
number here would be a silent protocol decision about what counts as notable.

Any numeric figure appearing below - proportions, ratios, counts, worked examples - is
Illustrative only. Not protocol defaults or committed parameters. The only
figures in this document that are protocol facts are the 5,042 USDC ignition threshold
and the 1,000,000,000 initial supply.

Each entry follows the same structure: purpose, candidate inputs, output, limitations,
unresolved threshold.

---

## NEAR CRITICAL MASS

**Purpose.** State that a reaction's reserves are close to the 5,042 USDC ignition
threshold. Distance to a constant is one of the few quantities in the protocol that is
unambiguous.

**Candidate inputs.**
- current reserve balance in USDC base units
- the ignition threshold constant (5,042 USDC)

**Output.** Label plus observed reserve value. Point-in-time; window is a single block.

**Limitations.**
- Proximity is not likelihood. A reaction can sit at 95% indefinitely, or retreat -
  sells move reserves down.
- The label is asymmetric in a way readers may not expect: it never appears again after
  ignition, so its absence covers both "far away" and "already ignited".
- Most reactions may never reach the threshold. A label that fires only near it is
  silent for the common case.

**Unresolved threshold.** What proportion counts as near - 80%, 90%, 95%? A single
figure treats a reaction 500 USDC away identically whether it arrived there in an hour
or a month. Whether the label should incorporate rate, or stay purely positional, is
also unresolved.

---

## ACCELERATING

**Purpose.** State that the observed rate of reserve accumulation over a recent window
exceeded the rate over a prior comparison window.

**Candidate inputs.**
- reserve delta over window W1 (recent)
- reserve delta over window W2 (prior, equal length)
- block range for both

**Output.** Label plus the ratio observed. Explicitly backward-looking.

**Limitations.**
- **Most exposed to the non-predictive property.** "Accelerating" invites a reading
  about what comes next. The label describes two past windows and nothing else.
- Extremely sensitive to window length. Any reaction is accelerating over *some*
  window.
- Low-base distortion: a reaction going from 2 USDC to 20 USDC has accelerated 10x and
  is not notable.
- Manipulable: an adversary can produce the observation by trading with themselves,
  paying only fees.

**Unresolved threshold.** Window lengths, the comparison ratio, and whether a minimum
absolute base is required before the label can fire at all. The last may matter most -
without it, the label mostly describes noise on small reactions.

---

## HIGH HOLDER CONCENTRATION

**Purpose.** State that supply is concentrated among few addresses.

**Candidate inputs.**
- balances per address at a block height
- circulating supply at the same height
- exclusion set (reaction contract, migrated pool position, known routers)

**Output.** Label plus the concentration statistic used.

**Limitations.**
- **Addresses are not people.** One party may hold across many addresses; one address
  may custody for many parties. The measurement is of addresses; the natural reading is
  of parties. This gap cannot be closed with onchain data. See
  [creator-attribution.md](../research/creator-attribution.md).
- The exclusion set is an open-ended classification problem. Counting the migrated pool
  as a holder is clearly wrong; enumerating everything else that should be excluded is
  not clearly finishable.
- Concentration is not inherently adverse. Early in a reaction it is arithmetically
  unavoidable.
- Publishing the label may cause deliberate address splitting - the measurement changes
  the behaviour it measures.

**Unresolved threshold.** The statistic itself (top-N share? Gini? Herfindahl?), the
cutoff, and the exclusion set. None chosen. Different statistics disagree on the same
distribution, so the choice is not cosmetic.

---

## BUY PRESSURE

**Purpose.** State that directional trade composition over a window skewed toward buys.

**Candidate inputs.**
- buy volume in USDC over window
- sell volume in USDC over window
- trade counts by direction
- distinct trader count by direction

**Output.** Label plus the ratio observed.

**Limitations.**
- Volume-weighted and count-weighted ratios can point in opposite directions: many
  small buys against one large sell.
- Trivially manipulable - self-trading produces the observation at the cost of fees.
- Pre- and post-ignition are not comparable. Curve trades and pool trades have
  different mechanics; a single label spanning both misleads.
- Says nothing about who is buying. Concentration of buying in one address is a
  materially different fact and is not captured.

**Unresolved threshold.** Ratio cutoff, window length, whether to weight by volume or
count, whether to require a minimum distinct-trader count before firing, and whether
the label should exist separately for curve and pool phases.

---

## CREATOR HISTORY

**Purpose.** State that a creator has prior observable activity across reactions.

**Candidate inputs.**
- count of reactions deployed by the subject
- count that reached ignition
- cumulative volume across those reactions
- fee generation across those reactions
- buyback and liquidity reinvestment execution history

**Output.** Label plus the counts, with an explicit attribution-confidence marker.

**Limitations.**
- **Attribution is the whole problem.** A creator is an address, or a set of addresses
  claimed to be one party. Treating a set as one party without a verification mechanism
  is an identity assumption Signal has no basis for. See
  [creator-attribution.md](../research/creator-attribution.md).
- History is not conduct. A creator with ten ignitions is not thereby trustworthy, and
  a first-time creator is not thereby suspect.
- Asymmetric and gameable in the worst direction: a creator with adverse history can
  start from a fresh address and carry no label at all, while an honest repeat creator
  accumulates one.
- Absence of the label is therefore uninformative and must never be presented as a
  negative.

**Unresolved threshold.** What counts as history - one prior reaction, or several?
Whether ignition count should be surfaced separately from deployment count. How, or
whether, multi-address creators are handled at all.

---

## Rejected candidates

Recorded so the reasoning is not relitigated.

| Candidate | Why rejected |
| :--- | :--- |
| `LIKELY TO IGNITE` | Predictive. Fails the non-predictive property outright. |
| `TRUSTED CREATOR` | Evaluative. States a judgement Signal cannot support. |
| `RUG RISK` | Both predictive and evaluative, and implies a model of intent that no onchain data supports. |
| `UNDERVALUED` | Requires a valuation model. Not observable. |
| Composite score 0-100 | Hides inputs, reads as a ranking, creates an optimisation target. See [signal-model.md](../docs/signal-model.md). |

## Related documents

- [Signal model](../docs/signal-model.md)
- [Telemetry pipeline](../docs/telemetry-pipeline.md)
- [Draft signal schema](../specs/signal.schema.json)
