# Creator Attribution

**Status: Concept / Research.** Open problem. No mechanism is proposed or endorsed.

## The problem

`CREATOR_HISTORY` requires knowing which reactions share a creator. On chain, a
deployment is signed by an address. A "creator" in the sense people mean - a person or
team - is not an onchain object.

Associating multiple addresses with one creator requires either **an assumption** or
**a verification mechanism**. Signal's four properties (observable, reproducible,
descriptive, non-predictive) make assumptions unacceptable: a heuristic link is not
observable, and different heuristics are not reproducible.

This document sets out why each available approach fails, and what that leaves.

## Approaches and their failure modes

### 1. Address equality only

A creator *is* an address. No linking.

- **Observable:** yes. **Reproducible:** yes.
- **Failure mode:** a creator using a fresh address per launch has no history, ever.
  Which is to say: the approach is correct and nearly useless. It is also the only
  approach that introduces no unsupported claim.
- **Asymmetry:** honest repeat creators accumulate history; anyone wishing to shed
  history simply does. History is therefore a weak positive signal and no negative
  signal at all.

### 2. Funding-graph heuristics

Link addresses funded from a common source.

- **Observable:** the transfers are. The *inference* is not.
- **Reproducible:** no. Depth, timing windows, and exchange-address exclusions all
  change the result. Two implementations will disagree.
- **False positives:** anyone funded from the same exchange hot wallet is linked to
  thousands of strangers.
- **False negatives:** one intermediate hop defeats it.
- **Assessment:** publishing a heuristic link as fact would attribute one party's
  conduct to another. This is the most harmful failure mode in the document.

### 3. Behavioural fingerprinting

Link by patterns - deployment timing, parameter choices, gas settings.

- **Reproducible:** no. Every parameter of the fingerprint is a choice.
- **Failure mode:** creators using the same launch tooling share fingerprints by
  construction. The signal measures tooling, not identity.
- **Assessment:** weaker than funding-graph on every axis. Not viable.

### 4. Self-declared linkage with onchain proof

A creator declares "address B is also me" and proves control of both by signing from
each.

- **Observable:** yes - the declarations and signatures are onchain.
- **Reproducible:** yes - the link either has a valid signature pair or does not.
- **Descriptive:** yes, if labelled as *declared*, not *verified as one party*.
- **What it proves:** control of both keys at declaration time. Nothing more.
- **Failure modes:** a creator can decline to declare, so absence means nothing;
  declarations can be made between genuinely different parties who cooperate; key
  control can transfer after declaration.
- **Assessment:** the only approach that survives the four properties, and it only
  works because it stops claiming something it cannot support. It provides no coverage
  where a creator does not opt in - which may be exactly where coverage matters.

### 5. Offchain identity attestation

A third party attests that addresses belong to one entity.

- **Observable:** no. Introduces a trusted party and an offchain dependency.
- **Assessment:** outside Signal's scope as defined. Recorded for completeness.

## What the schema does about it

The draft schema carries `attribution_confidence` on creator subjects, with three
values:

| Value | Meaning |
| :--- | :--- |
| `single-address` | Subject is one address. No linking claimed. |
| `declared` | Addresses linked by signed declaration. Control proven at declaration time. |
| `heuristic` | Linked by inference. Not reproducible; not a claim of fact. |

Carrying the marker does not solve the problem - it makes the problem visible to
consumers rather than hiding it inside a label. A consumer can reject `heuristic`
outright.

> **Open question.** Whether `heuristic` should exist in the schema at all. Including
> it invites use; a value present in a specification tends to be treated as endorsed.
> Removing it means heuristic linking either does not happen or happens undeclared -
> and undeclared is worse.

## The asymmetry that has no fix

Any history mechanism creates an asymmetry:

- accumulating history is **opt-out** - stop using the address;
- accumulating history is **not compulsory** - fresh addresses are free.

So `CREATOR_HISTORY` can attest presence but never absence. A reaction without the
label is not "by a new creator" - it is "no linked history was observed", which
includes the case of a creator who deliberately shed it.

**This must be reflected in presentation.** Displaying the label's absence as a neutral
or positive state is a misreading the design must not enable. Whether any presentation
can avoid that reading is itself unresolved - readers may infer absence regardless of
how it is framed.

## Concentration measurement has the same problem

`HIGH_HOLDER_CONCENTRATION` counts addresses, not parties. Address-level concentration
understates real concentration when one party splits holdings, and overstates it when
one address custodies for many. The measurement is honest about addresses and silent
about parties.

Publishing the label may itself encourage splitting. The measurement changes the
behaviour it measures, and no mitigation is proposed.

## Current position

Address equality (approach 1) is the only approach that is both observable and
reproducible without qualification. Declared linkage (approach 4) is the only extension
that survives the four properties, and only when labelled as declared.

> **Under research.** Whether `CREATOR_HISTORY` should ship at all given the
> asymmetry above. A signal that can only ever say something positive, about a subject
> who chose to be identifiable, may create more misplaced confidence than it removes.

## Related documents

- [Signal model](../docs/signal-model.md)
- [Signal catalog](../catalog/signals.md)
- [Draft signal schema](../specs/signal.schema.json)
