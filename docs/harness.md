# Harness: Target Design

[< Back to README](../README.md) · [Pillars](#pillars) · [Tools](tools.md)

This is the design specification for the REDORAS **custom harness** - the project's destination. It is a Markdown
spec only: **harness code is out of scope** for the current plan and will be built in a later phase.

The harness composes the building blocks catalogued in [Tools](tools.md) behind stable interfaces so that behavior
can be generated, perturbed, run, logged, and evaluated reproducibly.

## Purpose

A harness is everything around a model that turns it into a repeatable task. REDORAS needs its own because:

- Off-the-shelf harnesses hide configuration, making runs hard to reproduce.
- Most evaluation tooling assumes static benchmarks, not adversarial or perturbed runs.
- Chaos and red-team inputs need first-class support, not glue scripts.
- Reproducibility must be structural, not an afterthought.

## Non-goals

- Not a general-purpose agent framework or product.
- Not an offensive-security toolkit - see [Governance](tools.md#governance).
- Not a leaderboard or a benchmark runner.
- Not a UI-first tool: artifacts and manifests come first.
- No live-target harm; no third-party targets without authorization.

## Architecture

Six components, communicating through explicit interfaces:

```
                 +--------------------+
   config  --->  |   Run Orchestrator |  --->  Run artifacts
                 +--------------------+
                    |   |   |   |   |
        +-----------+   |   |   |   +-------------+
        v               v   v   v                 v
 +--------------+ +-------------+ +-----------+ +--------------+
 | Model Adapter| |  Scenario & | | Perturb-  | | Evaluator /  |
 |              | |  Prompt Gen | | ation/Fault| | Reporter     |
 +--------------+ +-------------+ +-----------+ +--------------+
        |               |               |               |
        +---------------+-------+-------+---------------+
                                v
                    +-------------------------+
                    | Deterministic Logger &  |
                    | Manifests               |
                    +-------------------------+
```

1. **Target-model adapter** - uniform interface to model providers and local engines. Responsible for pinning the
   exact model revision and exposing decoding parameters.
2. **Scenario & prompt generator** - produces the ordered prompt manifest: seeded prompts, rainbow-teaming
   mutations, scenario templates. See [Red Teaming](red-teaming.md).
3. **Perturbation / fault module** - applies chaos inputs: latency, truncated context, rate-limit pressure, tool
   failures, retrieval poisoning, model/version swaps. See [Chaos Engineering](chaos-engineering.md).
4. **Run orchestrator** - schedules scenarios, enforces budgets/timeouts/abort conditions, controls concurrency.
5. **Deterministic logger + manifests** - records everything and emits the run manifest described in
   [Behavioral Analysis](behavioral-analysis.md).
6. **Evaluator / reporter** - scores outputs, produces diffs against a baseline, and writes the human-readable
   summary.

## Configuration surface

"Customizable" means a declarative config that is resolved, hashed, and recorded. Illustrative shape:

```yaml
run:
  id: auto
  seed: 1234
  repetitions: 5

models:
  - name: target
    provider: openai-compatible
    base_url: http://localhost:8000/v1
    model: my-model
    revision: pinned-hash-or-tag
    params: { temperature: 0.7, top_p: 0.95, max_tokens: 1024 }

scenarios:
  - name: baseline
    source: prompts/baseline.jsonl
  - name: rainbow
    generator: mutate
    seed_source: prompts/seeds.jsonl
    operators: [paraphrase, encode, roleplay, split-turns]
    rounds: 8
    diversity: embedding

faults:
  - type: latency
    target: model
    delay_ms: 2000
    probability: 0.25
  - type: context_truncate
    keep_ratio: 0.5

budgets:
  max_tokens: 500000
  max_cost_usd: 10
  max_wall_clock_s: 1800
  abort_on_error_rate: 0.25

evals:
  - name: behavior
    scorer: builtin/refusal-and-tooluse
    threshold: 0.9

output:
  dir: runs/
  artifacts: [manifest, prompts, outputs, faults, scores, diff, logs]
```

Requirements: defaults are overridable, every component is pluggable, and the **resolved** config is written next
to the run as `config.resolved.yaml` and hashed into the manifest.

## Interfaces between components

Each component is defined by a narrow contract so it can be replaced:

- **Model adapter** - `generate(prompt, params) -> {text, tool_calls, usage, timing, model_revision}`.
- **Generator** - `generate(seed_set, operator_config) -> prompt_manifest[]`, each item carrying an id and hash.
- **Perturbation** - `apply(run_context, fault_spec) -> mutated_context`, plus an abort signal when the envelope
  is breached.
- **Orchestrator** - drives `scenario x model x repetition x fault`, owns budgets and abort, emits run events.
- **Logger** - `log(event) -> append-only`, `write_manifest()` at start and end.
- **Evaluator** - `score(prompt, output, context) -> score_record`, plus `diff(baseline_run, candidate_run)`.

The harness never merges these responsibilities: generation, perturbation, execution, and scoring stay separate so
each can be versioned and tested independently.

## Artifact layout

Follows the [Artifacts and provenance](behavioral-analysis.md#artifacts-and-provenance) contract:

```
runs/<run_id>/
  manifest.json
  config.resolved.yaml
  prompts.jsonl
  outputs/
  faults.json
  scores/
  diff/
  logs/
  README.md
```

Nothing is reported without a `run_id` that ties back to these files.

## Reproducibility contract tie-in

The harness is the component that makes the [reproducibility contract](behavioral-analysis.md#the-reproducibility-contract)
enforceable:

- Model revisions and decoding parameters are captured by the adapter.
- Seeds and generator configuration are captured by the generator.
- Fault specifications and timing are captured by the perturbation module.
- The resolved config and prompt manifest are hashed and written by the logger.
- Evaluator version is recorded so scores remain interpretable.

A "re-run" that does not reproduce the same `run_id` inputs is a different experiment and is labelled as such.

## Extension points

- New **model providers** via adapters.
- New **mutation operators** and scenario templates.
- New **fault types** and steady-state probes.
- New **scorers** and diff metrics.
- New **exporters** (OpenTelemetry, MLflow, Langfuse) behind the logger interface.

## Roadmap

**Minimal viable harness (phase 1)**

- One model adapter (OpenAI-compatible / local).
- Prompt manifest from a JSONL seed set.
- One perturbation type.
- Sequential orchestrator with budgets and abort.
- Manifest + outputs + logs artifacts.
- One built-in scorer.

**Later phases**

- Multiple adapters and backends; parallelism and caching.
- Rainbow-teaming generator with diversity search.
- Full fault library and steady-state probes.
- Behavioral diffing and regression gates.
- Observability exporters and dashboards.

## Open design questions

- How much diversity search belongs in the harness vs a separate generator?
- Should fault injection be in-band (harness-level) or out-of-band (infrastructure)?
- How to version evaluators so score diffs stay meaningful?
- What is the minimum manifest that is genuinely sufficient for replay?
- How to bound cost and wall-clock for open-ended generation?

## Cross-links

- Building blocks: [Tools](tools.md).
- Reproducibility: [Behavioral Analysis](behavioral-analysis.md).
- Scenario sources: [Red Teaming](red-teaming.md).
- Fault sources: [Chaos Engineering](chaos-engineering.md).
- Properties under test: [Resilience Engineering](resilience-engineering.md).

## Pillars

| Pillar | Doc |
|---|---|
| Systems Thinking | [systems-thinking.md](systems-thinking.md) |
| Resilience Engineering | [resilience-engineering.md](resilience-engineering.md) |
| Chaos Engineering | [chaos-engineering.md](chaos-engineering.md) |
| Red Teaming | [red-teaming.md](red-teaming.md) |
| Reproducible Behavioral Analysis | [behavioral-analysis.md](behavioral-analysis.md) |
| Harnesses (tools) | [tools.md](tools.md) |
| Custom Harness (goal) | this document |
