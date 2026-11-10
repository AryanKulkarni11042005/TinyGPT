### TinyGPT


| Item | Value |
| :--- | :--- |
| **Dataset** | TinyStories (HF roneneldan/TinyStories) |
| **Tokenizer** | Byte-level BPE, vocab 4096, retrained from GPT-2’s |
| **Train** | 2,119,719 stories → 485.8M tokens (972 MB) |
| **Val** | 21,990 stories → 4.88M tokens (9.8 MB) |
| **Avg tokens/story** | ~229 |

| Model | Validation Loss |
| :---  | :--- |
| **Bigram Model** | 3.99 |
| **Multi-Head Attention** | 3.82 | 

Hyper-parameters & Variables:
- Learning Rate = 1e-2
- Epochs = 3000
- Batch Size = 32
- Block Size = 8
- Vocab Size = 4096 

Next Steps: Feed Forward Layer, Layer Norm, Residual Connections
Target is 10-12M parameter model