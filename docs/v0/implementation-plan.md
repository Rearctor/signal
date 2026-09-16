# Rearctor Signal - v0 Implementation Plan

## Status

**Concept / Research.** This plan describes work that has not started. No phase is
complete, in progress, or scheduled.

There are **no dates** in this document. Phase ordering is a dependency order, not a
timeline. No pipeline exists, no indexer exists, no signal has been published, no test
harness exists, and no threshold has been agreed.

Companion document: [v0 design specification](design-spec.md).

## Phase A - Canonical event inputs

**Objective.** An exhaustive, agreed list of the chain events Signal reads, and what each
carries.

**Inputs.**
- [docs/telemetry-pipeline.md](../telemetry-pipeline.md)
- [docs/v0/design-spec.md](design-spec.md)

**Deliverables.**
- A complete event catalogue with the fields each event provides.
- Identification of any metric in the catalogue that cannot be derived from available
  events - and therefore cannot be a signal.
- A reorg and finality policy per event kind.

**Blockers.**
- Confirmation depth undecided.
- Whether the event set is sufficient for holder concentration depends on Issue #1.

**Exit criteria.**
- Every candidate signal in [catalog/signals.md](../../catalog/signals.md) traces to
  events in this catalogue, or is marked as unsupportable.
- No event field is used anywhere that this catalogue does not provide.

## Phase B - Normalization model

**Objective.** A canonical record shape per event kind that two implementations produce
identically.

**Inputs.** Phase A output.

**Deliverables.**
- A normalized record definition per event kind.
- Rules for event identity, ordering and deduplication.
- A retraction model for reorged events.

**Blockers.** Phase A incomplete.

**Exit criteria.**
- Normalization is lossless: no interpretive decision occurs at this stage.
- All amounts are base-unit integers. No floating-point value appears anywhere in the
  model.
- Two independent implementations produce byte-identical records for the same block
  range, or the divergence is documented as a defect.

## Phase C - Metric derivation

**Objective.** Windowed metrics computed from normalized records, published independently
of any label.

**Inputs.** Phase B output.

**Deliverables.**
- A metric definition per candidate, each with its window expressed in blocks.
- A documented exclusion set for holder-count and concentration metrics: contracts, the
  migrated pool position, routers, burn and system addresses.
- Tie-breaking and edge-case rules.

**Blockers.**
- Issue #1 open. The exclusion set and concentration statistic are undecided, and
  different statistics disagree on the same distribution.

**Exit criteria.**
- Every metric is reproducible from normalized records alone.
- The exclusion set is enumerated and justified, or the metric is withdrawn.
- Metrics are publishable without any threshold being chosen.

## Phase D - Signal evaluator

**Objective.** A pure function from metrics and thresholds to labels.

**Inputs.** Phase C output; [catalog/signals.md](../../catalog/signals.md).

**Deliverables.**
- An evaluator producing snapshots conforming to
  [specs/signal.schema.json](../../specs/signal.schema.json).
- Threshold values recorded per snapshot rather than referenced globally.
- Presentation rules: absence of a label is never a positive statement.

**Blockers.**
- **No threshold has been agreed for any signal.** Issues #1 and #2 both open.
- The evaluator can be built; it cannot be meaningfully run until thresholds exist.

**Exit criteria.**
- Evaluation depends on nothing but metrics and thresholds - no hidden state, no offchain
  input, no dependence on prior evaluations.
- Every emitted signal carries window, block range and threshold applied.
- No code path produces a composite score, an ordering, or a verdict.

## Phase E - Reproducibility test harness

**Objective.** Demonstrate the central claim: same block range plus same thresholds gives
the same labels.

**Inputs.** Phases B-D.

**Deliverables.**
- A harness running two independent derivations over one block range and diffing results.
- Adversarial cases: reorg mid-window; window boundary alignment; ties at a threshold;
  addresses entering and leaving the exclusion set.
- A record of every divergence found.

**Blockers.** Phases B-D incomplete.

**Exit criteria.**
- Any divergence between implementations is either eliminated or documented as a known
  reproducibility defect.
- The harness fails when a float is deliberately introduced. A test that cannot fail
  proves nothing.

## Phase F - Public telemetry interface research

**Objective.** Determine what a consumer sees, and whether intermediate metrics are
published alongside labels.

**Inputs.** Phases A-E.

**Deliverables.**
- A decision on publishing raw metric values with labels.
- Presentation rules ensuring labels are never collapsed into a verdict.
- An assessment of whether publishing labels changes the behaviour being measured -
  particularly address splitting in response to concentration labels.

**Blockers.** Phases A-E incomplete. Issue #2 must resolve how, or whether,
`CREATOR_HISTORY` is presented given that absence is uninformative.

**Exit criteria.**
- The interface exposes no ordering of reactions.
- Every published label is accompanied by enough provenance for independent verification.
- The measurement-changes-behaviour risk is documented even where unmitigated.

## Status of every phase

| Phase | Status |
| :--- | :--- |
| A - Canonical event inputs | Not started. |
| B - Normalization model | Not started. Blocked on Phase A. |
| C - Metric derivation | Not started. Blocked on Phase B and Issue #1. |
| D - Signal evaluator | Not started. Blocked on Phase C and both Issues. |
| E - Reproducibility harness | Not started. Blocked on Phases B-D. |
| F - Public telemetry research | Not started. Blocked on Phases A-E. |

No phase has begun. No deliverable in this document exists.

## Open design issues

- [#1 - Define reproducible holder concentration signal](https://github.com/Rearctor/signal/issues/1)
- [#2 - Define creator attribution boundaries](https://github.com/Rearctor/signal/issues/2)
