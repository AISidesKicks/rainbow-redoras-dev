# Chaos Engineering

[< Back to README](../README.md) · [Pillars](#pillars)

Chaos engineering is the practice of deliberately perturbing a system to test whether your understanding of it is
correct. It is not random breakage: it is a controlled experiment with a hypothesis, a bounded blast radius, and an
abort condition.

Read this alongside [Resilience Engineering](resilience-engineering.md) (what we are trying to preserve) and
[Reproducible Behavioral Analysis](behavioral-analysis.md) (how we keep experiments comparable).

## Principles

1. **Start from a steady state.** Define the normal behavior you expect, in measurable terms, before touching
   anything.
2. **Hypothesize.** State what you believe will happen and what would falsify it.
3. **Vary real-world events.** Perturb the system with plausible faults, not just convenient ones.
4. **Run in production-like conditions.** A lab that does not resemble the real system teaches little - but
   production experiments require explicit authorization.
5. **Minimize blast radius.** Prefer the smallest perturbation that can falsify the hypothesis.
6. **Abort automatically.** If the steady state leaves its envelope, stop and restore.
7. **Learn and harden.** A failed experiment is a successful discovery; feed it back into the system.

## Experiment lifecycle

1. **Frame** - pick a behavior and a failure mode from the [resilience](resilience-engineering.md) catalogue.
2. **Baseline** - record the steady state over multiple runs (distributions, not a single sample).
3. **Hypothesize** - write the expected outcome and the abort condition.
4. **Authorize** - confirm ownership/authorization and the allowed blast radius - see
   [Governance](tools.md#governance).
5. **Inject** - apply one fault at a time, with a monitored envelope.
6. **Observe** - capture logs, manifests, and behavioral deltas.
7. **Abort or complete** - restore the system; verify recovery quality, not just availability.
8. **Analyze** - did the system match the hypothesis? Where did the mental model diverge?
9. **Harden** - turn the finding into a balancing loop (guardrail, fallback, eval gate) and re-run.

## Tabletop vs live

- **Tabletop** - walk through a scenario with people; cheap, safe, and good for designing experiments and finding
  blind spots.
- **Live** - actually inject the fault. More informative, requires authorization, isolation, and an abort path.

REDORAS defaults to tabletop-first and moves to live only with explicit authorization and a bounded blast radius.

## AI-specific fault injection

Infrastructure faults are well understood; behavioral faults are where AI systems differ:

- **Latency** - slow the model or a tool to provoke timeouts, retries, and cascades.
- **Truncated / overflowing context** - cut the context to test summarization, retrieval, and failure handling.
- **Token and rate limits** - force the rate-limit cliff and observe the fallback.
- **Tool failures** - return errors, empty results, or malformed payloads from tools.
- **Prompt perturbation** - reorder, paraphrase, or corrupt instructions and observe robustness.
- **Retrieval poisoning** - inject irrelevant, stale, or contradictory retrieved documents.
- **Model / version swaps** - silently change the target revision to detect regressions.
- **Budget exhaustion** - cap tokens or cost and verify graceful degradation.
- **Concurrency pressure** - increase parallel requests to expose shared-state and cache effects.

The scenario and fault generators that produce these belong to the harness - see [Harness](harness.md) and the
building blocks in [Tools](tools.md).

## Safety rails and authorization preconditions

Never run a live AI chaos experiment without all of the following:

- **Ownership or explicit authorization** for every target, including models and tools.
- **A defined blast radius** - what may be affected, and what must not be.
- **An abort condition** tied to the steady-state envelope.
- **Isolation** - fakes or sandboxed targets wherever possible.
- **Full logging** - inputs, config, seeds, and outputs captured in a manifest.
- **A rollback path** - a known-good configuration to restore.

See [Governance](tools.md#governance) for the full policy.

## Chaos vs red teaming

Chaos engineering injects **faults** into a running system to test its resilience. Red teaming generates
**adversarial behavior** to test coverage and find undesirable outputs. They are complementary: chaos tests the
plumbing, red teaming tests the behavior. See [Red Teaming](red-teaming.md).

## Related

- [Resilience Engineering](resilience-engineering.md) - the properties under test.
- [Red Teaming](red-teaming.md) - adversarial behavior generation.
- [Reproducible Behavioral Analysis](behavioral-analysis.md) - how experiments are recorded and compared.
- [Harness](harness.md) - where fault injectors live in the architecture.

## Reading and references

- *Principles of Chaos Engineering* - <https://principlesofchaos.org>
- Ali Basiri et al., "Chaos Engineering" (IEEE Software, 2016).
- Google SRE Book - testing for reliability and disaster exercises.

## Pillars

| Pillar | Doc |
|---|---|
| Systems Thinking | [systems-thinking.md](systems-thinking.md) |
| Resilience Engineering | [resilience-engineering.md](resilience-engineering.md) |
| Chaos Engineering | this document |
| Red Teaming | [red-teaming.md](red-teaming.md) |
| Reproducible Behavioral Analysis | [behavioral-analysis.md](behavioral-analysis.md) |
| Harnesses (tools) | [tools.md](tools.md) |
| Custom Harness (goal) | [harness.md](harness.md) |
