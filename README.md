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
- `evidence/` — original research documents, reports, entropy output, model configuration, and complete input IDs and probability vectors

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
- Timestamp: `2026-11-03T20:07:55Z`
- Payload SHA-256: `d453ad69d807d334abd1fd4805bfeee99c3d9beea1b049e1bd1731c8825ef5d2`

## Implementation and reproducibility

Ablation is the reverse-engineering framework. GEM (Graphics Environment Manager) is its shared graphical investigation environment. See https://github.com/Ablation-Tool/ablation.

The measurement harness downloads and verifies the public `distilgpt2-ONNX` model, creates actual Lab evidence, performs a forward pass, computes the threshold sweep, and reloads the saved probability vector for verification. The context-sensitivity harness repeats the baseline and evaluates controlled context changes.

The signing certificate is self-signed. It provides document integrity, not military, Anthropic, or third-party identity validation. No private key is included.
