# The Significance of Accessible Volume

A self-contained research paper and reproducible evidence package by Nicholas Michael Kloster.

## Research record

The paper introduces accessible volume as a direct measurement of next-token breadth:

`A_tau(S) = sum over tokens a in O of 1[P(a | h_T(S)) > tau]`

It documents the research origin in the March 2026 Ambiguity Front-Loading and autoregressive compliance-cascade work, the March 4, 2026 discovery account, the Ablation + GEM implementation, and a real forward-pass measurement using 963 tokens of actual investigation evidence.

Measured result:

```text
tau:    0.02  0.05  0.10  0.15  0.30  0.50
A(S):      4     2     2     1     1     1
```

The measurement describes the concentration of a particular next-token distribution. It does not establish correctness, safety, attack intent, or multi-step behavior.

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
- Timestamp: `2026-11-03T21:37:18Z`
- Payload SHA-256: `013aa710cc52bf1ef7168e9e9fcac906d2420d7d0969ddde426cea5b6eb27467`
- Signed source SHA-256: `c0d20666142db000197b7bf44f5172a9ca3f148ac2a906bf096741daa3e4b347`

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

The two Foundation-Sec-8B variants were not run: each repository contains approximately 16.06 GB of weights, exceeding the available test-system storage. No experimental claim is made about those models.

The signing certificate is self-signed. It provides document integrity, not military, Anthropic, or third-party identity validation. No private key is included.
