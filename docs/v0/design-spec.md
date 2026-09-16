# Rearctor Signal - v0 Design Specification

## Status

**Concept / Research.** This is a draft design target, not a production specification.

Nothing described here is implemented, deployed, scheduled, or audited. No pipeline
exists. No APIs exist. No signal has ever been published. **No threshold has been
agreed.** There is no commitment that this design will ship.

Builds on, and does not replace:

- [docs/signal-model.md](../signal-model.md)
- [docs/telemetry-pipeline.md](../telemetry-pipeline.md)
- [catalog/signals.md](../../catalog/signals.md)
- [research/creator-attribution.md](../../research/creator-attribution.md)
- [specs/signal.schema.json](../../specs/signal.schema.json)

Every numeric figure here that is not a Rearctor protocol constant is illustrative only.
Not protocol defaults or committed parameters.

## The four properties

v0 preserves all four without exception. A candidate signal failing any is rejected
rather than weakened.

| Property | Test |
| :--- | :--- |
| **Observable** | Could a third party compute this with only an archive node? |
| **Reproducible** | Do two independent implementations, given the same block range, agree? |
| **Descriptive** | Does the label state a condition rather than a judgement? |
| **Non-predictive** | Does the label avoid any claim about the future? |

**No composite score.** Not in v0, not as an option, not behind a flag. The reasoning is
in [docs/signal-model.md](../signal-model.md): a score hides its inputs, reads as a
ranking whatever the disclaimer says, and publishes a specification for gaming it.

## Scope

### Proposed for v0

- A normalized event model over observable chain data.
- Derived metrics, published independently of any label.
- Labelled signals, each carrying its window, block range and the threshold applied.
- Provenance sufficient for independent recomputation.
- Explicit attribution-confidence marking on creator subjects.

### Under research

- Holder concentration definition, exclusion set and statistic. See Issue #1.
- Creator attribution boundaries. See Issue #2.
- Every threshold in [catalog/signals.md](../../catalog/signals.md).
- Confirmation depth before publication.
- Whether intermediate metric values are published alongside labels.

### Non-goals

- Any composite score, grade, rating or ranking of reactions.
- Prediction of ignition, price, or outcome.
- Recommendation, endorsement, or risk assessment.
- Offchain identity attestation.
- Any signal requiring data a third party cannot independently obtain.

## Data sources

Only observable chain events. No survey data, no social metrics, no private feeds, no
creator self-reporting.

| Event kind | Carries |
| :--- | :--- |
| Reaction deployed | reaction id, creator address, initial supply, fee configuration |
| Curve trade | direction, USDC amount, token amount, trader, resulting reserves |
| Fee accrual | amount, denomination, split destination |
| Ignition | reserves at threshold, migrated supply, pool reference |
| Pool trade | direction, amounts, pool reference |
| Token transfer | from, to, amount |

## Normalized telemetry

Normalization is **lossless and non-interpretive**. Stable event identity from chain
position; typed amounts as base-unit integers, never floats; explicit denomination;
resolved reaction identity.

**Integers end to end.** A float introduced for convenience makes two implementations
disagree, which breaks reproducibility directly.

## Derived metrics

Windowed aggregates over normalized records. Each declares its window in blocks and its
derivation.

Candidates: reserve level; reserve delta over window; trade count by direction; unique
trader count; distinct holding addresses; supply concentration statistics; fee accrual
rate.

**Metrics are not signals.** A metric is a number; a signal is a labelled condition.
Keeping them separate means raw numbers remain publishable where no threshold has been
agreed - which, at present, is everywhere.

## Signal definition

A signal is a statement about **a block range**, not about a reaction. Every signal
carries:

- `label` - a catalogued condition from a closed set
- `window` - the block range the derivation covered
- `threshold_applied` - the expression tested, and its status
- `observed_value` - the measured value, as a decimal string
- `limitations` - known weaknesses of that particular observation

`threshold_applied.status` is `unresolved` for every signal in v0. The field exists so a
disputed label is checkable rather than authoritative.

## Signal evaluation

Evaluation is a **pure function** of metrics and thresholds. No hidden state, no
dependence on prior evaluations, no offchain input.

Signals are **multi-valued and non-exclusive**. Several may apply at once. There is no
"no signals" state meaning safe: absence of a label means the condition was not
observed, not that its opposite holds.

## Provenance

Every published signal carries enough to be recomputed by a third party:

- block height and block hash at evaluation
- confirmations behind tip
- the window in blocks
- the threshold expression applied
- the observed value

A signal without its block range is not reproducible and must not be published.

## Reproducibility

The claim v0 makes: **given the same block range and the same published thresholds, an
independent implementation produces the same labels.**

Reproducibility is broken by any of: floating-point arithmetic; wall-clock rather than
block-height windows; undocumented exclusion lists; unspecified tie-breaking; dependence
on offchain data.

Each of these is treated as a defect, not a trade-off.

## Finality and reorg handling

A signal computed on a block later orphaned was never true.

- Every snapshot carries `confirmations`.
- Consumers may reject snapshots below their own finality requirement.
- The pipeline treats finality as an input, not as an assumption about the chain tip.

**Unresolved:** confirmation depth before publication. The right answer may differ per
signal - `NEAR_CRITICAL_MASS` tolerates lag badly, `CREATOR_HISTORY` tolerates it well.

## Creator attribution

A creator is an address. Associating several addresses with one party requires either an
assumption or a verification mechanism, and assumptions fail the observable and
reproducible properties.

| Confidence | Meaning | v0 position |
| :--- | :--- | :--- |
| `single-address` | Subject is one address; no linking claimed | Supported |
| `declared` | Addresses linked by signed declaration; control proven at declaration time | Under research |
| `heuristic` | Linked by inference | **Not reproducible.** Retention in the schema is itself an open question |

**The asymmetry that has no fix.** Accumulating history is opt-out: a creator can shed
it by using a fresh address. So `CREATOR_HISTORY` can attest presence but never absence.

**Absence of creator history must never be presented as a positive signal**, or as a
neutral one that a reader will take as positive. This constraint is binding on
presentation, not only on data. Whether any presentation can avoid the inference is
itself unresolved. See Issue #2.

## Output model

```
  snapshot
  +-- subject            reaction or creator, with attribution confidence
  +-- observed_at        block height, block hash, confirmations
  +-- signals[]          label, window, threshold_applied, observed_value, limitations
  +-- metrics{}          raw derived values behind the labels
  +-- notes              free text; used to mark illustrative snapshots non-normative
```

Constraints on presentation:

- labels are presented as a set, never collapsed into a verdict;
- raw metrics remain available beside labels so a reader can check the underlying fact;
- absence of a label is never published as a positive statement;
- no ordering of reactions by any signal or combination of signals.

## Explicit exclusions

Recorded so the reasoning is not relitigated. Each was considered and rejected:

| Excluded | Reason |
| :--- | :--- |
| Composite score | Hides inputs; reads as a ranking; publishes a gaming specification |
| `LIKELY TO IGNITE` | Predictive |
| `TRUSTED CREATOR` | Evaluative |
| `RUG RISK` | Predictive and evaluative; implies a model of intent |
| `UNDERVALUED` | Requires a valuation model; not observable |
| Ordering reactions by signal | Becomes a ranking regardless of framing |
| Offchain identity attestation | Introduces a trusted party and an offchain dependency |

## Open design issues

- [#1 - Define reproducible holder concentration signal](https://github.com/Rearctor/signal/issues/1)
- [#2 - Define creator attribution boundaries](https://github.com/Rearctor/signal/issues/2)

Both block v0: `HIGH_HOLDER_CONCENTRATION` and `CREATOR_HISTORY` cannot be specified
until they are resolved, and the remaining labels cannot be published without agreed
thresholds.

## Related documents

- [Signal model](../signal-model.md)
- [Telemetry pipeline](../telemetry-pipeline.md)
- [Signal catalogue](../../catalog/signals.md)
- [Creator attribution](../../research/creator-attribution.md)
- [Implementation plan](implementation-plan.md)
