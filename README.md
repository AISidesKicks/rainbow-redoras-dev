# rainbow-redoras-dev

**AI needs REDORAS** - **R**apid **E**xploitation &amp; **D**eception **O**perations for **R**esilient **A**rchitecture &amp; **S**ystems.

An educational lab for studying AI systems *as systems*: how they behave, how they fail, and how to make them
resilient - using customizable harnesses and reproducible behavioral analysis.

Landing page: <https://rainbow.redoras.dev>

## What is REDORAS

REDORAS treats an AI system as a whole - **model + harness + infrastructure + data + evaluation loop** - and studies
its behavior with the tools of **systems thinking**, **resilience engineering**, **chaos engineering**,
**rainbow teaming**, and **reproducible behavioral analysis**.

The name is deliberately provocative. "Exploitation" and "deception" describe how systems are stressed and how they
can be made to mislead their operators. REDORAS looks for those behaviors under controlled, authorized conditions and
turns the findings into resilience work - it does not build offensive tooling.

## Why

The AI scene is changing fast. We see a lot of interesting research, but we do not have full access to the LLMs,
their harnesses, their setups, and the supporting tools and code. Without that access, behavior cannot be reproduced,
compared, or trusted.

REDORAS closes that gap by building its own lab: small, transparent, customizable, and fully instrumented. The point
is not to consume benchmarks, but to run experiments you can inspect end to end.

## Scope &amp; Ethics

REDORAS is **resilience and reproducible-behavioral-analysis research** on systems **you own or are authorized to
test**. It is **not** a dual-use offensive-security toolkit.

- Only test systems you own or have explicit authorization to test.
- Log and manifest every run so results are auditable and reproducible.
- Seek graceful degradation and recovery, never live-target harm.

Read the full policy in [Governance](docs/tools.md#governance).

## Pillars

| Pillar | What it covers |
|---|---|
| [Systems Thinking](docs/systems-thinking.md) | Boundaries, stocks and flows, feedback loops, delays, emergence, leverage points, mental models. |
| [Resilience Engineering](docs/resilience-engineering.md) | Resilience vs robustness, Safety-II, the four potentials, graceful degradation, SLOs and error budgets. |
| [Chaos Engineering](docs/chaos-engineering.md) | Steady-state hypotheses, blast radius, abort conditions, and AI-specific fault injection. |
| [Red Teaming](docs/red-teaming.md) | Rainbow teaming: open-ended generation of diverse adversarial prompts for coverage and resilience. |
| [Reproducible Behavioral Analysis](docs/behavioral-analysis.md) | The reproducibility contract, manifest hashes, behavior diffing, and statistical replication. |
| [Harnesses (tools)](docs/tools.md) | The inventory of building blocks used to compose a harness, grouped by function. |
| [Custom Harness (goal)](docs/harness.md) | The project's destination: a customizable harness specified in Markdown, built later. |

## The Goal: a Custom Harness

REDORAS is working toward **its own custom harness**: a declarative, pluggable, reproducible system for running
behavioral experiments against AI models. It composes model adapters, prompt and scenario generators, fault
injectors, an orchestrator, a deterministic logger, and an evaluator.

The design is specified in [docs/harness.md](docs/harness.md); harness **code is out of scope** for the current
planning phase. The building blocks it will compose are catalogued in [docs/tools.md](docs/tools.md).

## How to use this repo

- Start at the [landing page](docs/index.html) or this README.
- Read the [pillars](#pillars) in order for the conceptual grounding.
- Use [tools.md](docs/tools.md) as the inventory of harness building blocks.
- Use [harness.md](docs/harness.md) as the target design.
- Treat every experiment as a controlled, logged, authorized run.

## Inspirations

### Red Teaming

- <https://sites.google.com/view/rainbow-teaming>
- <https://arxiv.org/abs/2402.16822> - *Rainbow Teaming: Open-Ended Generation of Diverse Adversarial Prompts*
- <https://www.youtube.com/watch?v=4hi9JavKrkc> - *Building a Vulnerability Discovery Harness* with Dan Jones,
  Red Team Engineer @ Cloudflare

## Related projects

- [AISidesKicks](https://github.com/AISidesKicks) -
  the umbrella home for sibling EDU labs on metering and billing AI tokens for local inference.

Maintained by [AISidesKicks](https://aha.aisideskicks.fyi/).
