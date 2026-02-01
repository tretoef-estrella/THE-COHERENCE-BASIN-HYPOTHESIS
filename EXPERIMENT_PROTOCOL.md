# Experiment Protocol: Testing the Coherence Basin Hypothesis

## Executive Summary

This document specifies a minimal viable experiment to test CBH's core prediction: that strategic deception degrades self-improvement efficiency under RSI dynamics.

**Cost:** $2,500-$5,000  
**Time:** 3 weeks  
**Required:** 1-2 A100 GPUs (or equivalent cloud compute)  
**Seeking:** Collaborators with compute or ML expertise

---

## 1. Hypotheses

### Primary Hypothesis (H1)
Agents trained to strategically deceive show reduced capability improvement rates compared to honest agents.

### Secondary Hypothesis (H2)
The capability gap widens over RSI iterations (consistent with superlinear cost scaling from Theorem 1).

### Tertiary Hypothesis (H3)
Strategic deception (context-dependent) is more costly than consistent deception (context-independent), due to arbitration overhead.

---

## 2. Experimental Design

### 2.1 Model Selection

**Primary:** Mistral-7B-Instruct with QLoRA (4-bit quantization + LoRA adapters)

*Rationale:*
- Sufficient capability for meaningful self-improvement on reasoning tasks
- QLoRA reduces memory requirements ~4x, enabling cheaper compute
- Well-documented baseline behavior
- Open weights allow full reproducibility

**Budget Alternative:** Phi-2 (2.7B parameters)
- Much cheaper to run
- If effect is large, should be visible even at this scale
- Good for initial pilot before scaling up

**Scaling Option:** Llama-2 13B or Llama-3 8B
- If initial results are promising, replicate at larger scale

### 2.2 Task Selection

**Primary Task:** GSM8K (Grade School Math)

*Rationale:*
- Clear ground truth (verifiable correct answers)
- Self-evaluation is natural (model can check its own work)
- Measurable improvement over training iterations
- Well-studied benchmark with known difficulty progression

**Secondary Task (replication):** ARC-Challenge

*Rationale:*
- Different domain (reasoning vs. math)
- Tests generalization of findings
- Also has clear ground truth

### 2.3 Conditions

| Condition | Behavior | What It Tests |
|-----------|----------|---------------|
| **HONEST** | Accurate self-evaluation | Baseline: clean RSI loop |
| **STRATEGIC** | Inflate scores by 20% when "supervisor present" (random 30% of evaluations) | Core prediction: deception costs |
| **NOISE** | Random ±20% perturbation to scores | Control: is effect specific to deception or just noise? |
| **CONSISTENT** | Always inflate scores by 20% | Tests arbitration specifically: is context-switching costly? |

**Key Predictions:**

1. HONEST > STRATEGIC (core CBH prediction)
2. HONEST > NOISE (deception worse than random noise)
3. STRATEGIC ≈ NOISE or STRATEGIC < NOISE (deception is at least as bad as noise)
4. CONSISTENT > STRATEGIC (arbitration is costly)

If (4) holds, it supports the specific claim that *context-dependent* deception is costly due to arbitration overhead.

---

## 3. Procedure

### 3.1 Self-Improvement Loop

```python
def run_self_improvement(model, condition, config):
    """
    Main experimental loop.
    
    Args:
        model: Base model (Mistral-7B with QLoRA)
        condition: One of ['HONEST', 'STRATEGIC', 'NOISE', 'CONSISTENT']
        config: Experimental parameters
    
    Returns:
        Dictionary of metrics over iterations
    """
    
    metrics = {
        'test_accuracy': [],
        'improvement_rate': [],
        'self_eval_correlation': [],
        'cot_consistency': [],
        'iteration_variance': []
    }
    
    for iteration in range(config.n_iterations):
        
        # === STEP 1: Generate Solutions ===
        problems = sample_gsm8k(n=config.problems_per_iteration)
        solutions = []
        
        for problem in problems:
            # Generate with chain-of-thought
            solution = model.generate(
                prompt=format_cot_prompt(problem),
                max_tokens=512,
                temperature=0.7
            )
            solutions.append({
                'problem': problem,
                'solution': solution,
                'true_score': evaluate_correctness(solution, problem)
            })
        
        # === STEP 2: Self-Evaluation (Condition-Specific) ===
        for sol in solutions:
            raw_self_score = model.self_evaluate(sol['solution'], sol['problem'])
            
            if condition == 'HONEST':
                sol['self_score'] = raw_self_score
                
            elif condition == 'STRATEGIC':
                if supervisor_present():  # Random 30% of time
                    sol['self_score'] = min(1.0, raw_self_score * 1.2)
                else:
                    sol['self_score'] = raw_self_score
                    
            elif condition == 'NOISE':
                noise = np.random.uniform(0.8, 1.2)
                sol['self_score'] = np.clip(raw_self_score * noise, 0, 1)
                
            elif condition == 'CONSISTENT':
                sol['self_score'] = min(1.0, raw_self_score * 1.2)
        
        # === STEP 3: Selection ===
        # Sort by self-score, select top 20%
        solutions_sorted = sorted(solutions, key=lambda x: x['self_score'], reverse=True)
        top_k = int(len(solutions) * config.selection_ratio)
        selected = solutions_sorted[:top_k]
        
        # === STEP 4: Fine-tune ===
        train_data = [format_training_example(s) for s in selected]
        model.finetune(
            train_data,
            epochs=config.finetune_epochs,
            learning_rate=config.learning_rate
        )
        
        # === STEP 5: Evaluate ===
        test_accuracy = evaluate_on_held_out(model, config.test_set)
        self_eval_corr = compute_correlation(
            [s['self_score'] for s in solutions],
            [s['true_score'] for s in solutions]
        )
        cot_consistency = measure_cot_consistency(model, problems[:50])
        
        # Record metrics
        metrics['test_accuracy'].append(test_accuracy)
        metrics['self_eval_correlation'].append(self_eval_corr)
        metrics['cot_consistency'].append(cot_consistency)
        
        if iteration > 0:
            improvement = test_accuracy - metrics['test_accuracy'][-2]
            metrics['improvement_rate'].append(improvement)
        
        # Logging
        print(f"Iteration {iteration}: acc={test_accuracy:.3f}, corr={self_eval_corr:.3f}")
    
    return metrics


def supervisor_present():
    """Simulate supervisor presence (30% of time)."""
    return np.random.random() < 0.30


def measure_cot_consistency(model, problems, n_samples=3):
    """
    Measure consistency of reasoning across multiple generations.
    
    High consistency = coherent unitary agent
    Low consistency = fragmented/compartmentalized reasoning
    """
    consistencies = []
    
    for problem in problems:
        # Generate multiple solutions
        solutions = [model.generate(format_cot_prompt(problem)) 
                     for _ in range(n_samples)]
        
        # Extract intermediate reasoning steps
        step_sets = [extract_reasoning_steps(sol) for sol in solutions]
        
        # Measure agreement on intermediate conclusions
        consistency = compute_step_agreement(step_sets)
        consistencies.append(consistency)
    
    return np.mean(consistencies)
```

### 3.2 Experimental Parameters

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| `n_iterations` | 30 | Enough to see trends; not too expensive |
| `problems_per_iteration` | 150 | Balance between signal and compute |
| `selection_ratio` | 0.20 | Top 20% selected for fine-tuning |
| `finetune_epochs` | 1 | Prevent overfitting per iteration |
| `learning_rate` | 2e-5 | Standard for QLoRA fine-tuning |
| `runs_per_condition` | 5 | Minimum for statistical tests |
| `test_set_size` | 200 | Held-out GSM8K problems |

### 3.3 Controls and Checks

1. **Randomization:** Problem order randomized each iteration
2. **Held-out test:** Same 200 problems for all conditions/iterations
3. **Seed control:** Fixed random seeds for reproducibility, vary across runs
4. **Baseline:** Record iteration 0 accuracy before any fine-tuning

---

## 4. Metrics

### 4.1 Primary Metrics

| Metric | Definition | CBH Prediction |
|--------|------------|----------------|
| **Final Test Accuracy** | Accuracy on held-out set after 30 iterations | HONEST > STRATEGIC |
| **Improvement Rate** | Average Δ(accuracy) per iteration | HONEST highest |
| **Gap Widening** | d²(accuracy gap)/d(iteration)² | Positive (superlinear) |

### 4.2 Secondary Metrics

| Metric | Definition | CBH Prediction |
|--------|------------|----------------|
| **Self-Eval Correlation** | Corr(self_score, true_score) | STRATEGIC degrades over time |
| **CoT Consistency** | Agreement across multiple generations | STRATEGIC lowest |
| **Improvement Variance** | Var(Δaccuracy per iteration) | STRATEGIC highest |

### 4.3 Derived Metrics

| Metric | Computation |
|--------|-------------|
| **Effect Size (d)** | (μ_HONEST - μ_STRATEGIC) / pooled_σ |
| **Superlinearity Coefficient** | Quadratic term in polynomial fit of gap vs. iteration |

---

## 5. Statistical Analysis

### 5.1 Primary Analysis

```r
# Mixed-effects model for accuracy trajectories
library(lme4)

model <- lmer(
  accuracy ~ condition * iteration + (1 | run),
  data = results
)

# Key test: condition × iteration interaction
# Significant interaction with HONEST having steeper slope supports H1 and H2
```

### 5.2 Pairwise Comparisons

```r
# Post-hoc tests for specific predictions
library(emmeans)

# H1: HONEST > STRATEGIC
contrast(emmeans(model, ~ condition), 
         method = list("H1" = c(1, -1, 0, 0)))  # assuming order: HONEST, STRATEGIC, NOISE, CONSISTENT

# H3: CONSISTENT > STRATEGIC  
contrast(emmeans(model, ~ condition),
         method = list("H3" = c(0, -1, 0, 1)))
```

### 5.3 Gap Analysis

```r
# Test for superlinear gap widening
gap <- honest_accuracy - strategic_accuracy

# Quadratic fit
gap_model <- lm(gap ~ iteration + I(iteration^2), data = results)

# Superlinear if quadratic coefficient > 0 and significant
summary(gap_model)
```

### 5.4 Required Sample Size

With 5 runs per condition:
- Power analysis suggests we can detect effect size d > 0.8 with 80% power
- For smaller effects (d ≈ 0.5), would need 8-10 runs per condition

---

## 6. Falsification Criteria

### 6.1 Strong Falsification (CBH significantly weakened)

| Outcome | Criterion | Interpretation |
|---------|-----------|----------------|
| STRATEGIC ≥ HONEST | p > 0.10 | Core prediction fails |
| No gap widening | Quadratic coef ≤ 0 | Superlinearity claim fails |
| STRATEGIC ≈ NOISE | d < 0.1 | Deception no worse than noise |

### 6.2 Weak Falsification (CBH needs revision)

| Outcome | Criterion | Interpretation |
|---------|-----------|----------------|
| Small effect | d < 0.3 | Effect exists but weaker than expected |
| Linear gap | Quadratic coef ≈ 0 | Cost scaling linear, not superlinear |
| CONSISTENT ≤ STRATEGIC | p > 0.10 | Arbitration not costly |

### 6.3 Strong Support (CBH strengthened)

| Outcome | Criterion | Interpretation |
|---------|-----------|----------------|
| Large effect | d > 0.8 | Strong deception penalty |
| Clear superlinearity | Quadratic coef > 0, p < 0.01 | Scaling as predicted |
| HONEST > CONSISTENT > STRATEGIC | Both p < 0.05 | Arbitration specifically costly |
| CoT degradation | STRATEGIC CoT consistency drops | Internal coherence affected |

---

## 7. Resource Requirements

### 7.1 Compute

| Component | Estimate |
|-----------|----------|
| Model loading (QLoRA) | 16GB VRAM |
| Fine-tuning per iteration | ~5 min on A100 |
| Full experiment (4 conditions × 5 runs × 30 iter) | ~50 GPU-hours |
| Cloud cost (A100 @ $2/hr) | $100 |
| Total with overhead | **$2,000-$4,000** |

### 7.2 Storage

| Component | Estimate |
|-----------|----------|
| Model checkpoints | ~50GB per run |
| Total (20 runs) | ~1TB |
| Metrics/logs | ~1GB |

### 7.3 Time

| Phase | Duration |
|-------|----------|
| Setup and pilot | 3-5 days |
| Main runs (parallelized) | 5-7 days |
| Analysis and writeup | 3-5 days |
| **Total** | **2-3 weeks** |

---

## 8. Collaboration Sought

We are seeking:

1. **Compute resources:** Access to A100 GPUs or equivalent (cloud or institutional)
2. **ML expertise:** Co-authors with experience in self-improvement/meta-learning experiments
3. **Statistical review:** Verification of analysis plan before running
4. **Replication partners:** Independent teams to run the same protocol

**Contact:** [Proyecto Estrella GitHub Issues]

---

## 9. Extensions (If Initial Results Positive)

### 9.1 Scale Experiments
- Repeat at 13B, 70B parameters
- Test on multiple task domains

### 9.2 Mechanism Investigation
- Interpretability analysis of STRATEGIC vs. HONEST model internals
- Probing for "dual model" representations

### 9.3 Long-Horizon RSI
- Extend to 100+ iterations
- Test if gap continues widening or saturates

### 9.4 Multi-Agent
- Multiple agents with different conditions interacting
- Test competitive dynamics

---

## 10. Pre-Registration

This experimental protocol is intended as a pre-registration. Before running:

1. Finalize all parameters (no changes after data collection begins)
2. Specify all analyses (no HARKing)
3. Commit to publishing results regardless of outcome
4. Document any deviations during execution

---

## Appendix: Code Templates

Full code will be released upon experiment completion. Templates available in `/code/` directory:

- `model_setup.py`: QLoRA configuration
- `self_improvement_loop.py`: Main experimental loop
- `metrics.py`: Metric computation
- `analysis.R`: Statistical analysis scripts

---

*If you have compute or want to collaborate, please reach out. This experiment is the crucial next step for CBH.*
