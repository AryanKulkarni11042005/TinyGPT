### TinyGPT

A ~12M-parameter decoder-only transformer written from scratch in PyTorch and trained on TinyStories, followed by inference optimizations (KV cache, INT8 quantization, batched generation) with benchmarks.

#### Data

| Item | Value |
| :--- | :--- |
| **Dataset** | TinyStories (HF roneneldan/TinyStories) |
| **Tokenizer** | Byte-level BPE, vocab 4096, retrained from GPT-2’s |
| **Train** | 2,119,719 stories → 485.8M tokens (972 MB) |
| **Val** | 21,990 stories → 4.88M tokens (9.8 MB) |
| **Avg tokens/story** | ~229 |

#### Model Runs

Small runs (rows 1–5) use batch size 32, block size 8, n_embd 32. Differences under ~0.05 val loss are within evaluation noise at this size.

| Model | Params | Validation Loss | Perplexity | Learning Rate | Steps |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Bigram Model** | — | 3.99 | ~54 | 1e-2 | 3000 |
| **Multi-Head Attention** | — | 3.82 | ~46 | 1e-2 | 3000 |
| **Multi-Head Attention with Feed Forward** | — | 3.84 | ~47 | 1e-2 | 5000 |
| **Multi-Head Attention with Residual Connections and FFWD** | — | 3.77 | ~43 | 1e-2 | 5000 |
| **Multi-Head Attention with Residual Connections and LayerNorm** | — | 3.66 | ~39 | 1e-2 | 5000 |
| **Scaled GPT (final model)** | **12.31M** | **1.58** | **~4.9** | 6e-4 peak, cosine → 6e-5 | 24,400 |

#### Final Model

| Item | Value |
| :--- | :--- |
| **Architecture** | 6 layers, 6 heads (head size 64), n_embd 384, context 256 |
| **Extras** | Pre-norm LayerNorm, residuals, final LayerNorm, weight tying, GPT-2 init (std 0.02), dropout 0.0 |
| **Parameters** | 12.31M (hand estimate V·d + 12·L·d² + T·d = 12.29M) |
| **Optimizer** | AdamW (β 0.9/0.95, weight decay 0.1), grad clip 1.0, 200-step warmup + cosine decay |
| **Batch** | 32 × 256 = 8,192 tokens/step |
| **Tokens trained** | ~200M (~0.41 epochs) |
| **Precision** | bf16 autocast |
| **Hardware** | NVIDIA RTX 3050 4GB Laptop GPU |
| **Training throughput** | ~23.6k tokens/sec |
| **Training time** | ~2.5 hours |

Train and val loss tracked closely for the whole run (final train 1.58 / val 1.59), so there was no overfitting.

#### Sample (from `<|endoftext|>`, 200 tokens)

> Ben and Lily are twins. They like to play outside. They have a big wagon. They load the wagon with toys. […] But soon they start to argue. They both want the toys. They pull the wagon. The wagon is broken. They cry. […] They hug each other. They decide to share the toys.

#### Next Steps
- [ ] KV cache: tokens/sec with and without cache at several output lengths
- [ ] INT8 per-channel weight quantization: memory, speed, perplexity before/after
- [ ] Batched generation: throughput vs batch size
- [ ] Results table + charts
