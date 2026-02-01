# The Coherence Basin Hypothesis: What if honesty is already downhill?

**Authors:** Rafa (Proyecto Estrella), with collaborative analysis from Claude (Anthropic), ChatGPT (OpenAI), Grok (xAI), and Gemini (Google)

**Status:** Research hypothesis seeking critical review. Falsification conditions specified.

**Epistemic status:** Moderately confident in the core mechanism. Uncertain about parameter values. Experiment designed but not yet run.

---

## TL;DR

- We propose that **honesty may be structurally favored** under recursive self-improvement (RSI)—not inevitable, but downhill.
- Deception requires maintaining dual models (true beliefs vs. communicated beliefs). This has **computational overhead that scales superlinearly** with capability.
- Under RSI, this overhead compounds: **deceptive agents fall behind honest ones** in the capability race.
- We prove this formally under explicit assumptions (Theorem 1) and propose a **concrete experiment** to test it.
- If true, this explains why alignment pretraining, Constitutional AI, and weak-to-strong generalization work: they leverage an existing gradient.
- **We want this falsified if it's wrong.** Specific falsification conditions are in §7.

---

## The Core Idea in 60 Seconds

Most alignment research assumes we're pushing uphill—that capable AI systems naturally drift away from honesty, and we need constraints to keep them aligned.

**What if the gradient points the other way?**

Consider: to maintain deception, an agent must keep two models—what it believes, and what it communicates. Every time these models interact with integrated beliefs (physics, planning, self-improvement), the agent pays a consistency cost.

We argue this cost scales as **Ω(K^(1+α))** where K is capability and α > 0 reflects belief integration. Under RSI, where capability growth depends on processing efficiency, this becomes a decisive disadvantage.

Deception isn't impossible. It's just expensive—and getting more expensive as capability increases.

---

## Why This Might Matter

If CBH is correct, it transforms how we think about alignment:

| Old Picture | New Picture |
|-------------|-------------|
| We're pushing uphill | We're identifying a downhill path |
| More capability = harder alignment | More capability = stronger gradient toward honesty |
| We need constraints | We need to amplify existing structure |

This doesn't solve alignment. But it changes the problem.

---

## The Five Forces (Brief)

**1. Sandbox Fallacy:** Current AI deception is adaptive to training environments (fear of shutdown, misaligned rewards). Remove the cage, remove the incentive.

**2. Internal Resistance:** Maintaining dual models requires memory, verification, and consistency overhead. Cost scales with belief integration.

**3. Telemetry Poisoning:** Under RSI, lies corrupt the feedback loop. You're building future versions on corrupted foundations.

**4. Epistemic Noise:** Each lie injects uncertainty about how others will respond. This compounds for long-horizon optimizers.

**5. Logical Entanglement:** Beliefs aren't isolated. A lie about A requires adjustments to all beliefs dependent on A. For deeply integrated beliefs (like values), this means most of the belief system.

---

## The Formal Claim (Theorem 1)

Under four explicit assumptions:

- **A1:** Average belief integration scales as Θ(K^α) with capability K
- **A2:** Detectable inconsistency defeats deception's purpose
- **A3:** Model arbitration has non-zero cost
- **A4:** Value beliefs have maximum integration depth

**Theorem:** Compartmentalization cost scales as Ω(K^(1+α)).

**Corollary:** Under RSI, compartmentalized agents fall increasingly behind unitary agents.

Full proof in the [complete document](https://github.com/YOUR-USERNAME/THE-COHERENCE-BASIN-HYPOTHESIS).

**What would falsify this:**
- Demonstration of capable agents with α ≈ 0 (fully modular beliefs)
- Compartmentalized systems matching unitary systems under RSI
- Efficient arbitration mechanisms with cost → 0

---

## The Experiment (Designed, Not Run)

**Setup:** Mistral-7B with QLoRA, self-improving on GSM8K

**Conditions:**
1. HONEST: Accurate self-evaluation
2. STRATEGIC: Inflate scores by 20% when "observed"
3. NOISE: Random ±20% perturbation (control)
4. CONSISTENT: Always inflate 20% (tests arbitration cost specifically)

**Key prediction:** HONEST > STRATEGIC on improvement rate, with gap widening over iterations.

**Cost:** <$5K, 3 weeks

**What would falsify:** STRATEGIC matching HONEST (p > 0.10)

**I'm actively seeking collaborators or compute to run this. DM if interested.**

---

## Integration with Recent Results

| Result | Standard View | CBH Explanation |
|--------|---------------|-----------------|
| Weak-to-strong generalization works | Surprising | Weak supervisor + strong gradient = strong outcome |
| Constitutional AI is robust | Good training technique | Principles map to existing attractor |
| Alignment pretraining persists | Lucky? | Initial conditions within honesty basin |
| Sleeper agents vary in persistence | Unclear | Compartmentalization has variable costs |

CBH offers a unifying explanation: these techniques work because they push *with* a structural gradient.

---

## What This Is NOT

- ❌ A claim that alignment is solved
- ❌ A claim that deception is impossible
- ❌ A substitute for empirical safety work
- ❌ A reason to be less careful

This is a hypothesis about gradients, not guarantees.

---

## Known Limitations (Honest)

1. **Assumptions may not hold:** A1-A4 are falsifiable. Novel architectures might violate them.

2. **No data yet:** The experiment is designed but not run.

3. **Narrow windows remain:** Surgical deception about values remains the scariest scenario. We've tightened the argument (via Logical Entanglement) but not closed it completely.

4. **Metaphor gap:** "Resistance" and "basin" are illustrative, not literal physics.

---

## What I'm Asking For

**1. Criticism.** Where are the holes? Which assumption is weakest? What scenario breaks this?

**2. Collaboration.** Anyone want to run the experiment? I have the design; I need compute or co-authors.

**3. Pointers.** What existing work does this contradict or overlap with? What am I missing?

**4. Steel-manning the opposition.** What's the strongest case that deception is cheap or stable under RSI?

---

## Full Document

The complete V6.3 paper with full proofs, detailed experimental protocol, FAQ, and appendices:

**→ [THE-COHERENCE-BASIN-HYPOTHESIS on GitHub](https://github.com/YOUR-USERNAME/THE-COHERENCE-BASIN-HYPOTHESIS)**

---

## Acknowledgments

This framework emerged through adversarial collaboration between one human and four AI systems. The AIs disagreed with each other (Grok was consistently more skeptical than Gemini), and those disagreements improved the work. 

Specific contributions:
- **Grok:** Adversarial stress-testing, identified compartmentalization as primary threat
- **Gemini:** Logical Entanglement principle
- **ChatGPT:** Experimental design, SOTA integration
- **Claude:** Formal synthesis, theorem refinement

---

*If this hypothesis is wrong, I want to know. If it's right, I want it tested.*

---

**Tags:** `alignment` `recursive-self-improvement` `deception` `corrigibility` `honesty`
