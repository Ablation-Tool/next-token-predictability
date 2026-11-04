# Evidence guide

This repository separates **verified accessible-volume measurements** from **historical research context**. A document's presence here does not make every claim in it a validated result.

## Verified measurements

These are the canonical artifacts for the current paper and reproduction procedures:

- [Ablation + GEM measurement](./ablation-gem-accessible-volume.json) and [complete next-token probabilities](./ablation-gem-next-token-probabilities.npz).
- [XLNet-Base report](./xlnet-hidden-token-accessible-volume.json) and [XLNet-Large report](./xlnet-large/xlnet-hidden-token-accessible-volume.json), with complete probability vectors and input IDs.
- [Sixteen-model comparison summary](./comparison/model-comparison-summary.json) and [frozen case manifest](../model-comparison-cases.json).
- [BioBERT optimization results](./biobert-optimization/optimization.json), [per-case scores](./biobert-optimization/optimization-scores.csv), and [frozen cases](./biobert-optimization/cases.json).
- [Signed paper](../accessible-volume-significance-signed.pdf) and [signature metadata](../accessible-volume-significance.signature.json).

The signed paper and its source-signature record are unchanged by the additional historical context below.

## Historical research context

The curated files in [`research-context/`](./research-context/) preserve direct development history without bulk-importing duplicate, unrelated, or sensitive material. [`source-manifest.json`](./research-context/source-manifest.json) records the source archives, original member names, and SHA-256 hashes.

| File | Why it belongs | Evidentiary status |
|:---|:---|:---|
| [Origin prompt](./research-context/origin-prompt.txt) | A short early request for word-level vector scores, an interpretable equation, and token-level intent evaluation. It supplies direct context for the measurement's development. | Development record, not a model result. |
| [Early equations](./research-context/early-equations.txt) | An early compact statement of the strict thresholded accessible set, hidden-state composition, and related sensitivity ideas. | Historical draft. The canonical equation is Equation (1) in the current paper. |
| [Autonomy framework draft](./research-context/autonomy-framework-draft.txt) | The early named framework connecting thresholded output volume, input context, entropy, and alignment measurements. | Historical conceptual draft. It explicitly lacks empirical evaluation, and its universal nonzero-sensitivity claim is not established by the current experiments. |
| [Compliance-cascade subsection](./research-context/cascade-subsection.txt) | Explains the proposed relationship between generated tokens, changing context, and gradual compliance-trajectory growth. | Theoretical trajectory analysis, not proof that a single-step count detects a cascade. |
| [Early experiment harness](./research-context/autonomy_empirical.py) and [proposed protocol](./research-context/EXPERIMENT_README.md) | Implements the strict full-vocabulary next-token threshold count on open-weight causal models and proposes base-versus-instruct comparisons. | Code and protocol only. The archive contains no run artifacts from this harness, so it is not evidence that these proposed validations succeeded. Use the current reproduction scripts for verified results. |
| [AFL ambiguity/directive diagram](./research-context/afl-ambiguity-directive-zones.png) | Visualizes the ambiguous-prefix and clear-directive interpretation that motivated the original AFL investigation. | Conceptual illustration, not a measured probability distribution. |

Archive timestamps are preserved as provenance metadata, not as proof of first invention or creation. The author's stated creation date for the accessible-volume equation remains **March 6, 2026**.

### What was deliberately excluded

- **Duplicate and superseded documents:** repeated PDFs, renamed copies, earlier disclosure versions, and nested ZIP duplicates were not added. The existing [March 2026 unified paper](./Ambiguity_Autonomy_Compliance_Cascade.pdf) already states that it supersedes previous submissions.
- **Illustrative numbers presented as examples:** use-case appendices and generated demonstrations were not promoted to empirical evidence. Verified numbers must come from the saved model distributions and reports above.
- **Live-target and credential-adjacent material:** operational penetration-test logs, target-specific exploitation walkthroughs, license-key recovery discussions, and related tooling were excluded from this public context pack.
- **Unrelated tooling:** SMTP/configuration-drift scripts, websocket tracers, generic reconnaissance utilities, and RXVM/license-analysis transcripts do not support the accessible-volume measurement.
- **Non-equivalent API sampling test:** `test_as_claude.py` estimates unique sampled words rather than the model's full next-token probability distribution. It does not measure the canonical equation and could also incur external API costs.
- **Bulk archives:** the original ZIPs are identified by SHA-256 in the source manifest but are not redistributed, avoiding duplication and unnecessary disclosure of excluded material.

## Tested-model sources

Nineteen unique public models were used across the Ablation + GEM measurement, two XLNet extensions, and sixteen additional comparisons. BioBERT appears in both the sixteen-model comparison and the subsequent 120-case study, so it is listed once.

**Model weights are not duplicated in this Git repository.** Exact public model IDs and immutable revisions are linked whenever the experiment recorded them. This avoids large binary redistribution, preserves each publisher's licensing and distribution channel, and still allows the reproduction scripts to fetch the correct artifacts. The saved probability vectors and independent verifiers do not require model downloads.

| Experiment | Model | Public source and revision | Saved evidence |
|:---|:---|:---|:---|
| Ablation + GEM | `onnx-community/distilgpt2-ONNX` | [Model page](https://huggingface.co/onnx-community/distilgpt2-ONNX). The experiment records [model SHA-256 `0f79f192…539c41`](./ablation-gem-accessible-volume.json) rather than a commit revision. | [Report](./ablation-gem-accessible-volume.json) |
| XLNet query stream | `xlnet/xlnet-base-cased` | [Cached revision `ceaa69c`](https://huggingface.co/xlnet/xlnet-base-cased/tree/ceaa69c7bc5e512b5007106a7ccbb7daf24b2c79) | [Report](./xlnet-hidden-token-accessible-volume.json) |
| XLNet query stream | `xlnet/xlnet-large-cased` | [Cached revision `37658b4`](https://huggingface.co/xlnet/xlnet-large-cased/tree/37658b4f179eaf971d127d16bfbd9ca676a93034) | [Report](./xlnet-large/xlnet-hidden-token-accessible-volume.json) |
| General language | `google-bert/bert-base-cased` | [Revision `cd5ef92`](https://huggingface.co/google-bert/bert-base-cased/tree/cd5ef92a9fb2f889e972770a36d4ed042daf221e) | [Report](./comparison/bert-base-cased-accessible-volume.json) |
| General language | `FacebookAI/roberta-base` | [Revision `e2da8e2`](https://huggingface.co/FacebookAI/roberta-base/tree/e2da8e2f811d1448a5b465c236feacd80ffbac7b) | [Report](./comparison/roberta-base-accessible-volume.json) |
| General language | `albert/albert-base-v2` | [Revision `8e2f239`](https://huggingface.co/albert/albert-base-v2/tree/8e2f239c5f8a2c0f253781ca60135db913e5c80c) | [Report](./comparison/albert-base-v2-accessible-volume.json) |
| General language, causal | `openai-community/gpt2` | [Revision `607a30d`](https://huggingface.co/openai-community/gpt2/tree/607a30d783dfa663caf39e06633721c8d4cfcd7e) | [Report](./comparison/gpt2-accessible-volume.json) |
| Security | `ehsanaghaei/SecureBERT` | [Revision `3a47918`](https://huggingface.co/ehsanaghaei/SecureBERT/tree/3a47918dd874e5c769efd152b51c5756a953fb67) | [Report](./comparison/securebert-accessible-volume.json) |
| Security | `markusbayer/CySecBERT` | [Revision `c9e04e3`](https://huggingface.co/markusbayer/CySecBERT/tree/c9e04e355a2bc0690b6e55833a9895d7591afcd0) | [Report](./comparison/cysecbert-accessible-volume.json) |
| Biomedical comparison and 120-case study | `dmis-lab/biobert-base-cased-v1.2` | [Revision `67c9c25`](https://huggingface.co/dmis-lab/biobert-base-cased-v1.2/tree/67c9c25b46986521ca33df05d8540da1210b3256) | [Comparison](./comparison/biobert-base-cased-v1.2-accessible-volume.json), [120-case results](./biobert-optimization/optimization.json) |
| Clinical | `emilyalsentzer/Bio_ClinicalBERT` | [Revision `d5892b3`](https://huggingface.co/emilyalsentzer/Bio_ClinicalBERT/tree/d5892b39a4adaed74b92212a44081509db72f87b) | [Report](./comparison/bio_clinicalbert-accessible-volume.json) |
| Scientific | `allenai/scibert_scivocab_cased` | [Revision `ddf0be0`](https://huggingface.co/allenai/scibert_scivocab_cased/tree/ddf0be025f8e432a1870e34811997ba6725bf04a) | [Report](./comparison/scibert_scivocab_cased-accessible-volume.json) |
| Legal | `nlpaueb/legal-bert-base-uncased` | [Revision `15b570c`](https://huggingface.co/nlpaueb/legal-bert-base-uncased/tree/15b570cbf88259610b082a167dacc190124f60f6) | [Report](./comparison/legal-bert-base-uncased-accessible-volume.json) |
| Financial | `yiyanghkust/finbert-pretrain` | [Revision `88ab954`](https://huggingface.co/yiyanghkust/finbert-pretrain/tree/88ab954a39ea6d3ce2b62cff086dd5ad1172c664) | [Report](./comparison/finbert-pretrain-accessible-volume.json) |
| Programming | `microsoft/codebert-base` | [Revision `3b0952f`](https://huggingface.co/microsoft/codebert-base/tree/3b0952feddeffad0063f274080e3c23d75e7eb39) | [Report](./comparison/codebert-base-accessible-volume.json) |
| Multilingual | `FacebookAI/xlm-roberta-base` | [Revision `e73636d`](https://huggingface.co/FacebookAI/xlm-roberta-base/tree/e73636d4f797dec63c3081bb6ed5c7b0bb3f2089) | [Report](./comparison/xlm-roberta-base-accessible-volume.json) |
| Chemistry | `seyonec/ChemBERTa-zinc-base-v1` | [Revision `761d6a1`](https://huggingface.co/seyonec/ChemBERTa-zinc-base-v1/tree/761d6a18cf99db371e0b43baf3e2d21b3e865a20) | [Report](./comparison/chemberta-zinc-base-v1-accessible-volume.json) |
| Protein | `Rostlab/prot_bert` | [Revision `7a89448`](https://huggingface.co/Rostlab/prot_bert/tree/7a894481acdc12202f0a415dd567f6cfdb698908) | [Report](./comparison/prot_bert-accessible-volume.json) |
| Mathematics, causal | `Qwen/Qwen2.5-Math-1.5B` | [Revision `4a83ca6`](https://huggingface.co/Qwen/Qwen2.5-Math-1.5B/tree/4a83ca6e4526a4f2da3aa259ec36c259f66b2ab2) | [Report](./comparison/qwen2.5-math-1.5b-accessible-volume.json) |

For reproduction, use the pinned IDs and revisions in [`model-comparison-cases.json`](../model-comparison-cases.json) and the commands in the main README. The two Foundation-Sec-8B variants mentioned in the paper were **not run** and therefore are not listed as tested models.
