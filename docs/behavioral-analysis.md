# Reproducible Behavioral Analysis

[< Back to README](../README.md) · [Pillars](#pillars)

Reproducible behavioral analysis is the REDORAS measurement discipline: every claim about how an AI system behaves
must be tied to a fully specified, replayable run. The goal is **reproducible behavioral analysis** - understanding
and comparing behavior across models, harnesses, and time. It is explicitly **not** weaponization; see
[Governance](tools.md#governance).

This is what makes the other pillars scientific rather than anecdotal: [chaos experiments](chaos-engineering.md) and
[red-team findings](red-teaming.md) are only useful if they can be replayed and compared.

## Why behavior is hard to reproduce

A run is the product of many moving parts, most of them invisible by default:

- model identity and **revision** (a floating tag can change underneath you),
- decoding parameters and seeds,
- the prompt manifest and ordering,
- harness control flow, tools, and retries,
- retrieved data and its version,
- infrastructure (engine, version, hardware, rate limits),
- the environment's current state (caches, concurrency).

Change any one and the behavior can change. Omission of any one makes the result unfalsifiable. The answer is not
to freeze the world - it is to **record it**.

## The reproducibility contract

Every REDORAS run must record, and be replayable from, the following:

1. **Model** - provider, model id, exact revision/hash, and decoding parameters (temperature, top-p, top-k, max
   tokens, stop sequences).
2. **Seeds** - random seeds for sampling and for any generator/mutation step.
3. **Prompts** - the complete prompt manifest, in order, with a content hash.
4. **Harness configuration** - the declarative config that produced the run (see [Harness](harness.md)).
5. **Data and retrieval** - dataset identifiers and versions, retrieval corpus snapshot or hash.
6. **Environment** - runtime, engine and version, hardware class, relevant environment variables.
7. **Faults and perturbations** - which were injected, with parameters and timing.
8. **Outputs** - raw model/tool outputs, timings, token counts, cost, and errors.

If a field is unknown, record it as unknown - do not silently omit it.

## Manifests and hashes

A run **manifest** is the contract made executable: a machine-readable file describing exactly what was run. Each
manifest carries content hashes so that two runs can be compared cheaply:

- `prompt_manifest_hash` - hash of the ordered prompt set.
- `config_hash` - hash of the resolved harness configuration.
- `dataset_hash` - hash of the data/retrieval snapshot.
- `environment_hash` - hash of the relevant runtime/environment fields.
- `run_id` - derived from the above, so identical configurations share a deterministic id.

Hashes make drift visible: if a "re-run" produces a different `config_hash`, it is not the same experiment.

## Behavior diffing across versions

Comparing two runs is the core analytical operation:

- **Paired replay** - run the same manifest against model A and model B (or harness v1 and v2) and align outputs by
  prompt id.
- **Behavioral diffs** - per-prompt changes in output, refusal, tool calls, latency, token use, and score.
- **Aggregate view** - distributions of change, not single examples; report effect sizes and confidence intervals.
- **Regression gate** - a diff that exceeds a configured threshold fails the gate and blocks promotion.

Store diffs as artifacts, not as prose, so they can be re-reviewed when the taxonomy or evaluator changes.

## Statistical replication

LLM behavior is stochastic. A single run is an anecdote:

- Run **n** repetitions per condition and report the distribution.
- Separate **sampling** noise from **configuration** effects.
- Use confidence intervals (e.g. bootstrap) on success and failure rates.
- Prefer paired comparisons (same prompts across conditions) to reduce variance.
- Report the n, the seed policy, and the aggregation rule alongside every number.

## Artifacts and provenance

A run directory should be self-describing:

```
runs/<run_id>/
  manifest.json          # the reproducibility contract
  config.resolved.yaml   # resolved harness configuration
  prompts.jsonl          # ordered prompt manifest (with hashes)
  outputs/               # raw model/tool outputs and timings
  faults.json            # injected perturbations and timing
  scores/                # evaluator outputs
  diff/                  # comparison against a baseline run, if any
  logs/                  # structured run logs
  README.md              # human-readable summary + provenance
```

Provenance links every derived number back to the run that produced it. Nothing is reported without a `run_id`.

## What this pillar is not

- It is **not** an offensive exploit pipeline.
- It is **not** a leaderboard.
- It is **not** about leaking model internals or weights.

It is an evidence discipline: same inputs, recorded conditions, comparable outputs.

## Related

- [Harness](harness.md) - the component that emits manifests and artifacts.
- [Chaos Engineering](chaos-engineering.md) - perturbations that must be recorded.
- [Red Teaming](red-teaming.md) - adversarial prompts that become regression cases.
- [Tools](tools.md) - the logging, manifest, and eval building blocks.

## Reading and references

- Association for Computing Machinery, *Artifact Review and Badging* (reproducibility terminology).
- Joelle Pineau et al., *Improving Reproducibility in Machine Learning Research* (NeurIPS checklist).
- *The Turing Way* - reproducible research practices.

## Pillars

| Pillar | Doc |
|---|---|
| Systems Thinking | [systems-thinking.md](systems-thinking.md) |
| Resilience Engineering | [resilience-engineering.md](resilience-engineering.md) |
| Chaos Engineering | [chaos-engineering.md](chaos-engineering.md) |
| Red Teaming | [red-teaming.md](red-teaming.md) |
| Reproducible Behavioral Analysis | this document |
| Harnesses (tools) | [tools.md](tools.md) |
| Custom Harness (goal) | [harness.md](harness.md) |
