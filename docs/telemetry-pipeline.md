# Telemetry Pipeline

**Status: Concept / Research.** Conceptual only. No pipeline is implemented, deployed,
or scheduled. No infrastructure choices are made here.

## Stages

```
  chain events
       |
       v
  normalization        canonical event records, one shape per event kind
       |
       v
  derived metrics      windowed aggregates over normalized records
       |
       v
  signal evaluation    labels, each with threshold and window
       |
       v
  public telemetry     what a reader or downstream consumer sees
```

Each stage is separated because each has a different failure mode and a different
reproducibility requirement.

## Chain events

The source of truth. Everything downstream is derived and therefore recomputable.

Event kinds relevant to Signal:

| Kind | Carries |
| :--- | :--- |
| Reaction deployed | reaction id, creator address, initial supply, fee configuration |
| Curve trade | direction, USDC amount, token amount, trader, resulting reserves |
| Fee accrual | amount, denomination, split destination |
| Ignition | reserves at threshold, migrated supply, pool reference |
| Pool trade (post-ignition) | direction, amounts, pool reference |
| Token transfer | from, to, amount |

**Reorg handling.** A signal computed on a block later orphaned was never true. The
pipeline must treat finality as a first-class input rather than assuming the chain tip
is stable.

> **Open question.** How many confirmations before a signal is published? Publishing
> at tip is responsive and occasionally wrong; waiting is correct and less useful for
> anything resembling live monitoring. The right answer may differ per signal -
> `NEAR CRITICAL MASS` tolerates lag badly, `CREATOR HISTORY` tolerates it well.

## Normalization

Raw logs become canonical records with:

- a stable event identity (chain reference: block, transaction, log index);
- typed amounts as base-unit integers, never floats;
- explicit denomination;
- reaction identity resolved.

Normalization is **lossless and non-interpretive**. No thresholds, no derived values.
If normalization makes a judgement, that judgement becomes invisible downstream.

**Precision.** Amounts stay integers end to end. A float introduced for convenience at
this stage produces signals that two implementations cannot reproduce identically -
directly violating the reproducibility property.

## Derived metrics

Windowed aggregates over normalized records. Each metric declares its window and
derivation.

Candidates: reserve level, reserve delta over window, trade count by direction, unique
trader count, distinct holder count, supply concentration statistics, fee accrual rate.

**Metrics are not signals.** A metric is a number; a signal is a labelled condition.
Keeping them separate means the raw numbers remain publishable even where no threshold
has been agreed - which, at present, is everywhere.

> **Under research.** Whether distinct holder count should exclude contract addresses,
> the migrated pool position, and known routers. Counting the pool as a holder is
> clearly wrong; enumerating what to exclude is an open-ended classification problem.
> See [creator-attribution.md](../research/creator-attribution.md) for the same problem
> in harsher form.

## Signal evaluation

Applies thresholds to metrics to produce labels. The only stage that makes judgements,
and therefore the only stage where a wrong decision is invisible to a reader.

Requirements:

- every threshold documented alongside the label;
- every label carries the window and block range it was computed over;
- evaluation is a pure function of the metrics and thresholds - no hidden state.

Since no thresholds are chosen (see [signal-model.md](signal-model.md)), this stage is
currently specified only in shape.

## Public telemetry

What a consumer sees. Constraints follow from the four properties:

- a signal without its window and block range is not reproducible - never publish one;
- labels are presented as a set, never collapsed into a verdict;
- raw metrics remain available beside labels, so a reader can check the underlying fact;
- absence of a label is not published as a positive statement.

## Reproducibility contract

The pipeline's claim is: **given the same block range and the same published
thresholds, an independent implementation produces the same labels.**

Reproducibility is broken by any of: floats, wall-clock rather than block-height
windows, hidden exclusion lists, unspecified tie-breaking, dependence on offchain data.

> **Open question.** Should the pipeline publish the intermediate metric values
> alongside labels so third parties can verify without reindexing? This is more useful
> and more costly, and it makes a wrong threshold immediately visible - arguably a
> feature.

## Related documents

- [Signal model](signal-model.md)
- [Signal catalog](../catalog/signals.md)
- [Draft signal schema](../specs/signal.schema.json)
