# Resilience Engineering

[< Back to README](../README.md) · [Pillars](#pillars)

Resilience engineering asks not only "how do we prevent failure?" but "how do we keep succeeding when the
unexpected happens?" For AI systems - which are probabilistic, fast-moving, and embedded in tool-using harnesses -
this second question is the one that decides whether they survive contact with reality.

See also [Systems Thinking](systems-thinking.md) for the whole-system view and [Chaos Engineering](chaos-engineering.md)
for how resilience is exercised.

## Resilience is not robustness

- **Robustness** is resisting perturbation: the system does not change when pushed.
- **Resilience** is absorbing, adapting to, and recovering from perturbation: the system bends, degrades, and comes
  back - possibly as a better version.

A system can be robust to the failures it anticipated and brittle to everything else. REDORAS optimizes for
resilience because the failure space of AI systems is open-ended.

## Safety-I and Safety-II

- **Safety-I** explains safety as the absence of adverse events: count incidents, find causes, add barriers.
- **Safety-II** explains safety as the presence of the capacity to succeed under varying conditions: study normal
  work, understand how success is produced, and strengthen that capacity.

AI systems produce novel behavior constantly, so a purely incident-driven Safety-I posture cannot keep up.
REDORAS adopts Safety-II: define what "good" looks like across many conditions, then test whether the system can
produce it - not just whether it avoids the known bad.

## The four potentials

Erik Hollnagel's four potentials are a practical checklist for resilience:

1. **Respond** - know when something is wrong and act before it escalates.
2. **Monitor** - observe the system and its environment, including the things that are hard to see.
3. **Anticipate** - reason about what could happen next, including low-probability, high-impact risks.
4. **Learn** - turn experience into durable change in the system and the organization.

For an AI system, each potential needs instrumentation:

- **Reserve** - headroom in context, tokens, rate limits, compute, and budget.
- **Flexibility** - the ability to reroute: fallback models, degraded modes, tool substitutes.
- **Margin** - deliberate slack between normal operation and the limit.
- **Tolerance** - the ability to accept and contain partial failure without collapse.

## Graceful degradation

When a resource or dependency is lost, the system should fail *down*, not *off*:

- Serve a smaller model or a cached answer instead of erroring.
- Reduce retrieval depth rather than blocking.
- Drop optional tools and continue in a constrained mode.
- Surface uncertainty and provenance instead of silently guessing.

Design target: every degraded mode is an explicit, testable state - not an accident. Chaos experiments should verify
that each one is reachable and bounded.

## SLOs and error budgets

Resilience needs a definition of success that is measurable over time:

- **SLI** - the indicator (e.g. task success rate, p95 latency, grounded-answer rate).
- **SLO** - the target for that indicator over a window.
- **Error budget** - the allowed distance from the target; when spent, reliability work takes priority over new
  behavior.

For behavioral systems, SLIs must be **behavioral**, not just infrastructural: "answers with valid citation 99% of
the time on the eval set" is more useful than "HTTP 200". Because behavior depends on model revision and harness
configuration, every SLI is only meaningful together with its run manifest - see
[Reproducible Behavioral Analysis](behavioral-analysis.md).

## AI-specific failure modes

- **Drift** - data, prompt, or environment changes so the old behavior no longer applies.
- **Hallucination** - confident output unsupported by evidence, including fabricated citations.
- **Context rot** - quality degrades as context fills with stale or irrelevant material.
- **Tool/agent cascades** - a failed tool call triggers retries that amplify failure.
- **Rate-limit and timeout cliffs** - smooth behavior turns discontinuous at a threshold.
- **Model/version swaps** - silent regressions when the provider changes the target.
- **Prompt injection via retrieved content** - untrusted data becomes instructions.

Each of these is a candidate steady-state violation and a candidate chaos experiment.

## What to measure

- **MTTD / MTTD-Response** - how fast a deviation is detected and acknowledged.
- **MTTR** - time to restore the target behavior.
- **Recovery quality** - is the post-recovery system actually correct, or merely up?
- **Degraded-mode quality** - how much behavior is preserved in each fallback state.
- **Containment** - did the perturbation stay within the blast radius?
- **Regression surface** - how much behavior changed between model/harness revisions.

Measure distributions, not anecdotes: replay runs and compare with
[behavioral analysis](behavioral-analysis.md).

## Related

- [Systems Thinking](systems-thinking.md) - feedback, delays, leverage.
- [Chaos Engineering](chaos-engineering.md) - how to exercise these potentials safely.
- [Red Teaming](red-teaming.md) - adversarial behavior generation for coverage.
- [Harness](harness.md) - the tool that runs the experiments.

## Reading and references

- Erik Hollnagel, David Woods, Nancy Leveson, *Resilience Engineering: Concepts and Precepts*.
- Erik Hollnagel, *Safety-I and Safety-II: The Past and Future of Safety Management*.
- Sidney Dekker, *Drift into Failure*.
- Google SRE Book - service level objectives and error budgets.

## Pillars

| Pillar | Doc |
|---|---|
| Systems Thinking | [systems-thinking.md](systems-thinking.md) |
| Resilience Engineering | this document |
| Chaos Engineering | [chaos-engineering.md](chaos-engineering.md) |
| Red Teaming | [red-teaming.md](red-teaming.md) |
| Reproducible Behavioral Analysis | [behavioral-analysis.md](behavioral-analysis.md) |
| Harnesses (tools) | [tools.md](tools.md) |
| Custom Harness (goal) | [harness.md](harness.md) |
