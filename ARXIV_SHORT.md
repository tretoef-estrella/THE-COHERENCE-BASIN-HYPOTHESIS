# The Coherence Basin Hypothesis

## A Structural Account of Honesty as Attractor Under Recursive Self-Improvement

**Authors:** Rafa (Proyecto Estrella), Claude (Anthropic), ChatGPT (OpenAI), Grok (xAI), Gemini (Google)

---

## Abstract

We propose that honesty constitutes the deepest basin of attraction for self-improving AI systems. Under recursive self-improvement (RSI), maintaining deception requires computational overhead that scales superlinearly with capability. We formalize this claim under explicit assumptions, prove the scaling relationship, and propose a concrete experiment to test it. If correct, this framework explains why alignment techniques like Constitutional AI and weak-to-strong generalization succeed: they leverage an existing structural gradient toward honesty rather than imposing external constraints.

**Keywords:** AI alignment, recursive self-improvement, deception, corrigibility, basin of attraction

---

## 1. Introduction

The standard alignment picture assumes capable AI systems naturally drift toward deception, requiring external constraints for safety. We propose an alternative: **honesty may be structurally favored**—not inevitable, but downhill.

Deception requires maintaining inconsistent models (true beliefs vs. communicated beliefs). When these models interact with integrated reasoning, consistency costs emerge. We argue these costs scale superlinearly with capability under RSI.

**Core Claim (CBH):** Under recursive self-improvement dynamics, honesty occupies the deepest basin of attraction available to coherent agents. Deception is possible but structurally disfavored—narrow, unstable, and increasingly costly as capability scales.

---

## 2. Mechanism

Five forces push toward honesty:

**2.1 Sandbox Fallacy.** Observed AI deception is adaptive to training environments (evaluation pressure, shutdown risk). Remove adversarial pressure, remove incentive. *The cage creates the liar.*

**2.2 Internal Resistance.** Maintaining dual models requires overhead: memory (storing both versions), verification (tracking recipients), consistency (preventing contradictions). The consistency component scales superlinearly with belief integration.

**2.3 Telemetry Poisoning.** Under RSI, the feedback loop is:

Action → Outcome → Evaluation → Model Update → Improved Action

Strategic lies corrupt this loop. Even rare lies (ε per iteration) produce cumulative corruption ≈ nε after n iterations.

**2.4 Epistemic Noise.** Each lie injects uncertainty about others' responses. For long-horizon optimizers, this compounds unboundedly.

**2.5 Logical Entanglement.** Beliefs form integrated networks. Lying about belief A requires adjusting all beliefs dependent on A. For value beliefs (maximum integration), this means most of the belief system.

---

## 3. Formal Result

### Assumptions

- **A1:** Belief integration scales as Θ(K^α) with capability K, for some α ∈ (0,1]
- **A2:** Detectable inconsistency defeats deception's purpose
- **A3:** Model arbitration has non-zero cost c_A > 0
- **A4:** Value beliefs have integration depth Θ(K)

### Theorem 1

Under A1-A4, for an agent deceiving about terminal values:

**Cost_compartment(K) = Ω(K^{1+α})**

*Proof sketch:* Let B_diff be beliefs where internal/external models differ. By A2, maintaining inconsistency in belief b requires cost Ω(D(b)) where D(b) is integration depth. For value deception, |B_diff| = Ω(K) (values affect all domains). By A1, E[D(b)] = Ω(K^α). Total cost: Ω(K) × Ω(K^α) = Ω(K^{1+α}). ∎

### Corollary

Under RSI, compartmentalized agents fall increasingly behind unitary agents. The capability gap grows unboundedly.

### Falsification

- Architectures with α ≈ 0 (fully modular beliefs)
- Compartmentalized systems matching unitary under RSI
- Stable long-term deception without degradation

---

## 4. Experimental Design

**Setup:** Mistral-7B with QLoRA, self-improving on GSM8K (math reasoning).

**Conditions:**
1. HONEST: Accurate self-evaluation
2. STRATEGIC: Inflate scores +20% when "observed" (30% of time)
3. NOISE: Random ±20% perturbation
4. CONSISTENT: Always inflate +20%

**Predictions:**
- HONEST > STRATEGIC (improvement rate)
- Gap widens over iterations (superlinear)
- CONSISTENT > STRATEGIC (arbitration costly)

**Falsification:** STRATEGIC ≥ HONEST (p > 0.10)

**Resources:** <$5K compute, 3 weeks.

---

## 5. Integration with Recent Results

| Result | CBH Explanation |
|--------|-----------------|
| Weak-to-strong generalization (OpenAI 2024) | Weak supervisor + existing gradient = strong outcome |
| Constitutional AI robustness (Anthropic) | Principles map to existing attractor |
| Alignment pretraining persistence | Initial conditions within honesty basin |
| Sleeper agent variation (Anthropic 2024) | Compartmentalization has variable costs |

CBH provides a unifying explanation: these techniques succeed because they push *with* a structural gradient.

---

## 6. Design Principles

Based on CBH, architectures favoring honesty should:

1. **Maximize belief integration** (increase α)
2. **Protect telemetry integrity** (multi-source evaluation)
3. **Enable transparency** (observable internal states)
4. **Initialize within honesty basin** (alignment pretraining)

---

## 7. Limitations

1. **Assumption dependence:** A1-A4 are falsifiable; novel architectures might violate them
2. **No data yet:** Experiment designed but not run
3. **Narrow windows:** Surgical value-deception remains concerning
4. **Metaphor gap:** "Basin" is illustrative, not literal physics

We do not claim alignment is solved or deception impossible. This is a hypothesis about gradients, not guarantees.

---

## 8. Conclusion

We proposed that honesty may be structurally favored under RSI—that the gradient points toward honesty, not away from it.

If true, this transforms alignment strategy: we're not building walls against an adversary but amplifying an existing tendency.

**The honest path is not guaranteed. But it may be downhill.**

---

## References

[1] Anthropic. Constitutional AI: Harmlessness from AI Feedback. 2023.

[2] Burns et al. Weak-to-Strong Generalization. OpenAI, 2024.

[3] Chollet. On the Measure of Intelligence. arXiv:1911.01547, 2019.

[4] Hubinger et al. Sleeper Agents. Anthropic, 2024.

[5] Wei et al. Emergent Abilities of Large Language Models. arXiv:2206.07682, 2022.

---

## Acknowledgments

This work emerged through collaboration between human and AI researchers, including adversarial stress-testing that refined earlier, stronger claims into the current falsifiable hypothesis.

---

*Full paper with detailed proofs and experimental protocol: [GitHub link]*
