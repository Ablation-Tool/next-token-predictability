# Next-Token Predictability: Accessible Volume

A self-contained research paper and reproducible evidence package by Nicholas Michael Kloster.

**[Read the signed research paper (PDF)](./accessible-volume-significance-signed.pdf)**

## What the equation shows

Before a language model produces a token, it assigns probabilities to the alternatives. A token can be a word or part of a word.

Imagine the model is deciding the next token in “The cat sat on the ___.” Its forecast might be:

```text
mat:   70%
floor: 20%
roof:   8%
rug:    2%
```

If we set the probability cutoff to 5%, three alternatives are above it. Accessible volume is therefore 3:

`A_tau(S) = sum over tokens a in O of 1[P(a | h_T(S)) > tau]`

In plain language, **the equation counts how many next-token alternatives remain above a chosen probability cutoff**. This makes next-token predictability measurable:

- A **small count** means the choice is concentrated on a few alternatives.
- A **large count** means many alternatives remain plausible.

The final text shows which token was selected. Accessible volume shows how open or settled that decision was **before selection**. That is its significance: it turns the model’s hidden forecast into a number we can measure, compare, preserve, and use to study prediction errors.

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

The original rule improved AUROC by **0.0729** and AUPRC by **0.1379**. The adaptive equation looked better during validation but did not retain that advantage on unseen documents. **The simpler original equation was the stronger way to measure prediction uncertainty in this experiment.**

## Research record

The paper documents the research origin in the March 2026 Ambiguity Front-Loading and autoregressive compliance-cascade work, the March 4, 2026 discovery account, the Ablation + GEM implementation, and a real forward-pass measurement using 963 tokens of actual investigation evidence.

Original Ablation + GEM measurement:

```text
tau:    0.02  0.05  0.10  0.15  0.30  0.50
A(S):      4     2     2     1     1     1
```

## Contents

- `accessible-volume-significance.tex` — signed LaTeX source
- `accessible-volume-significance.pdf` — paper with embedded evidence
- `accessible-volume-significance-signed.pdf` — PKCS #7 detached signed PDF
- `accessible-volume-significance.p7s` — detached CMS signature
- `accessible-volume-significance-signing-cert.pem` — public signing certificate
- `accessible-volume-significance.signature.json` — source and evidence hashes
- `ablation-gem-measure.py` — baseline measurement harness
- `ablation-gem-context-sweep.py` — controlled context experiments
- `ablation_gem_artifacts.py` — model and artifact utilities
- `xlnet-accessible-volume.py` — XLNet Base and Large query-stream measurements
- `model-comparison-cases.json` — exact model revisions, contexts, targets, and prediction styles for 16 additional models
- `prediction-accessible-volume.py` — masked-token and causal-token measurement harness
- `run-model-comparison.py` — sequential, pinned-model reproduction runner
- `verify-model-comparison.py` — independent raw-artifact verifier and summary generator
- `prepare-biobert-cases.py` — frozen biomedical source and target selection
- `biobert-accessible-volume.py` — 120-case original-equation baseline
- `optimize-biobert.py` — development/validation parameter selection and held-out evaluation
- `verify-biobert-optimization.py` — independent AUROC and AUPRC recomputation
- `biobert-attachments.tex` — declarations for all 240 per-case BioBERT attachments
- `evidence/` — original research documents, all model reports, complete input IDs and probability vectors, and aggregate JSON and CSV summaries

## Verify

Build the paper twice with LaTeX:

```bash
pdflatex accessible-volume-significance.tex
pdflatex accessible-volume-significance.tex
```

Verify the PDF signature and embedded files:

```bash
pdfsig -nocert -no-ocsp accessible-volume-significance-signed.pdf
pdfdetach -list accessible-volume-significance-signed.pdf
```

Verify source integrity by hashing the LaTeX payload before `\section{Source signature}` and comparing it with `accessible-volume-significance.signature.json`. The record identifies:

- Signer: `Nicholas Michael Kloster [1505393089]`
- Timestamp: `2026-11-03T22:58:38Z`
- Payload SHA-256: `d8fe3401f6fcf1a710451d07a893ab61243cdf7a0f85de6e1743df051b21eaf3`
- Signed source SHA-256: `1f6fa4d8cfc34b3b102c2a3c62ecf109d85b206aa3fcaf4240e55a8549a94a8b`

## Implementation and reproducibility

Ablation is the reverse-engineering framework. GEM (Graphics Environment Manager) is its shared graphical investigation environment. See https://github.com/Ablation-Tool/ablation.

The measurement harness downloads and verifies the public `distilgpt2-ONNX` model, creates actual Lab evidence, performs a forward pass, computes the threshold sweep, and reloads the saved probability vector for verification. The context-sensitivity harness repeats the baseline and evaluates controlled context changes.

The XLNet extension uses `xlnet/xlnet-base-cased` and `xlnet/xlnet-large-cased` with the target token masked out of query-stream attention. Three different replacement token IDs produce bit-identical probability distributions for each model, demonstrating that the target identity does not enter those predictions.

```text
Variant       0.02  0.05  0.10  0.15  0.30  0.50
XLNet-Base       6     2     1     1     1     1
XLNet-Large      5     4     2     1     0     0
```

A separate offline Base reproduction matched the input IDs, threshold counts, and target-token ordering. Its maximum absolute probability difference was `2.3443287986563144e-07`; it was numerically close, not bit-identical across runtimes. The complete XLNet script, Base and Large JSON reports, probability vectors, input IDs, and reproduction comparison are included in `evidence/` and embedded in the signed PDF.

## Sixteen additional model comparisons

The paper also measures BERT-Base, RoBERTa-Base, ALBERT-Base, GPT-2, SecureBERT, CySecBERT, BioBERT, Bio_ClinicalBERT, SciBERT, Legal-BERT, FinBERT, CodeBERT, XLM-RoBERTa-Base, ChemBERTa, ProtBERT, and Qwen2.5-Math-1.5B. Every requested domain is covered. Exact model revisions, targets, and input contexts are frozen in `model-comparison-cases.json`.

```text
Category       Model                           0.02 0.05 0.10 0.15 0.30 0.50
General        BERT-Base                          2    1    1    1    1    1
General        RoBERTa-Base                       2    2    1    1    1    1
General        ALBERT-Base                        5    1    1    1    1    0
General        GPT-2 (causal)                     7    3    2    1    0    0
Security       SecureBERT                         6    4    2    0    0    0
Security       CySecBERT                          2    0    0    0    0    0
Biomedical     BioBERT                            1    0    0    0    0    0
Clinical       Bio_ClinicalBERT                   6    2    2    1    0    0
Scientific     SciBERT                            6    3    2    0    0    0
Legal          Legal-BERT                         7    2    1    1    1    0
Financial      FinBERT                            5    3    2    2    0    0
Programming    CodeBERT                           4    3    1    1    1    0
Multilingual   XLM-RoBERTa-Base                   6    3    1    0    0    0
Chemistry      ChemBERTa                          8    5    0    0    0    0
Protein        ProtBERT                          18    9    0    0    0    0
Mathematics    Qwen2.5-Math-1.5B (causal)         9    8    4    2    0    0
```

All sixteen raw artifacts passed independent recomputation of probability sums, hashes, threshold counts, ranks, entropy, top-ten mass, and token ordering. These are individual distribution measurements, not a model-quality, correctness, safety, or security leaderboard.

To reproduce the comparisons, use Python 3.14 with NumPy 2.5.3, PyTorch 2.14.1, Transformers 4.56.2, Tokenizers 0.22.1, SentencePiece 0.2.2, and Hugging Face Hub 0.36.2. The XLNet experiments use a separate environment with Transformers 5.18.0.

```bash
python run-model-comparison.py --all --output evidence/comparison --cache-dir /path/to/temporary-cache
python verify-model-comparison.py
```

The runner downloads public models sequentially and cleans only its own temporary model caches. Use `--model` with an exact model ID from the manifest to run one case. The verifier checks the included raw artifacts without downloading weights.

## Reproduce the BioBERT finding

The frozen inputs are in `evidence/biobert-optimization/cases.json`. The 30 source documents remain together in their assigned development, validation, or test split. Use the same Python and model-runtime versions listed above, then run:

```bash
python biobert-accessible-volume.py
python optimize-biobert.py
python verify-biobert-optimization.py
```

The first command reproduces all 120 original-equation distributions and reports. The second selects the adaptive parameters using only development and validation data, then evaluates the frozen choice on the test documents. The third independently verifies every AUROC and AUPRC value. The included `baseline-summary.json`, `optimization.json`, and `optimization-scores.csv` contain the complete findings; all 120 raw distributions and reports are also included.

The two Foundation-Sec-8B variants were not run: each repository contains approximately 16.06 GB of weights, exceeding the available test-system storage. No experimental claim is made about those models.

The signing certificate is self-signed. It provides document integrity, not military, Anthropic, or third-party identity validation. No private key is included.

## Contributors

- **[Ablation-Tool](https://github.com/Ablation-Tool)** — Repository owner, project development, Ablation + GEM integration, and maintenance.
- **Nicholas Michael Kloster [1505393089]** — Accessible-volume research, equation, experiments, paper, and evidence package.
