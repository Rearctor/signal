# Signal Model

**Status: Concept / Research.** Nothing described here is implemented, deployed, or
scheduled.

## Four properties

Every candidate signal is judged against four properties. A proposal failing any of
them is rejected rather than weakened.

### Observable

A signal derives only from data present on chain. No survey data, no social metrics, no
private feeds, no self-reported information from creators.

**Test:** could an independent party, with only an archive node, compute this signal?
If not, it is not a signal.

### Reproducible

A signal's inputs, window, and derivation are documented well enough that a third party
recomputes the same value. This implies:

- the block range is part of the signal, not an implicit "now";
- the derivation is specified, not merely named;
- ties and edge cases are defined.

**Test:** two independent implementations, given the same block range, produce the same
output. If they can disagree, the specification is incomplete.

### Descriptive

A signal states a condition. It does not rate, rank by quality, or imply endorsement.

`HIGH HOLDER CONCENTRATION` is a fact about a distribution. It is not a warning, and
not advice. Whether concentration is good or bad depends on context Signal does not
have.

**Test:** could the label be read as a recommendation? If the natural reading is "this
is a good reaction", it is mis-specified.

### Non-predictive

Signal describes what has happened. It never states or implies what will happen.

`ACCELERATING` means the observed rate of reserve accumulation over a defined window
was higher than over a prior window. It does not mean the reaction will ignite.

**Test:** does the label make a claim about the future? `LIKELY TO IGNITE` fails.
`NEAR CRITICAL MASS` passes - it describes present distance to a constant.

---

## Why no composite score

A single number - a rating, a grade, a 0-100 - is the obvious product decision and is
explicitly rejected.

**It hides its inputs.** A score of 72 tells a reader nothing about what produced it.
Reproducibility becomes theoretical: you can recompute it only if every weight is
published, and if every weight is published the score adds nothing over the inputs.

**It is a recommendation wearing a description's clothes.** Any ordering of reactions
by a single number is read as a quality ranking, whatever the disclaimer says.

**It creates an optimisation target.** A published scoring function is a specification
for gaming it. Concentration limits become a reason to split holdings across addresses;
velocity thresholds become a reason to wash trade.

Labels have the same problem in weaker form - a threshold is still a target - but a
label says exactly what it observed, so a reader can evaluate the underlying fact
directly.

> **Open question.** Whether even labels create enough of an optimisation target to be
> harmful. Publishing "this reaction has high holder concentration" may encourage
> deliberate address splitting. No mitigation is proposed.

## Signals are multi-valued and non-exclusive

Several labels may apply at once. `NEAR CRITICAL MASS` and `HIGH HOLDER CONCENTRATION`
are independent observations; presenting them together is correct, and collapsing them
into a verdict is exactly what Signal does not do.

There is no "no signals" state meaning "safe". Absence of a label means the condition
was not observed, not that the opposite holds.

## Threshold problem

Every label needs a threshold, and every threshold is a decision this repository has
not made.

The difficulty is not choosing a number - it is that any number is a protocol decision
about what counts as notable, made without data, applied across reactions of very
different sizes. A concentration threshold sensible for a reaction with 2,000 holders
may be meaningless for one with 30.

Options under consideration:

| Approach | Strength | Weakness |
| :--- | :--- | :--- |
| Fixed absolute threshold | Simple, reproducible, stable | Wrong for most reactions at either end of the size range |
| Relative to reaction's own history | Adapts to scale | Undefined for new reactions - the common case |
| Relative to cohort of similar reactions | Adapts to scale and market | Requires cohort definition, which is itself a decision |
| Publish the raw value, no label | No threshold needed | Loses legibility, which was the point |

> **Under research.** No threshold is chosen anywhere in this repository, and the
> catalog deliberately leaves every threshold marked unresolved.

## Temporal scope

A signal without a block range is not reproducible. Every signal carries:

- `observed_at_block` - the chain height at evaluation;
- `window` - the range the derivation covered, where applicable;
- `chain_reference` - enough to locate the state examined.

A signal is a statement about a block range, not about a reaction.

## Related documents

- [Telemetry pipeline](telemetry-pipeline.md)
- [Signal catalog](../catalog/signals.md)
- [Draft signal schema](../specs/signal.schema.json)
- [Creator attribution](../research/creator-attribution.md)
