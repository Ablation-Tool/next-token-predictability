# Next-Token Predictability: Accessible Volume

This research introduces a new way to measure next-token predictability: accessible volume reveals the alternatives and uncertainty hidden by the final token. In a 120-case BioBERT study, the original equation outperformed a more complex adaptive equation on unseen data—showing the value of a simple, direct measurement of prediction uncertainty.

**[The Significance of Accessible Volume](./accessible-volume-significance-signed.pdf)**

## What the equation shows

Before a language model produces a token, it assigns probabilities to the alternatives. A token can be a word or part of a word.

Imagine the model is deciding the next token in “The cat sat on the ___.” Its forecast might be:

```text
mat:   70%
floor: 20%
roof:   8%
rug:    2%
```

With a 5% probability cutoff, three alternatives are above the threshold. Accessible volume is therefore 3:

`A_tau(S) = sum over tokens a in O of 1[P(a | h_T(S)) > tau]`

In plain language, **the equation counts how many next-token alternatives remain above a chosen probability cutoff**:

- A **small count** means the choice is concentrated on a few alternatives.
- A **large count** means many alternatives remain plausible.

The final text shows which token was selected. Accessible volume shows how open or settled that decision was **before selection**. It turns the model’s hidden forecast into a number we can measure, compare, preserve, and use to study prediction errors.

## Main finding: the original equation worked better

We tested 120 biomedical masked-token cases from 30 documents. First, we measured the original equation. Then we optimized an adaptive version using only development and validation data:

`A_alpha_beta(x) = sum over tokens i of 1[p_i > max(beta, alpha * p_max(x))]`

Instead of always using the same cutoff, this version adjusts the cutoff using the largest probability in each distribution. The selected parameters were `alpha = 0.001` and `beta = 0`.

We then compared both equations on 40 unseen test cases. The task was to distinguish incorrect top predictions from correct ones using prediction breadth as the uncertainty score:

| Error-detection metric | Original equation, 5% cutoff | Optimized adaptive equation |
|---|---:|---:|
| AUROC | **0.7737** | 0.7008 |
| AUPRC | **0.7978** | 0.6599 |

AUROC has a direct interpretation: when comparing one incorrect and one correct prediction, the original equation ranked the incorrect one as more uncertain about **77.4%** of the time, versus **70.1%** for the adaptive equation. AUPRC is another measure of how well errors rank above correct predictions; higher is better.

The original rule improved AUROC by **0.0729** and AUPRC by **0.1379**. The adaptive equation looked better during validation but did not retain that advantage on unseen documents.

**This is the significance of the finding:** accessible volume exposes structure in the model’s decision that the final token cannot show, and the simplest version of the equation provided the strongest measured separation between correct and incorrect predictions. More complexity did not produce a better measurement.

## Evidence behind the finding

The original Ablation + GEM experiment used 963 tokens of real investigation context:

```text
tau:    0.02  0.05  0.10  0.15  0.30  0.50
A(S):      4     2     2     1     1     1
```

XLNet Base and Large produced these measurements with the target hidden from the query stream:

```text
Variant       0.02  0.05  0.10  0.15  0.30  0.50
XLNet-Base       6     2     1     1     1     1
XLNet-Large      5     4     2     1     0     0
```

Three target-token replacements produced bit-identical distributions for each XLNet model. A separate offline Base reproduction preserved the threshold counts and token ordering, with a maximum absolute probability difference of `2.3443287986563144e-07`.

Sixteen additional models cover general language, security, biomedicine, clinical text, science, law, finance, code, multilingual text, chemistry, protein sequences, and mathematics. Their complete distributions, input IDs, revisions, and reports are preserved and independently verified in the [cross-model summary](./evidence/comparison/model-comparison-summary.json).

## Research context

The work builds on the March 2026 [Ambiguity, Autonomy, and the Autoregressive Compliance Cascade](./evidence/Ambiguity_Autonomy_Compliance_Cascade.pdf) research and the [March 4, 2026 discovery account](./evidence/afl_disclosure.docx.pdf).

[Ablation](https://github.com/Ablation-Tool/ablation) is the reverse-engineering framework. GEM (Graphics Environment Manager) is its shared graphical investigation environment. The experiments use real model probability distributions and preserve the evidence needed to reproduce every reported result.

## Reproduce the BioBERT finding

The frozen inputs are in [`evidence/biobert-optimization/cases.json`](./evidence/biobert-optimization/cases.json). Each source document remains entirely within its assigned development, validation, or test split.

Use Python 3.14 with NumPy 2.5.3, PyTorch 2.14.1, Transformers 4.56.2, Tokenizers 0.22.1, SentencePiece 0.2.2, and Hugging Face Hub 0.36.2:

```bash
python biobert-accessible-volume.py
python optimize-biobert.py
python verify-biobert-optimization.py
```

- `biobert-accessible-volume.py` reproduces the 120 original-equation distributions and reports.
- `optimize-biobert.py` selects adaptive parameters using only development and validation data, then evaluates the frozen choice on the test documents.
- `verify-biobert-optimization.py` independently recomputes every AUROC and AUPRC value.

The complete results are available in [optimization.json](./evidence/biobert-optimization/optimization.json) and [optimization-scores.csv](./evidence/biobert-optimization/optimization-scores.csv).

To reproduce the sixteen additional model comparisons:

```bash
python run-model-comparison.py --all --output evidence/comparison --cache-dir /path/to/temporary-cache
python verify-model-comparison.py
```

## Verify the paper

Build the paper twice:

```bash
pdflatex accessible-volume-significance.tex
pdflatex accessible-volume-significance.tex
```

Verify the full-document signature and the 305 embedded evidence files:

```bash
pdfsig -nocert -no-ocsp accessible-volume-significance-signed.pdf
pdfdetach -list accessible-volume-significance-signed.pdf
```

Verify the source against [accessible-volume-significance.signature.json](./accessible-volume-significance.signature.json). The certificate is self-signed; it provides document integrity, not third-party identity validation. No private key is included.

## Key documents and evidence

- [Signed research paper](./accessible-volume-significance-signed.pdf)
- [LaTeX source](./accessible-volume-significance.tex)
- [BioBERT optimization results](./evidence/biobert-optimization/optimization.json)
- [BioBERT per-case scores](./evidence/biobert-optimization/optimization-scores.csv)
- [Frozen BioBERT cases and splits](./evidence/biobert-optimization/cases.json)
- [Cross-model results](./evidence/comparison/model-comparison-summary.json)
- [Model revisions and input cases](./model-comparison-cases.json)

## Contributor

- **[Nicholas Michael Kloster (Ablation-Tool)](https://github.com/Ablation-Tool)** — Research, accessible-volume equation, experiments, paper, evidence, and project maintenance. Signatory ID: 1505393089.

## License

This project is available under the [MIT License](./LICENSE). Third-party source documents retain their original licenses; see the [source metadata](./evidence/biobert-optimization/sources.json).
