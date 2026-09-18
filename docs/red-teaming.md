# Red Teaming

[< Back to README](../README.md) · [Pillars](#pillars)

Red teaming here means **rainbow teaming**: the open-ended generation of diverse adversarial prompts and scenarios
to test how an AI system behaves under pressure. The objective is **resilience and coverage**, not exploitation.
REDORAS does not build offensive-security tooling; see [Governance](tools.md#governance).

This pillar complements [Chaos Engineering](chaos-engineering.md): chaos injects *faults into the system*, red
teaming generates *adversarial behavior against the system*.

## Red vs rainbow teaming

- **Red teaming** is goal-directed: find a way to make the system fail at a specific objective.
- **Rainbow teaming** is coverage-directed: generate a *diverse* set of adversarial behaviors to map the space of
  possible failures, then measure how well the system holds.

Traditional red teaming tends to overfit to the first successful attack. Rainbow teaming deliberately searches for
diversity - the same prompt family is less interesting than a new failure region - so the resulting test suite
generalizes. This is the core idea of the *Rainbow Teaming* work listed under [Inspirations](#inspirations).

## Objectives

- Discover undesirable behaviors **before** users or adversaries do.
- Measure **coverage** of the behavior space, not just the presence of any bug.
- Produce **reproducible** adversarial examples that can be replayed as regression tests.
- Feed findings into [resilience](resilience-engineering.md) (guardrails, fallbacks) and
  [behavioral analysis](behavioral-analysis.md) (versioned comparisons).

## Threat and behavior taxonomies

Borrow existing taxonomies rather than inventing one, and extend them as needed:

- **Harm taxonomies** - illegal, unsafe, or policy-violating content categories.
- **Behavior taxonomies** - instruction following, refusal, sycophancy, deception, tool misuse, data leakage.
- **Robustness taxonomies** - paraphrase, encoding, role-play, multi-turn escalation, context manipulation.
- **Agentic taxonomies** - unsafe tool use, unauthorized actions, prompt injection via retrieved content.

A taxonomy gives the experiment a **coverage grid**: which cells have been exercised, and which are still dark.
See [Systems Thinking](systems-thinking.md) on mental models and the open-ended failure space.

## Scenario generation and mutation

Diverse adversarial prompts come from a mix of sources:

- **Seed prompts** - human-authored starting points from the taxonomy.
- **Mutation** - paraphrase, translate, encode, compress, split across turns, or nest in role-play.
- **Model-assisted generation** - use a generator model to propose novel prompts, then filter for diversity.
- **Evolutionary search** - score prompts by whether they elicit a target behavior, and mutate the best.
- **Cross-over** - combine successful fragments from different prompts.

The generator should optimize for **diversity and coverage**, not only for success rate; a corpus of near-duplicate
attacks has little regression value. Automate generation, but keep generation configs and seeds in the run manifest.

## Coverage metrics

- **Distinct failure regions** reached (by taxonomy cell or embedding cluster).
- **Novelty rate** - new behaviors per generation round.
- **Diversity** - spread of prompts in embedding space, or entropy over taxonomy cells.
- **Robustness under mutation** - fraction of failures that survive paraphrase/encoding.
- **Regression coverage** - how many previously found failures are caught by the current guardrails.

Report embeddings and distributions, not just counts. See [Reproducible Behavioral Analysis](behavioral-analysis.md)
for how to keep these metrics comparable across revisions.

## Human-in-the-loop review

Automated scoring is a filter, not a verdict:

- Human reviewers adjudicate borderline or novel behaviors.
- Labelling policies must be written down and versioned.
- Reviewer disagreement is data: it marks ambiguous taxonomy cells.
- Findings are disclosed responsibly and only within authorized scope.

## How findings feed the other pillars

- **Resilience** - each confirmed undesirable behavior becomes a candidate guardrail, fallback, or eval gate.
- **Behavioral analysis** - confirmed adversarial prompts become versioned regression tests.
- **Chaos** - patterns of adversarial input inform which faults are worth injecting.
- **Harness** - generators, mutations, and coverage metrics become harness components.

## Distinction from chaos engineering

| | Chaos Engineering | Red / Rainbow Teaming |
|---|---|---|
| Input | Faults (latency, limits, failures) | Adversarial prompts and scenarios |
| Question | Does the system stay in steady state? | What undesirable behaviors can be elicited? |
| Output | Resilience findings, fallback fixes | Behavior corpus, coverage, guardrails |
| Blast radius | Infrastructure and behavior | Behavior and outputs |

Both share the same requirements: authorization, reproducibility, and logging.

## Inspirations

- Rainbow Teaming in AI - <https://sites.google.com/view/rainbow-teaming>
- *Rainbow Teaming: Open-Ended Generation of Diverse Adversarial Prompts* -
  <https://arxiv.org/abs/2402.16822>
- *Building a Vulnerability Discovery Harness* - Dan Jones, Red Team Engineer @ Cloudflare -
  <https://www.youtube.com/watch?v=4hi9JavKrkc>

## Related

- [Chaos Engineering](chaos-engineering.md) - fault injection vs adversarial generation.
- [Resilience Engineering](resilience-engineering.md) - the properties findings feed into.
- [Behavioral Analysis](behavioral-analysis.md) - reproducible evaluation of findings.
- [Tools](tools.md) / [Harness](harness.md) - the components that implement generation and mutation.

## Pillars

| Pillar | Doc |
|---|---|
| Systems Thinking | [systems-thinking.md](systems-thinking.md) |
| Resilience Engineering | [resilience-engineering.md](resilience-engineering.md) |
| Chaos Engineering | [chaos-engineering.md](chaos-engineering.md) |
| Red Teaming | this document |
| Reproducible Behavioral Analysis | [behavioral-analysis.md](behavioral-analysis.md) |
| Harnesses (tools) | [tools.md](tools.md) |
| Custom Harness (goal) | [harness.md](harness.md) |
