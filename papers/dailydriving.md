## Current Settings

These are the LM Studio load settings I am using for Qwen 3.8 27B.

### Context and offload

- **Context length:** 262144, which is the model's maximum
- **GPU offload:** 65. LM Studio limits offload to dedicated GPU memory, so the number of layers that actually land on the GPU can be lower than this
- **Offload KV cache to GPU memory:** on
- **Keep model in memory:** on
- **Try mmap():** on

### Advanced

- **CPU thread pool size:** 4
- **Evaluation batch size:** 2048
- **Physical batch size:** 512
- **Max concurrent predictions:** 4
- **Unified KV cache:** on
- **Context checkpoints:** 32. At 32 this uses a lot of system memory. Setting it to 8 pretty much alleviates that. Setting it to 4 may be a longer-term solution.

### Speculative decoding

- **Method:** DFlash
- **Draft model:** Qwen3.8 27B DFlash2 Q8_0 (2.06 GB)
- **Max draft tokens:** 3
- **Min draft tokens:** 0
- **Draft probability:** 0

### Attention and cache

- **Flash Attention:** on
- **K cache quantization:** Q8_0
- **V cache quantization:** Q8_0

## Speeds

These numbers are from one coding session on this load. The model was `qwen/qwen3.8-27b` in LM Studio, with the 262144 context window above. The task was a single-file Tetris game. It took 45 model steps and about 49 minutes. About 41 minutes of that was token generation. The model produced 81,609 output tokens.

Generation speed is output tokens divided by the time from the first token to the end of that step. Longer replies, 1,000 output tokens or more:

| Prompt tokens | Output tokens | Speed |
| ---: | ---: | ---: |
| 14,175 | 50,783 | 39.0 tok/s |
| 65,198 | 4,734 | 28.9 tok/s |
| 69,980 | 5,336 | 29.4 tok/s |
| 76,417 | 2,997 | 26.1 tok/s |
| 81,231 | 3,932 | 25.7 tok/s |
| 86,769 | 1,662 | 28.8 tok/s |
| 100,436 | 1,450 | 21.1 tok/s |
| 102,009 | 1,007 | 23.3 tok/s |

The first reply is the fast one: 50,783 tokens at 39.0 tok/s while the prompt was still 14,175 tokens. Past 100,000 prompt tokens, long replies sit around 21 to 23 tok/s. Shorter steps bounce between about 22 and 35 tok/s, which is noisy because a 66-token reply does not average well. Weighted across every output token, the session ran at 33.4 tok/s, mostly because that first reply was so large.

Prompt speed below is new prompt tokens divided by time to first token. Only steps that added at least 1,000 tokens are listed. Short follow-ups that added a few dozen tokens usually started in 3 to 5 seconds, so those are cache hits, not a prefill rate.

| Context after the add | New tokens | Time to first token | Prompt speed |
| ---: | ---: | ---: | ---: |
| 14,175 | 14,175 | 24.7 s | 574 tok/s |
| 65,011 | 50,836 | 135.8 s | 374 tok/s |
| 69,980 | 4,782 | 18.7 s | 256 tok/s |
| 75,364 | 5,384 | 22.1 s | 244 tok/s |
| 79,451 | 3,034 | 14.0 s | 217 tok/s |
| 85,202 | 3,971 | 18.2 s | 218 tok/s |
| 88,528 | 1,759 | 9.5 s | 186 tok/s |
| 94,237 | 4,005 | 27.9 s | 143 tok/s |
| 98,661 | 4,020 | 28.8 s | 140 tok/s |
| 102,009 | 1,573 | 10.6 s | 148 tok/s |
| 103,053 | 1,044 | 7.8 s | 134 tok/s |

The expensive prompt was the second step. Context jumped from 14k to 65k, mostly the first reply being folded into the next prompt, and the model sat for 136 seconds before the first token. After that, a few thousand new tokens on a 70k to 85k prompt processed at about 220 to 260 tok/s. Near 100k, the same kind of add fell to about 130 to 150 tok/s.

## Context and reasoning

The window is 262,144 tokens. The last step started at 106,963 input tokens and finished at 107,418 total, which is 41% of the window.

That 106,963 split three ways:

- **14,175** tokens of the original prompt (13%)
- **81,154** tokens of earlier model output (76%)
- **11,634** tokens of tool results and other added context (11%)

The usage record does not separate reasoning tokens from answer tokens. Of the characters in the model output still sitting in that prompt, 66.9% was reasoning, 31.8% was tool-call arguments, and 1.4% was text for the user. Scaled onto the 81,154 output tokens, that is about 54,300 reasoning tokens, 25,800 tool-call tokens, and 1,100 visible-text tokens. Reasoning was about half of the 106,963-token prompt.

The first step alone wrote 50,783 output tokens and no user-visible text. It was reasoning plus one tool call.