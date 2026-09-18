# Tools: Harness Building Blocks

[< Back to README](../README.md) · [Pillars](#pillars)

REDORAS treats tools as **building blocks for a harness**, not as ends in themselves. A harness is the scaffolding
that turns a model into a repeatable task: it wires prompts, tools, faults, orchestration, logging, and evaluation
into one runnable experiment.

This document is the inventory of blocks. The target design that composes them is [Harness](harness.md).

## Governance

**Scope and ethics.** REDORAS is **resilience and reproducible-behavioral-analysis research** on systems **you own
or are authorized to test**. It is **not** a dual-use offensive-security toolkit, and it is not a pipeline for
attacking third-party or live targets.

Requirements for every experiment:

- **Authorization** - only systems you own, or for which you hold explicit, documented permission.
- **Owned / permitted targets only** - no third-party production systems, no unconsented users.
- **Logging** - every run records inputs, configuration, seeds, and outputs (see
  [Behavioral Analysis](behavioral-analysis.md)).
- **Reproducibility** - a run must be replayable from its manifest; unrecorded experiments do not count as evidence.
- **No live-target harm** - no destructive actions, no data exfiltration, no resource exhaustion outside the agreed
  blast radius.
- **Not dual-use security tooling** - nothing here should be repurposed as an attack kit.

If a proposed experiment cannot satisfy all of the above, do not run it. For the resilience framing that motivates
these constraints, see [Resilience Engineering](resilience-engineering.md).

## How to read this inventory

Each block lists **what it does**, **what is customizable**, **which pillar it serves**, and a **link**. Items marked
**(candidate)** are strong candidates to fold into the [custom harness](harness.md). Prefer open, inspectable, and
override-friendly options.

## 1. Model / engine adapters

Turn a target model into a uniform interface the harness can call.

| Block | What it does | Customizable | Serves | Link |
|---|---|---|---|---|
| LiteLLM **(candidate)** | OpenAI-compatible gateway over many providers and local engines | providers, routing, caching, budgets | Harness | <https://github.com/BerriAI/litellm> |
| Ollama | Local model runner with a simple API | models, quantization, sampling | Harness | <https://github.com/ollama/ollama> |
| llama.cpp | Portable CPU/GPU inference | quantization, context, sampling | Harness | <https://github.com/ggml-org/llama.cpp> |
| vLLM | High-throughput GPU serving | batching, parallelism, sampling | Resilience | <https://github.com/vllm-project/vllm> |
| SGLang | Structured-generation serving | runtime, batching, constrained decoding | Resilience | <https://github.com/sgl-project/sglang> |
| Transformers | Direct model access for research | tokenizer, revision, device, decoding | Behavioral Analysis | <https://github.com/huggingface/transformers> |

The adapter layer must expose the **exact model revision** so runs can be pinned and diffed.

## 2. Prompt and scenario generators

Produce the inputs: from simple prompt sets to open-ended adversarial corpora.

| Block | What it does | Customizable | Serves | Link |
|---|---|---|---|---|
| garak **(candidate)** | LLM vulnerability/behavior probes and detectors | probe sets, generators, detectors | Red Teaming | <https://github.com/NVIDIA/garak> |
| PyRIT **(candidate)** | Risk-identification framework for generative AI | orchestrators, converters, scorers | Red Teaming | <https://github.com/Azure/PyRIT> |
| promptfoo **(candidate)** | Prompt/eval matrices with assertions | providers, prompts, test suites | Behavioral Analysis | <https://github.com/promptfoo/promptfoo> |
| TextAttack | Adversarial text generation and mutation | transformations, constraints, search | Red Teaming | <https://github.com/QData/TextAttack> |

Generator configs (seeds, mutation operators, diversity objectives) are part of the run manifest. Diversity and
coverage matter more than raw success rate - see [Red Teaming](red-teaming.md).

## 3. Fault and chaos injectors

Perturb the system to test its steady state.

| Block | What it does | Customizable | Serves | Link |
|---|---|---|---|---|
| Chaos Mesh | Kubernetes-level fault injection | faults, scope, schedule, duration | Chaos Engineering | <https://github.com/chaos-mesh/chaos-mesh> |
| Toxiproxy **(candidate)** | Network fault proxy (latency, limits, cuts) | toxics, endpoints, timing | Chaos Engineering | <https://github.com/Shopify/toxiproxy> |
| Pumba | Container-level chaos (kill, pause, netem) | targets, actions, intervals | Chaos Engineering | <https://github.com/alexei-led/pumba> |
| LitmusChaos | Chaos workflows and experiments | experiments, probes, workflows | Chaos Engineering | <https://github.com/litmuschaos/litmus> |

Behavioral faults (context truncation, prompt perturbation, retrieval poisoning, model swaps) are usually authored
as harness modules rather than infrastructure tooling. See [Chaos Engineering](chaos-engineering.md).

## 4. Runners and orchestration

Execute scenarios, manage budgets, and control concurrency.

| Block | What it does | Customizable | Serves | Link |
|---|---|---|---|---|
| Inspect AI **(candidate)** | Evaluation framework with solvers, tools, and scorers | tasks, solvers, tools, scorers | Behavioral Analysis | <https://github.com/UKGovernmentBEIS/inspect_ai> |
| lm-evaluation-harness | Standardized model evaluation tasks | tasks, model backends, few-shot | Behavioral Analysis | <https://github.com/EleutherAI/lm-evaluation-harness> |
| LangGraph | Graph-based agent/flow orchestration | nodes, edges, state, retries | Harness | <https://github.com/langchain-ai/langgraph> |
| promptfoo | Matrixed runs with parallelism and caching | runs, concurrency, cache | Behavioral Analysis | <https://github.com/promptfoo/promptfoo> |

The orchestrator owns rate limits, timeouts, retries, and abort conditions - the same controls a chaos experiment
depends on. See [Chaos Engineering](chaos-engineering.md).

## 5. Logging, manifests and provenance

Make runs auditable and comparable.

| Block | What it does | Customizable | Serves | Link |
|---|---|---|---|---|
| OpenTelemetry **(candidate)** | Standard traces, metrics, and logs | spans, attributes, exporters | Behavioral Analysis | <https://opentelemetry.io> |
| MLflow | Experiment tracking and artifacts | runs, params, metrics, artifacts | Behavioral Analysis | <https://github.com/mlflow/mlflow> |
| DVC | Data and artifact versioning | remotes, pipelines, hashing | Behavioral Analysis | <https://github.com/iterative/dvc> |
| Git | Immutable provenance for configs and prompts | branches, commits, tags | Behavioral Analysis | <https://git-scm.com> |

Every run must emit a manifest with content hashes; this is the backbone of the
[reproducibility contract](behavioral-analysis.md#the-reproducibility-contract).

## 6. Evaluations and observability

Score outputs, watch behavior over time, and surface regressions.

| Block | What it does | Customizable | Serves | Link |
|---|---|---|---|---|
| Arize Phoenix **(candidate)** | LLM tracing and evaluation UI | datasets, evaluators, dashboards | Behavioral Analysis | <https://github.com/Arize-ai/phoenix> |
| Langfuse **(candidate)** | Tracing, prompt management, and evals | traces, scores, datasets | Behavioral Analysis | <https://github.com/langfuse/langfuse> |
| DeepEval | Unit-test-style LLM metrics | metrics, thresholds, datasets | Behavioral Analysis | <https://github.com/confident-ai/deepeval> |
| Ragas | Retrieval-augmented generation evaluation | metrics, datasets, models | Resilience | <https://github.com/explodinggradients/ragas> |
| OpenLLMetry | OpenTelemetry instrumentation for LLM apps | instrumentors, exporters | Behavioral Analysis | <https://github.com/traceloop/openllmetry> |

Evaluators must be versioned alongside prompts and models, or diffs become meaningless. See
[Behavioral Analysis](behavioral-analysis.md).

## From blocks to harness

These blocks are candidates, not commitments. The [custom harness](harness.md) selects and composes the minimal set,
wraps each behind a stable interface, and makes configuration declarative and overridable.

Read next: [Harness](harness.md) - the target design; [Behavioral Analysis](behavioral-analysis.md) - what every
run must record.

## Related

- [Harness](harness.md) - the target architecture these blocks compose.
- [Behavioral Analysis](behavioral-analysis.md) - the reproducibility contract.
- [Chaos Engineering](chaos-engineering.md) - fault injectors in context.
- [Red Teaming](red-teaming.md) - scenario and prompt generators in context.

## Pillars

| Pillar | Doc |
|---|---|
| Systems Thinking | [systems-thinking.md](systems-thinking.md) |
| Resilience Engineering | [resilience-engineering.md](resilience-engineering.md) |
| Chaos Engineering | [chaos-engineering.md](chaos-engineering.md) |
| Red Teaming | [red-teaming.md](red-teaming.md) |
| Reproducible Behavioral Analysis | [behavioral-analysis.md](behavioral-analysis.md) |
| Harnesses (tools) | this document |
| Custom Harness (goal) | [harness.md](harness.md) |
