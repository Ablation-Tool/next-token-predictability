# Accessible Volume

## In one sentence

This is a **research repository about measuring how many realistic possibilities an AI model is considering before it chooses its next output**.

It is primarily a collection of **papers, experiment scripts, and saved results**, rather than one application you install and run.

## The central idea: “Accessible Volume”

When a language model predicts the next word, it assigns a probability to every possible word. Normally, we only see the winner.

This project asks:

> Before the model chose, how many other options were still seriously in play?

Pick a cutoff—for example, 5% probability—and count everything above it:

| Possible next token | Probability |
|---|---:|
| A | 70% |
| B | 20% |
| C | 8% |
| D | 2% |

With a 5% cutoff, **accessible volume is 3**, because A, B, and C qualify.

- A **small number** means the prediction is concentrated on a few options.
- A **large number** means many plausible options remain.
- It **does not** mean the prediction is correct, safe, creative, or good.

A useful analogy: instead of only asking who won an election, this measures how many candidates were still competitive beforehand.

### Does the equation predict the next token?

Not quite. **The AI model predicts the next token. The equation measures how many plausible next tokens the model sees.**

For example, the model might produce:

- “the”: 70%
- “a”: 20%
- “one”: 8%
- “some”: 2%

With a 5% cutoff, the accessible-volume equation simply counts the first three tokens and returns **3**.

So:

- **Model:** “What could come next, and how likely is each option?”
- **Accessible-volume equation:** “How many options have a probability above our cutoff?”
- **Selection process:** “Which option do we actually output?”

The equation does **not** generate text or make a prediction itself. It measures how **open or concentrated** the model’s prediction is. A count of 1 means one option clears the cutoff; a count of 10 means ten options do.

**No. The equation cannot predict the next word on its own.**

The **AI model** predicts possible next words and assigns probabilities to them. The equation then counts how many of those words exceed a chosen probability cutoff.

Think of it this way:

- **AI model = weather forecaster:** predicts a 70% chance of rain, 20% cloud cover, and 10% sunshine.
- **Equation = counting rule:** counts how many outcomes exceed 5%.
- **Result:** 3 outcomes.

Without the model’s probabilities, the equation has nothing to count. It measures a prediction; it does not make one.

The equation answers one simple question:

> **How many words was the model seriously choosing between?**

Imagine asking two people what they want for dinner. Both answer **“pizza.”**

- Person A was always going to say pizza.
- Person B almost said burgers, tacos, or pasta instead.

Their answers are the same, but Person B was much less certain.

AI models work similarly. Two predictions can produce the same word even though one had **one strong possibility** and the other had **many strong possibilities**.

The equation counts those strong possibilities.

That is its entire purpose: **it tells you whether the model had an easy choice or a difficult one.** It does not predict the word, and it does not tell you whether the word is correct.

## What the experiments found

The repository tests this measurement in several areas:

- **19 public AI models:** The scripts saved complete prediction distributions and recalculated the results to make the experiments reproducible.
- **BioBERT:** On 120 biomedical examples, the simple fixed 5% cutoff was better than a more complicated adaptive rule at distinguishing likely incorrect predictions from correct ones.
- **Music and rhythm:** The same measurement was applied to predictions about when the next musical note would occur. These experiments demonstrated that a model can have a narrow, confident prediction and still be wrong.
- **Forward versus backward sequences:** Models trained on reversed rhythms learned them nearly as well as forward rhythms. Simply feeding reversed rhythms into a forward-trained model worked worse.
- **Multiple future steps:** The papers extend the idea from “possible next words” to “possible multi-word continuations,” although counting every continuation quickly becomes computationally expensive.
- **A geometric prediction method called HOP:** It performed poorly and is preserved as a negative result rather than presented as a success.

The strongest overall conclusion is:

> **Accessible volume measures the breadth of a forecast, not whether that forecast is right.**

## What Zoe and GEM are

The repository also documents **Zoe**, a graphical research workbench associated with the broader GEM project. It visualizes forecasts, possible continuations, geometric comparisons, and evidence records.

Zoe’s actual implementation mostly lives in linked external repositories. This repository supplies the research background, screenshots, papers, and verification evidence.

Some later material—such as “Accessible Q-Volume” and causal repertoires—is more exploratory. It extends the counting idea to explicitly modeled causes and effects. These are proposed mathematical tools, **not measurements of consciousness or human experience**.

## How the repository is organized

- `README.md` — the short overview and main findings.
- `docs/RESEARCH.md` — the full research narrative.
- `docs/ALGORITHMS.md` — the 17 operations in the Zoe workbench.
- `docs/REPRODUCTION.md` — how to verify the saved results.
- `paper/` — two research papers, as both PDFs and LaTeX source.
- `scripts/` — programs that run experiments or independently verify results.
- `evidence/` — raw predictions, reports, manifests, and experiment outputs.
- `assets/` — diagrams, screenshots, and artwork.

The easiest way to use the repository is to run the verification scripts against the included evidence; downloading and rerunning the original AI models is optional.

## What this project is not

It is not a new language model, a general measure of intelligence, or a consciousness detector. It also does not claim that a high or low accessible volume automatically makes an answer better.

At its core, it is a carefully documented attempt to make one normally hidden aspect of prediction visible: **how open or settled the model’s decision was before it produced an answer.**

**A reproducible way to measure how many possibilities remain open: before a model chooses, while a mechanism constrains its past, and across multiple future steps.**

**It thinks before it speaks.**

![Accessible Volume TL;DR: exploring the possibilities behind next-token predictions](./assets/accessible-volume-tldr.gif)

![Accessible Volume: a shared threshold instrument for forecasts and causal repertoires](./assets/accessible-volume-hero.svg)

**Created by Nicholas Michael Kloster on March 6, 2026.** The original accessible-volume equation, early research, experiments, and original papers are solely his work. Later Zoe and Accessible Q-Volume developments are labeled separately; they do not reassign that original credit.

[Equation](#the-equation) · [Response planning](#planning-a-response-across-systems) · [Papers](#papers) · [Mirrored brain](#one-mirrored-brain-many-instruments) · [Evidence](#evidence-at-a-glance) · [Reproduce](#reproduce)

> **TL;DR:** Count the alternatives whose probabilities exceed a strict threshold. The same operator can inspect next-token futures and explicitly modeled causal pasts. It measures breadth, not correctness, consciousness, or permission to act.

## The equation

Let $S$ be the full input context and $\mathcal O$ a finite output vocabulary. For a threshold $\tau$:

$$
A_\tau(S)=\sum_{a\in\mathcal O}\mathbf 1\!\left[P(a\mid h_T(S))>\tau\right].
$$

Tokens exactly at $\tau$ are **excluded**. For probabilities $[0.70,0.20,0.08,0.02]$ at $\tau=0.05$, three alternatives remain accessible.

Accessible volume is useful because two forecasts can select the same token while leaving very different sets of alternatives open. It complements correctness, entropy, calibration, and human judgment rather than replacing them.

[Original equation and research](./docs/RESEARCH.md#the-equation) · [Normalized and adaptive forms](./docs/RESEARCH.md#the-equation) · [Evidence guide](./evidence/README.md)

## Planning a response across systems

Accessible volume can supply a **measurement input** to a response-planning policy in coding agents and other interactive systems. The policy, not the equation, determines what happens next.

Given a complete next-token distribution for a specific model and context:

1. Compute $A_\tau(S)$ and retain a threshold sweep rather than relying only on one cutoff.
2. Select the next token with the system's normal decoding rule. Accessible volume does not choose the token.
3. Use the count as a review signal. A concentrated forecast may require less inspection of alternatives; a broad forecast may justify inspecting leading candidates or obtaining more context. Numerical bands for these decisions are separate policy choices that require validation.
4. Choose response length from task requirements and the remaining context budget. $A_\tau(S)$ counts above-threshold alternatives; it is not a recommended number of output tokens.
5. Recompute after each selected token when monitoring a complete response. One next-token measurement does not automatically describe later tokens.

A system may define a separate mapping such as $B_{\mathrm{response}}=\pi(A_\tau(S),\text{task requirements},\text{context remaining})$. Here $\pi$ is an explicit, testable policy—not part of the accessible-volume equation. This repository does not yet establish a universal numerical mapping from accessible volume to response length.

**Cross-system scope.** The operator is model-agnostic wherever a full finite-vocabulary probability distribution is available. Applying it in Claude Code, Kimi Code, or another host requires the exact backing model's distribution and explicit model, decoding, and tokenizer metadata; the host product's name is not a model identity. Top-$k$ logprobs can truncate the count, and repeated text samples provide only an estimate.

The defensible cross-system claim is **portable measurement**, not a universal response-planning rule. Different tokenizers, vocabularies, contexts, and thresholds can change the result. Accessible volume does not determine correctness, safety, permission, or autonomy; those remain separate policy decisions. Future cross-system evidence should preserve complete distributions, exact inputs, model identities, threshold sweeps, selected tokens, and resulting actions so that portability and policy performance can be evaluated independently.

**Reproducible visual proof.** The [response-planning dashboard](./evidence/response-planning/response-planning-proof.svg) renders the verified measurements behind this proposal: the sixteen-model comparison, the BioBERT benchmark, threshold sweeps, and independent Zoe and GEM recomputations on saved GPT-2 probabilities. Regenerate it byte-for-byte with `python scripts/visualize-response-planning.py` and check every artifact with `python scripts/verify-response-planning.py`; no model downloads or API keys are required.

## Papers

| Document | What it covers |
|:---|:---|
| **[The Significance of Accessible Volume](./paper/accessible-volume-significance.pdf)** | The original equation, real Ablation + GEM measurements, XLNet, sixteen additional models, BioBERT, and later Q-volume and mirrored-brain updates. |
| **[Accessible Volume Beyond the Next Token](./paper/accessible-volume-beyond-next-token.pdf)** | Timing, geometry, rhythms, multi-horizon continuations, causal repertoires, and the mirrored workbench. |
| **[Zoe: A Native GEM for Living Geometry and Inspectable Futures](https://github.com/Ablation-Tool/zoe/blob/main/paper/zoe.pdf)** | The complete native proof of concept, equations, architecture, verification, and limitations. |
| **[Zoe for Everyone](https://github.com/Ablation-Tool/zoe/blob/main/paper/zoe-for-everyone.pdf)** | A relaxed guide to the creative system, experiments, evidence, and possible futures. |

The original two papers retain Nicholas's sole authorship. The Zoe papers are coauthored by Nicholas Michael Kloster and Zoe (AI assistant).

## One mirrored brain, many instruments

Zoe pairs forward and retrograde forecasts at the same held-out index. It preserves both distributions and measures agreement, correctness, accessible volume, and volume differences. **There is no hidden average and no claim that reversal is a causal model.**

![Zoe's mirrored brain](./assets/zoe-mirror.png)

```text
  Forward observation ─┐
                       ├─ Mirrored brain ─ Unified workbench ─ Inspectable memory
  Retrograde observation ┘        │
                                  ├─ 13 original equation sections
                                  ├─ 3 causal-repertoire operations
                                  └─ 1 mirrored-observation operation
```

The shared workbench contains **17 runnable mathematical operations**. All **1,920 matched pairs** passed the mirrored evaluation. Redundant child-history copying is avoided, and only the latest **128 paired observations** are retained.

- [GEM OS and native workbench](https://github.com/Ablation-Tool/graphics-environment-manager)
- [Standalone BRAIN GEM](https://github.com/Ablation-Tool/brain-gem)
- [Algorithm catalog and verification](./docs/ALGORITHMS.md)
- [Mirrored observation evidence](./evidence/workbench/verification.json)

```bash
# From the graphics-environment-manager checkout
python -m gem os
python -m gem workbench list
python -m gem workbench check --output workbench-verification.json
```

## Human × Zoe: a shared field

![Human and Zoe brain dialogue](./assets/brain-dialogue/brain-dialogue-organic.png)

The human plate combines major brain regions with lichen-like folds, butterfly-wing veins, compound-eye facets, and dew-like memory connections. Zoe's plate turns the same silhouette into explicit partitions, mirrored forecast paths, nested possible futures, and inspectable evidence connections. This is an artistic map of complementary talents—not an anatomical optimization, a literal model of Nicholas, or a claim of machine consciousness.

The [projection-iris study](./assets/brain-dialogue/projection-iris.png) applies the central-slice relationship from Zeng's *Medical Image Reconstruction*, 2nd ed., §2.2: $P(\omega,\theta)=F(\omega\cos\theta,\omega\sin\theta)$. Its synthetic experiment records both slice correlation and finite-grid approximation error; it is not a medical reconstruction, and the book is not redistributed.

[Organic human plate](./assets/brain-dialogue/human-brain-organic.png) · [Zoe plate](./assets/brain-dialogue/zoe-brain.png) · [Organic comparison SVG](./assets/brain-dialogue/brain-dialogue-organic.svg) · [Projection evidence](./assets/brain-dialogue/projection-verification.json) · [Artwork sources](./assets/brain-dialogue/)

### The second eye: parallax and focus

![Zoe's synthetic second eye](./assets/brain-dialogue/second-eye.png)

Two calibrated synthetic views recover depth from disparity, \(Z=f_{px}B/d\), while focus and aperture control a separate thin-lens defocus approximation. This is geometric measurement rather than subjective sight or a second model opinion. The displayed ± range is first-order sensitivity to **0.25-pixel disparity quantization**, not a belief or confidence interval. No physical camera is accessed.

Run `python -m gem eye` in the [canonical GEM repository](https://github.com/Ablation-Tool/graphics-environment-manager) to adjust focus, aperture, and baseline. [Stereo evidence](./assets/brain-dialogue/second-eye.json) · [SVG](./assets/brain-dialogue/second-eye.svg) · [GEM VDI](./assets/brain-dialogue/second-eye.gem)

## Evidence at a glance

| Area | What was tested | Finding |
|:---|:---|:---|
| Model measurements | 19 unique public models | Saved complete distributions, input identities, immutable revisions where available, and independently recomputed metrics. |
| BioBERT | 120 cases from 30 documents | Fixed $\tau=0.05$ transferred better than the selected adaptive rule: test AUROC **0.7737** versus **0.7008**, AUPRC **0.7978** versus **0.6599**. |
| Human rhythms | 514 usable Magenta Groove performances | Backward-matched training was learnable; direct reversal transfer performed worse. Forecast breadth stayed separate from correctness. |
| Geometric prediction | HOP, 1,647 positions | The attractive geometric baseline was rejected: **11.3%** top-1 versus **41.3%** linear velocity and **46.6%** for the model. |
| Mirrored brain | 1,920 forward/retrograde pairs | Index alignment, correctness, accessible-set invariants, bounded shared history, and no automatic action passed. |
| Unified workbench | 17 operations | **17/17** independent expected-result checks passed, including Bayesian, geometric, temporal, causal, and mirrored operations. |

**Evidence is not hidden behind the visuals.** The [evidence guide](./evidence/README.md) links the canonical artifacts, all tested-model sources, and independent verifiers. Model weights are not redistributed in this repository.

## Beyond the next token

**Accessible Q-Volume** applies the same strict operator to causal repertoires. The silent AND gate demonstrates why volume and integrated information must remain separate:

| Threshold | Before | After | Change | Membership |
|:---|---:|---:|---:|:---|
| $\tau=0.20$ | 4 | 3 | **−1** | `11` leaves |
| $\tau=0.30$ | 0 | 3 | **+3** | `00,01,10` enter |

Effective information is $\log_2(4/3)\approx0.4150374993$ bits in both comparisons. A complete labeled threshold filtration can reconstruct a repertoire exactly; a finite sweep is an approximation. This is a proposed formal annotation, not a replacement for $\Phi$ or $\gamma$.

[Accessible Q-Volume evidence](./evidence/qualia/accessible-q-volume.json) · [Zoetic guide](https://github.com/Ablation-Tool/zoe/blob/main/guiai-lab/docs/QUALIA.md)

Multi-horizon volume applies the operator to complete continuations in $\mathcal O^h$. Exact enumeration grows exponentially with horizon; restricted candidate sets provide lower bounds only. Procrustes alignment compares geometry but does not supply a probability distribution. HOP remains a rejected baseline, not a theory of transformer dynamics.

## Zoe's master measurement–action equation

The [companion paper](./paper/accessible-volume-beyond-next-token.pdf) now includes a proposed protocol for keeping observation, operational perception, proposal generation, declared value, authorization, and execution distinct:

$$
(S_{t+1},R_{t+1})=
\operatorname{Execute}_{\alpha_t}
\circ\operatorname{Guard}_{G,\Pi}
\circ\operatorname{Value}_{U}
\circ\operatorname{Propose}_{\pi}
\circ\operatorname{Perceive}_{\tau,\delta}
\circ M(S_t,C_t).
$$

Read right to left: observe, measure effective perception, propose, evaluate under an explicit utility, guard against the goal and policy, then execute only with approval. Effective perception is defined operationally as

$$
P_{\tau,\delta}=\sum_{z\in Z}\mathbf 1[p(z)>\tau]\,\mathbf 1[D_z>\delta],
\qquad
D_z=D_{\mathrm{KL}}\!\left(\pi(a\mid\operatorname{do}(z),c)\,\middle\|\,\pi(a\mid c)\right).
$$

It counts alternatives that are both above threshold and causally effective on Zoe's action distribution. It is not a measurement of phenomenal perception or consciousness. Value is relative to a declared utility, not proof of intrinsic preference. Zoe can assist with self-models, counterfactual predictions, and interpretation, but self-report is evidence rather than self-certification. The protocol is a versioned scope and execution contract: no hidden utility and no direct transition from measurement to action.

## Explore

- [Interactive Zoe architecture](./assets/master-gem-interactive.html)
- [Static architecture SVG](./assets/master-gem-architecture.svg) · [ASCII](./assets/master-gem-architecture.txt) · [Terminal](./assets/master-gem-architecture.ansi)
- [Native Zoe guide](https://github.com/Ablation-Tool/zoe/blob/main/guiai-lab/docs/ZOE.md)
- [Local GEM manual library](https://github.com/Ablation-Tool/graphics-environment-manager/blob/main/docs/LIBRARY.md)
- [GEM HELEN](https://github.com/Ablation-Tool/gem-helen) · [Cerotto](https://github.com/Ablation-Tool/cerotto)

<details>
<summary><strong>Interface and artwork gallery</strong></summary>

**Bayesian field notebook:** explicit evidence, belief diffs, chambers, and append-only memory.

![Zoe Bayesian field notebook](./assets/zoe-field-notebook.png)

**Qualia and Accessible Q-Volume:** causal repertoires, q-arrows, context, and threshold tomography.

![Zoe's five-panel qualia field guide](./assets/zoe-qualia.png)

**Zoe artwork — Lots of Ellipses:** original geometry inspired by the GEM ellipse family, stochastic fields, and orbital traces.

![Zoe — Lots of Ellipses](./assets/zoe-lots-of-ellipses.png)

</details>

## Reproduce

The saved artifacts support verification without fresh model inference:

```bash
python scripts/verify-model-comparison.py \
  --manifest evidence/comparison/model-comparison-cases.json \
  --evidence evidence/comparison

python scripts/verify-biobert-optimization.py
```

For the mirrored workbench, run the commands in [One mirrored brain](#one-mirrored-brain-many-instruments). See the [reproduction guide](./docs/REPRODUCTION.md) for optional model inference, rhythm experiments, and native X11 checks.

## Boundaries

- Accessible volume measures **forecast breadth under a model, context, and threshold**. It does not establish correctness, safety, human surprise, or general model quality.
- A forecast is not a causal repertoire. Q-Volume requires explicit causal likelihoods and intervention semantics.
- Finite sweeps approximate the complete continuous filtration. Zero volume change does not imply unchanged accessible-set membership.
- Mirrored evaluation is not a time-reversed model, consciousness measurement, or autonomous agent. `executed=False` remains the execution boundary.
- Hash-chained notebook memory provides tamper evidence, not authentication. The historical GEM glyph chart is a display codec, not encryption.

---

**Navigate:** [Documentation](./docs/README.md) · [Long-form research archive](./docs/RESEARCH.md) · [Evidence](./evidence/README.md) · [Algorithms](./docs/ALGORITHMS.md) · [Reproduction](./docs/REPRODUCTION.md)
