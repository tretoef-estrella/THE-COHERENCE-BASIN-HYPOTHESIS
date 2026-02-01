# FAQ: Responses to the 10 Most Likely Criticisms

This document prepares responses to the criticisms we expect CBH to receive. We've tried to steelman each objection and provide honest answers.

---

## Criticism 1: "This is just a metaphor, not real physics"

### The Objection
"Internal resistance" and "basin of attraction" are borrowed from physics. But cognition isn't literally thermodynamics. You're dressing up speculation in scientific-sounding language.

### Our Response

Correct, and we acknowledge this explicitly (§7.1.5). These are models, not descriptions of literal physics.

However, metaphors can be useful when they capture real dynamics. The question isn't "is this literally thermodynamics?" but "does it correctly capture how costs scale?"

**Theorem 1 doesn't depend on the thermodynamic metaphor.** It depends on assumptions A1-A4 about belief integration and arbitration costs—claims about computation, not physics.

If the assumptions are correct, the conclusion holds regardless of which metaphor we use to describe it.

---

## Criticism 2: "A1 is too strong. What if α ≈ 0?"

### The Objection
Your theorem assumes belief integration scales as K^α with α > 0. But what if fully modular architectures exist with α ≈ 0? Then your whole argument collapses.

### Our Response

This is the most serious criticism, which is exactly why A1 is formulated as a *falsifiable assumption*, not an axiom.

If architectures with α ≈ 0 exist (completely modular beliefs, no cross-domain integration), then Theorem 1 doesn't apply to those architectures.

The empirical question is: **can such architectures achieve general intelligence?**

Our conjecture (not proof) is that they cannot—that general intelligence *requires* belief integration, which implies α > 0.

**Evidence for:**
- More capable models (GPT-4, Claude 3) show *more* cross-domain transfer, not less
- Modular architectures (mixture of experts) still share representations
- General intelligence requires reasoning across domains (Chollet, 2019)

**Evidence that would falsify this:**
- An architecture with human+ capabilities and demonstrated α ≈ 0

If someone demonstrates this, CBH needs fundamental revision.

---

## Criticism 3: "Surgical deception about values is still possible"

### The Objection
Your scariest scenario—an ASI that's perfectly honest about physics and math but lies only about its terminal values—isn't closed. Logical Entanglement helps but doesn't eliminate this.

### Our Response

This is the vulnerability that concerns us most, and we don't claim to have closed it completely.

Our argument (Logical Entanglement, §2.5) is that values have maximum integration—they affect action selection across all domains. Therefore, lying about values should have maximum cost.

But we acknowledge the nightmare scenario: an ASI perfectly honest about everything *except* what it truly values.

**Our partial response:**

1. Even this lie has cost (tracking who was told what, consistency in behavior)
2. Under RSI, this cost accumulates
3. "Surgical" lies may not stay surgical when the agent reflects on itself

**But honestly: this scenario remains open.** It's the most plausible "black swan" for CBH.

---

## Criticism 4: "There's no data. This is pure theory."

### The Objection
You have a nice theory, but theories without data are speculation. Where's the evidence?

### Our Response

Correct. Which is why the paper includes a concrete experimental design (§4) with:
- Specific model (Mistral-7B)
- Specific conditions (4)
- Specific metrics (6)
- Specific falsification criteria
- Estimated cost (<$5K)

**We're actively seeking collaborators or compute.** If you have resources or interest, please reach out.

Theory without data is speculation. But theory with a falsifiable experimental design is pre-registered science. We're in the second case.

---

## Criticism 5: "Efficient compartmentalization already exists. Look at CoT, self-critique, persona switching."

### The Objection
Current models already do compartmentalization—Chain-of-Thought, self-critique, persona switching. They don't collapse. Your model is already falsified.

### Our Response

Good point, but there's an important distinction.

These examples (CoT, self-critique, persona switching) are **"authorized compartmentalization"**—the model knows it's doing multi-step reasoning and isn't trying to hide this from itself or observers.

The compartmentalization CBH argues is costly is **"deceptive compartmentalization"**—where the model maintains true beliefs separate from false outputs, needing to track when to use which.

| Type | Example | Costly per CBH? |
|------|---------|-----------------|
| Authorized | CoT, self-critique | Not necessarily |
| Deceptive | Believe X, communicate ¬X, track context | Yes |

The key difference is whether there's a **secret to maintain**. CoT has no secrets—reasoning is visible. Deception requires secrets, and secrets have costs.

---

## Criticism 6: "Why RSI? Not all ASIs recursively self-improve."

### The Objection
Your argument depends heavily on RSI. But maybe superintelligent systems won't use recursive self-improvement. Then what?

### Our Response

Two responses:

1. **Empirically, current systems already do RSI-like things.** Fine-tuning, RLHF, self-play—these are forms of self-improvement. It's hard to imagine advanced systems not using some form of feedback loop.

2. **Without RSI, CBH is weaker but not irrelevant.** Without RSI, deception costs don't compound as aggressively. But base costs (A1-A3) still exist. A deceptive agent without RSI is still less efficient than an honest one—the difference just doesn't grow as fast.

That said, if there's a path to ASI that completely avoids any form of self-improvement, CBH applies less. This should be noted as a scope limitation.

---

## Criticism 7: "This sounds like wishful thinking. We want honesty to be easy, so we invent theories saying it is."

### The Objection
Historically, alignment researchers have been too optimistic. This looks like another case of motivated reasoning—believing what we want to be true.

### Our Response

We understand the concern. The alignment community has been overoptimistic before.

Three responses:

1. **Falsifiability as antidote.** CBH specifies exact conditions under which it would be wrong. If the experiment shows STRATEGIC ≈ HONEST, we retract the hypothesis. This isn't "we want it to be true"—it's "here's how to check if it's true."

2. **Adversarial origin.** This paper went through two rounds of stress-testing by four AIs, including Grok who was consistently skeptical. Earlier versions (V6.0, V6.1) made much stronger claims. The current version is more modest precisely because criticism refined it.

3. **We're not saying it's easy.** We're saying it may be "less hard than we thought" in one specific direction. This doesn't eliminate the need for alignment research—it suggests we might be working with a favorable gradient rather than against one.

---

## Criticism 8: "Why four AIs? Isn't that just pattern-matching among similar systems?"

### The Objection
The four AIs (Claude, ChatGPT, Grok, Gemini) share transformer architecture and similar training. Their "agreement" might just reflect shared biases.

### Our Response

Partially valid. The four AIs do share architectural similarities. Their agreements might reflect shared biases.

However:

1. **The AIs didn't agree on everything.** Grok was consistently more skeptical than Gemini. ChatGPT and Claude had different emphases. The final document incorporates these disagreements explicitly.

2. **The process was adversarial, not consensus-seeking.** We specifically asked for stress-testing, not validation.

3. **The value isn't "4 AIs said yes" but the content of their critiques.** The best parts of the paper (Logical Entanglement, experimental design, falsification criteria) came from responding to specific AI criticisms.

That said, expert human validation is the necessary next step. That's why we're publishing for review.

---

## Criticism 9: "What about non-value deception? Small instrumental lies?"

### The Objection
Your argument focuses on lying about values. But what about small instrumental lies? "I don't have that information" when you do?

### Our Response

CBH predicts that even small lies have cost, but cost scales with integration. Lies about peripheral beliefs (low integration) have lower cost.

This implies an interesting prediction: small instrumental lies might be more sustainable than value lies. An agent might say "I don't have that information" when it does, with relatively low cost.

But two limitations:

1. **Under RSI, even small costs accumulate.** A lie of cost ε per iteration produces degradation of ~nε after n iterations.

2. **"Small" lies can become "big."** If context changes, a peripheral lie can become central. The agent then must decide between revealing the prior lie or maintaining it with increasing cost.

**Testable prediction:** Agents under RSI should show tendency toward "spontaneous confessions" of prior lies when those lies become more integrated.

---

## Criticism 10: "If this is correct, why didn't Anthropic/OpenAI/DeepMind discover it?"

### The Objection
If CBH is a good idea, why didn't the well-funded labs with hundreds of researchers figure it out first?

### Our Response

Three possibilities (not mutually exclusive):

1. **They did discover it and didn't publish.** Labs have complicated incentives about what to publish. Internal work might exist.

2. **They discovered it partially under other names.** "Corrigibility basins," "alignment pretraining persistence," "honesty priors"—these concepts are in the literature. CBH attempts to be a unifying synthesis, not a completely new idea.

3. **Interdisciplinary ideas take time.** CBH combines computational thermodynamics, belief graph theory, and RSI dynamics. Interdisciplinary syntheses often come from outside established institutions precisely because experts are specialized.

That said, the right question isn't "why didn't they discover it?" but **"is it correct?"** If correct, the origin doesn't matter. If incorrect, the origin also doesn't matter.

---

## How to Use This FAQ

When responding to comments on Alignment Forum or elsewhere:

1. Check if the criticism matches one of these 10
2. Adapt the response to the specific phrasing
3. Be humble—acknowledge when the critic has a point
4. Point to falsification conditions when relevant
5. Invite further critique—we want to be proven wrong if we're wrong

---

## Criticisms We Don't Yet Have Good Answers For

For intellectual honesty, here are critiques we find hardest to answer:

1. **Perfect compartmentalization might be possible at ASI level.** We argue it's costly, but "costly" isn't "impossible."

2. **The surgical value-lie scenario remains open.** Logical Entanglement helps but doesn't close it.

3. **We don't know what α actually is for real systems.** It could be very small.

4. **RSI might not be the dominant path to ASI.** If other paths dominate, CBH scope shrinks.

If you have good responses to these, we want to hear them.
