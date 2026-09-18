# Systems Thinking

[< Back to README](../README.md) · [Pillars](#pillars)

Systems thinking is the discipline of understanding a whole by looking at its parts, their relationships, and the
way the whole behaves over time - instead of reading behavior off a single component. It is the foundation every
other REDORAS pillar rests on.

## The system is not the model

It is tempting to explain an AI outcome by pointing at the model. In practice, behavior emerges from an interacting
stack:

- **Model** - weights, revision, architecture, decoding parameters.
- **Harness** - the scaffolding that turns a model into a task: prompts, tools, control flow, retries, memory.
- **Infrastructure** - serving engine, latency, rate limits, caching, hardware.
- **Data** - training, retrieval corpus, examples, tool payloads.
- **Evaluation loop** - what is measured, by whom, and how results feed back into the system.

A change in any layer can change the observed behavior. This is why REDORAS insists on a whole-system view: the
**evaluation loop** is part of the system, not an external referee.

## Boundaries

A **system boundary** is a decision, not a discovery. It determines what counts as inside, what counts as
environment, and where you draw the line when attributing behavior. Draw it too narrowly and you miss the cause;
draw it too widely and you cannot run a controlled experiment.

For each experiment, state the boundary explicitly: which model revision, which harness version, which datasets,
which infrastructure, which observers. Boundaries can be redrawn between experiments, but not silently within one.

## Stocks and flows

AI systems accumulate and spend resources over time:

- **Stocks** - context window contents, KV cache, conversation history, retrieved documents, GPU memory, budget.
- **Flows** - tokens generated, requests admitted, documents retrieved, cache hits and misses, errors emitted.

Behavior is often a *stock* phenomenon (context rot, memory pressure) rather than an instantaneous property. Reading
only the snapshot at the end hides the trajectory that produced it.

## Feedback loops

- **Reinforcing loops** amplify change: an agent that retries a failing tool call can generate more failures and
  more retries; a model that hallucinates a fact can retrieve it back as grounding.
- **Balancing loops** resist change: rate limiters, circuit breakers, retry budgets, human review gates, evals that
  block a release.

Resilience work is largely about making balancing loops *visible, fast, and well-placed* - and keeping reinforcing
loops inside a safe envelope. See [Resilience Engineering](resilience-engineering.md).

## Delays

Almost every interesting signal in an AI system is delayed: retrieval latency, cache warm-up, model rollout lag,
evaluation turnaround, the time between a prompt change and its behavioral effect. Delays cause oscillation and
overshoot - teams tune a prompt, wait, see a noisy result, and overcorrect.

Reproducible manifests and versioned artifacts exist to make delayed effects *comparable* instead of guessed. See
[Reproducible Behavioral Analysis](behavioral-analysis.md).

## Emergence

Behavior that is not present in any component can appear when components interact: tool-call cascades, prompt
injection through retrieved content, conversation drift, unexpected persona shifts. Emergence is the main reason a
component-level test suite is not enough.

## Leverage points

Donella Meadows' leverage points apply directly:

- **Parameters** (temperature, top-p, timeouts) are easy to change and rarely transformative.
- **Feedback** (adding an eval gate, a monitor, a rollback) changes behavior durably.
- **Rules and information flows** (what the harness exposes, what is logged, what can be overridden) change what
  the system can do at all.
- **Goals and paradigms** (what the lab is optimizing for) change everything upstream.

The REDORAS custom harness is deliberately aimed at the higher leverage points: making rules explicit,
information flows observable, and defaults overridable. See [Harness](harness.md).

## Mental models

Every operator carries a mental model of the system - usually incomplete, usually unstated. Chaos and red-team
experiments are, among other things, a way to surface where the mental model and the observed system diverge.
The finding is not "the model failed"; it is "our model of the system was wrong here."

## Why behavior cannot be read off one component

- The same model under two harnesses can behave differently.
- The same harness against two model revisions can behave differently.
- The same run at two times can behave differently because the environment moved.
- The observer changes the system: what you log, sample, and gate changes what you see and ship.

Because of this, REDORAS treats every claim about behavior as conditional on a **fully specified run**.

## Reading and references

- Donella Meadows, *Thinking in Systems: A Primer*.
- Peter Senge, *The Fifth Discipline* (mental models, leverage).
- John Sterman, *Business Dynamics* (stocks, flows, delays).
- Nancy Leveson, *Engineering a Safer World* (systems-theoretic accident models; STAMP/STPA).
- [Resilience Engineering](resilience-engineering.md) - how systems absorb and recover from perturbation.
- [Chaos Engineering](chaos-engineering.md) - how to perturb a system to test your understanding of it.

## Pillars

| Pillar | Doc |
|---|---|
| Systems Thinking | this document |
| Resilience Engineering | [resilience-engineering.md](resilience-engineering.md) |
| Chaos Engineering | [chaos-engineering.md](chaos-engineering.md) |
| Red Teaming | [red-teaming.md](red-teaming.md) |
| Reproducible Behavioral Analysis | [behavioral-analysis.md](behavioral-analysis.md) |
| Harnesses (tools) | [tools.md](tools.md) |
| Custom Harness (goal) | [harness.md](harness.md) |
