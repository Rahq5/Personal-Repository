# LLM Inference

This note covers how a trained large language model (LLM) turns a prompt into text at serving time: the two phases (prefill and decode), the KV cache, batching, and the main speed-up techniques. It matters because inference is a recurring cost (you pay it on every request), and its speed is limited more by moving data in memory than by doing math, which changes how you size and design a serving system.

**What will be covered (your LearningFlow steps):**
- What is LLM inference
- Properties
- Assumptions & Expectations
- How it works (pipeline, KV cache, memory-bound decode, batching, PagedAttention, other optimizations, code)
- Use cases
- Pros & Cons
- Why we needed it (problem + history)
- Big-picture flow diagram, then references

**Source tags used below:** `[P]` primary (paper or official docs), `[S]` secondary (blog/tutorial, verify before trusting numbers), `[mine]` my own calculation or inference.

---

### 1. What is LLM Inference
> **LLM inference** = running an already-trained (frozen) model to generate output tokens from an input prompt. No weights are updated, unlike training.
> **Token** = a chunk of text (a word or word piece) that the model reads and writes. It is the model's unit of input and output.
> **Autoregressive** = each new token is generated based on all the tokens before it, so token N+1 cannot be produced before token N exists.

- **What it is:** the process of transforming a prompt into generated tokens. It runs in two phases: prefill (read the prompt) and decode (write the answer one token at a time). [S: Exoscale, Redis]
- **What it helps in:** it is the runtime behind chat assistants, code completion, summarization and RAG systems.
- **What it does:** takes prompt tokens, runs them through the transformer's layers, outputs a probability score (logits) for every possible next token, picks one (sampling), appends it, and repeats until a stop condition. [P: Orca paper describes the autoregressive loop]
- **What it offers:** a way to serve a huge model to many users at acceptable speed (latency) and cost. The "serving layer" (scheduler, memory manager, kernels) is where most of the engineering lives. Inference cost is recurring, while training is a one-time cost. [P: NVIDIA blog]

> **Logits** = raw scores, one per vocabulary token, before they become probabilities.
> **Sampling** = choosing the next token from those scores (greedy picks the top one; other strategies add randomness).

---

### 2. LLM Inference Properties
> **Design properties** = the built-in characteristics that shape every serving decision.

- **Sequential by nature:** generating K tokens takes K serial runs of the model. [P: Leviathan et al.]
- **Two phases with opposite bottlenecks:**
  - **Prefill:** all prompt tokens are processed in parallel; compute-heavy. [P: NVIDIA TensorRT-LLM docs, quoted in S: IntuitionLabs]
  - **Decode:** one token per step; memory-heavy (limited by how fast data moves from GPU memory). [P: Sarathi paper]
- **Stateful per request:** each request carries a KV cache (the stored keys/values of previous tokens). 
- **Two big memory consumers:** model weights and the KV cache. [P: NVIDIA blog]
- **KV cache grows linearly** with sequence length, so long contexts and many concurrent users eat memory fast. [P: arXiv 2602.00426]
- **Unknown output length:** the server cannot know in advance when a request will finish, which is why scheduling and memory allocation are hard. [P: Orca, vLLM papers]
- **Latency vs throughput trade-off:** bigger batches raise total tokens/second but can slow each individual user. [S: BentoML, Databricks docs]
- **Exact vs approximate optimizations:**
  - Do not change outputs: FlashAttention, PagedAttention, continuous batching, speculative decoding (same output distribution).
  - May change quality: quantization, KV-cache compression/eviction.

> **Bottleneck** = the slowest part of a system, which limits the speed of everything else.
> **Trade-off** = improving one thing makes another thing worse.

**Metrics you must know** [S: BentoML, Databricks docs [P], Clarifai]:

| Metric | Meaning | Mostly driven by |
|---|---|---|
| TTFT (time to first token) | wait until the first token appears | prefill + queueing |
| TPOT / ITL (time per output token / inter-token latency) | gap between consecutive tokens | decode |
| End-to-end latency | `TTFT + TPOT × number_of_output_tokens` | both |
| Throughput (tokens/s) | total tokens served per second across all users | batching, memory |
| Goodput | requests/s that actually meet the latency targets (SLOs) | everything |

> **SLO (service-level objective)** = a latency/availability target you promise, e.g. "TTFT under 500 ms for 95% of requests".

---

### 3. LLM Inference Assumptions & Expectations
**Assumptions the design makes:**
- The model is a decoder-only autoregressive transformer with fixed weights.
- The prompt is known up front (so it can be processed in parallel), but the output length is not.
- Requests arrive at random times, so batches must change on the fly.
- The GPU has a small amount of very fast memory and a large amount of slower memory (HBM). Reading from HBM is the expensive part.
- Many requests share the same weights, so reading the weights once can serve many requests (this is what batching exploits).

> **HBM (High Bandwidth Memory)** = the GPU's large main memory; it holds weights and the KV cache.
> **SRAM** = tiny, very fast memory on the GPU chip itself.

**What you should expect from each technique** (numbers come from different models, hardware and baselines, so never add them up or compare them directly):

| Technique | Reported outcome | Quality impact |
|---|---|---|
| KV cache | per-token attention work drops from roughly O(N²) recompute to O(N) [S: MachineLearningMastery] | none, but costs memory |
| Continuous batching (Orca) | 36.9× throughput vs NVIDIA FasterTransformer at the same latency, GPT-3 175B [P] | none |
| PagedAttention (vLLM) | 2–4× throughput vs FasterTransformer and Orca; near-zero KV-cache waste [P] | none |
| FlashAttention | up to 3× faster than standard attention for sequence lengths 128–2K [P] | none (exact) |
| Speculative decoding | 2–3× speed-up on T5-XXL with identical outputs [P] | none |
| Static KV-cache + `torch.compile` | up to 4× on the forward pass only, not the whole `generate()` [P: HF docs] | none |
| Quantization | smaller memory, faster memory-bound decode [P: HF docs] | accuracy may drop |

> **Throughput improvement "at the same latency"** = more total work done without making each request slower.

⚠ **Benchmark rule:** results are only comparable when model, GPU, batch size, context length, precision and concurrency are all held constant. [S: IntuitionLabs]

---

### 4. How LLM Inference Works / Operates
### 4.1 The pipeline in one view
1. **Tokenize** the prompt text into token IDs.
2. **Schedule**: the server decides which requests join the next GPU step.
3. **Prefill**: process the whole prompt in one parallel pass, write the KV cache, produce the first token.
4. **Decode loop**: generate one token per step, reusing the KV cache, until a stop token or max length.
5. **Detokenize** and stream text to the user; free the request's memory.

> **EOS (end-of-sequence) token** = a special token the model emits to say "I'm done".

### 4.2 Prefill (reading the prompt)
- All prompt tokens go through the model at once, so the work is big matrix-by-matrix math that keeps the GPU's compute units busy. [S: MachineLearningMastery, Exoscale]
- Side effect that matters: the keys and values for every prompt token at every layer are computed and stored in the KV cache. [S: Exoscale]
- Output: logits for the first new token. Time spent here is most of your TTFT.

### 4.3 Decode (writing the answer) and the KV cache
- Each step handles one new token: compute its query/key/value, append the new key and value to the cache, attend (compare) the new query against all cached keys. [S: MachineLearningMastery]
- Without the cache, every step would recompute keys/values for the whole history. With it, only the new token's work is added.
- Only keys and values are cached, not queries. [S: MachineLearningMastery]

```python
# Illustration of the loop (pseudo-code, [mine])
tokens = tokenize(prompt)
logits, kv_cache = model.prefill(tokens)         # parallel, writes cache
next_tok = sample(logits[-1])
out = [next_tok]
while next_tok != EOS and len(out) < max_new_tokens:
    logits, kv_cache = model.decode_step(next_tok, kv_cache)  # 1 token, reads ALL weights + cache
    next_tok = sample(logits[-1])
    out.append(next_tok)
```

**KV cache size formula** `[mine]`:

```python
# bytes per token = 2 (K and V) * layers * kv_heads * head_dim * bytes_per_value
layers, kv_heads, head_dim, bytes_fp16 = 32, 32, 128, 2      # ASSUMED 7B-class config
per_token = 2 * layers * kv_heads * head_dim * bytes_fp16     # 524,288 B = 512 KiB
# seq=4096,  batch=1 -> 2 GiB
# seq=4096,  batch=8 -> 16 GiB
# seq=32768, batch=1 -> 16 GiB
```
(Models that use fewer KV heads, such as grouped-query attention (GQA), shrink this number. I computed the above myself; it is a sizing illustration, not a measured result.)

### 4.4 Why decode is memory-bound
> **Arithmetic intensity** = FLOPs (math operations) done per byte read from memory.
> **Memory-bound** = speed is limited by data movement. **Compute-bound** = speed is limited by math capacity.
> **Ridge point** = the arithmetic intensity at which a GPU switches from memory-bound to compute-bound (peak FLOP/s ÷ peak memory bandwidth).

- Decode at batch size 1 does a matrix-vector multiply: about 1 FLOP per byte, far below the ridge point, so the GPU mostly waits for weights to arrive. [S: dev.to roofline article]
- Measured evidence: in the Sarathi paper (LLaMA-13B on an A6000), per-token decode cost was 200×, 100× and 16.7× the per-token prefill cost at batch sizes 1, 2 and 18, and decode only became compute-intensive at a very large batch of 256. [P]
- Rule of thumb: decode tokens/s ≈ memory bandwidth ÷ bytes of weights. [S: viz.osteele.com]

```python
# Illustrative upper bound, batch=1 ([mine], ASSUMED numbers; ignores KV cache reads and overhead)
weights_GB = 70e9 * 2 / 1e9        # 70B params in FP16 = 140 GB
bandwidth_GBps = 3000              # assume ~3 TB/s HBM
print(bandwidth_GBps / weights_GB) # ~21 tokens/s
# 4-bit weights: 35 GB -> ~86 tokens/s bound (why quantization helps decode)
```

⚠ **Sources disagree on the exact ridge point** (about 295 FLOP/byte for H100 in one source, about 590 in another, 150–255 for A100 in others). The gap is consistent with different peak-FLOPS figures (precision, dense vs sparse), but I did not verify which; check the GPU's official datasheet before quoting a number.

**Consequence:** batching raises decode's arithmetic intensity, because one read of the weights is amortized across many requests. [S: dev.to, theorempath]

> **Amortize** = spread one fixed cost over many items so each item pays less.

### 4.5 Batching: static → continuous
- **Static (request-level) batching:** the server runs a batch until every request in it is finished. Early finishers cannot return, and new requests wait for the whole batch. [P: Orca paper]
- **Continuous batching** (Orca calls it *iteration-level scheduling*): the scheduler decides at every single model step, so finished requests leave and waiting ones join immediately. [P: Orca]
- **Selective batching:** batch only the operations that can be batched across different-length requests; handle attention per request. [P: Orca abstract]
- Same idea, different names: continuous batching (vLLM, Anyscale), in-flight batching (TensorRT-LLM), persistent batching (LMDeploy). [S: PremAI, Morph]

> **Head-of-line blocking** = a slow item at the front of a queue delays everything behind it.
> **Padding** = filler tokens added so sequences in a batch have equal length; wasted compute.

### 4.6 PagedAttention (KV cache memory management)
- Problem: older servers stored each request's KV cache in one contiguous memory block sized for the worst case, causing reserved-but-unused space and fragmentation. [P: vLLM paper]
- Idea (borrowed from operating-system virtual memory): split the cache into fixed-size blocks that need not be next to each other; a **block table** maps a request's logical blocks to physical blocks. [P]
- Result: near-zero KV-cache waste (waste is bounded by one block per sequence), larger batches, and memory sharing between sequences via copy-on-write (parallel sampling, beam search). [P]
- vLLM combines it with block-level memory management and preemptive scheduling (pausing/evicting requests when memory runs out). [P]
- Trade-offs: needs a custom attention kernel that understands non-contiguous blocks; block size needs tuning. [S: PromptLayer, ZeroEntropy]

> **Fragmentation** = memory that is reserved or split into pieces that cannot be used effectively.
> **Copy-on-write** = share the same data until one owner changes it, then copy only then.

### 4.7 Other optimizations (what each one attacks)
- **FlashAttention:** computes exact attention in small tiles kept in fast on-chip SRAM, avoiding writing the huge N×N attention matrix to HBM. Goal: fewer memory reads/writes, not fewer FLOPs. [P: Dao et al.]
- **Speculative decoding:** a small draft model guesses several tokens; the big model verifies them in one parallel pass; wrong guesses are discarded, so output matches what the big model would have produced. Best at low batch sizes where compute is spare. [P: Leviathan et al.; S: Baseten]
- **Quantization:** store weights in lower precision (for example 8-bit or 4-bit) so fewer bytes must be read per token. Tools: bitsandbytes, GPTQ, AWQ, LLM.int8(). [P: HF docs; S: DigitalOcean]
- **Chunked prefill:** split a long prompt into chunks and mix them with decode steps so a big prefill does not freeze ongoing decodes. [P: Sarathi; DeepSpeed-FastGen's SplitFuse is a related design]
- **Prefill/decode disaggregation:** run prefill and decode on separate GPU pools, matching compute-heavy work and memory-heavy work to suitable hardware. [S: Weka, theorempath]
- **Parallelism (tensor/pipeline):** split a model across several GPUs when it does not fit on one. [P: vLLM paper, NVIDIA blog]

> **Tiling** = processing data in small blocks that fit in fast memory.
> **Stall** = a pause in progress while waiting on something.

### 4.8 Doing it right: minimal implementations
```python
# vLLM: continuous batching + PagedAttention are on by default (API as shown in secondary tutorial; check docs.vllm.ai for your version)
from vllm import LLM, SamplingParams
llm = LLM(model="meta-llama/Llama-3.1-8B-Instruct")
outs = llm.generate(["Explain photosynthesis briefly."], SamplingParams(max_tokens=64))
```

```python
# Hugging Face Transformers: static KV cache + torch.compile (from HF docs; verify against current version)
from transformers import AutoModelForCausalLM
import torch
model = AutoModelForCausalLM.from_pretrained("google/gemma-2b", torch_dtype="auto", device_map="auto")
model.generation_config.cache_implementation = "static"
model.forward = torch.compile(model.forward, mode="reduce-overhead", fullgraph=True)
```
Checklist for a sane setup:
- Measure TTFT, TPOT and p95/p99 latency (tail percentiles), not only averages. [S: Clarifai]
- Decide which phase hurts: long inputs + short outputs points to prefill; short inputs + long outputs points to decode. [S: Redis]
- Turn on continuous batching and paged KV cache (default in modern engines), then add quantization or speculative decoding based on your measured bottleneck.

---

### 5. LLM Inference Use Cases
**By workload shape** (the "when"):
- **Long prompt, short answer** (summarization, classification, RAG with large retrieved context): prefill-heavy; TTFT dominates. [S: MachineLearningMastery, Redis]
- **Short prompt, long answer** (long-form generation): decode-heavy; TPOT and memory bandwidth dominate. [S: same]
- **Chat with long history:** stresses both. [S: MachineLearningMastery]

**By goal:**
- **Interactive apps** (chatbots, assistants, code completion): need good TTFT and steady TPOT, with tail latency watched closely. 
- **Batch jobs** (bulk summarization, dataset labeling): care about throughput and cost per token, not individual latency. [S: Redis]

**Technique picker:**

| Situation | Reach for |
|---|---|
| Many concurrent users, GPU memory fills up | continuous batching + paged KV cache |
| Memory-bound decode, spare compute at low batch | speculative decoding |
| Model too big or too slow for your GPU | quantization, then multi-GPU parallelism |
| Long prompts cause stutter for other users | chunked prefill or disaggregation |
| Same prompt prefix reused | KV cache reuse / prefix caching [S: Baseten] |

**Real systems that implement these:** vLLM, Hugging Face TGI, NVIDIA TensorRT-LLM, SGLang, LMDeploy, DeepSpeed-FastGen. [P/S: DeepSpeed-FastGen README, HF docs, PremAI]

**Connecting it to your interests** `[mine]`: a legal RAG system feeds long retrieved passages and usually wants short, precise answers, which matches the prefill-heavy pattern, so TTFT and prompt caching are probably your first levers. Also, if decode speed is bound by memory bandwidth, the same principle should apply to small devices like a Raspberry Pi 5 running a local model (not tested here). A good experiment: measure TTFT vs TPOT on your own setup and see which one moves when you change prompt length.

---

### 6. LLM Inference Pros & Cons
**Pros of the modern optimized inference stack**
- Large throughput gains without retraining the model (Orca 36.9×, vLLM 2–4×; see section 3 for caveats).
- Exact techniques (FlashAttention, PagedAttention, speculative decoding) keep output quality unchanged.
- Lower cost per token because GPUs are kept busy and memory is not wasted.
- Open-source engines (vLLM and others) make these techniques accessible.

**Cons / costs**

| Technique | Downside |
|---|---|
| KV cache | memory grows with every token and every user; can become the main limit |
| Continuous batching | bigger batches can raise per-user latency and queueing; needs a smarter scheduler |
| PagedAttention | custom kernels, tuning (block size), extra indirection (the page-table lookup) [S] |
| Speculative decoding | helps mostly at low batch sizes with spare compute; needs a good draft model (shared tokenizer) [S: Baseten; P: HF docs] |
| Quantization | accuracy can degrade; needs specialized methods to limit loss [S: Novita, DigitalOcean] |
| Static cache + compile | needs recompilation if batch size or max length changes [P: HF docs] |
| Disaggregation | more infrastructure complexity (two pools, moving KV cache between them) [S] |
| Benchmarks | numbers are hard to compare across setups |

**Verdict:** the techniques mostly attack one root fact, decode is memory-bound and stateful. Pick them by measured bottleneck, not by hype.

---

### 7. Why We Needed LLM Inference (Optimization)
**The core problem:** the Transformer (2017) made *training* highly parallel because the whole target sequence is available at once. During *generation* it is the opposite: each output token feeds the next step, so the work cannot be parallelized across output positions. [P: Vaswani et al.; S: Transformers are RNNs paper]

**Chain of consequences:**
1. Generating K tokens needs K serial model runs. [P: Leviathan]
2. Recomputing history each step is wasteful, so the KV cache was introduced; it trades compute for memory. [S]
3. Cache memory grows per request and per token, so contiguous allocation wasted memory and limited batch size. [P: vLLM]
4. Request-level batching left GPUs idle waiting for the slowest request. [P: Orca]
5. Decode being memory-bound meant faster math did not help; you had to move less data or do more per read (FlashAttention, quantization, speculative decoding, batching). [P]

**Timeline** (dates from arXiv submissions and venue pages):

| When | Milestone |
|---|---|
| Jun 2017 | *Attention Is All You Need* introduces the Transformer |
| May 2022 | FlashAttention (IO-aware exact attention) |
| Jul 2022 | Orca at OSDI '22: iteration-level scheduling (continuous batching) |
| Nov 2022 | Speculative decoding (Leviathan et al.; ICML 2023 oral) |
| Aug 2023 | Sarathi: chunked prefills piggybacked with decodes |
| Sep 2023 | PagedAttention / vLLM (SOSP 2023) |

> **IO-aware** = designed around how much data is read from and written to different memory levels.

---

### Big Picture Flow
```
Prompt text
   │ tokenize
   ▼
Token IDs ──► SCHEDULER (continuous batching: admit when KV blocks are free)
   │
   ▼
PREFILL  (parallel over prompt, compute-bound)
   │  writes KV cache (paged blocks) ──► logits ──► sample token #1      <-- TTFT ends here
   ▼
DECODE LOOP  (1 token per step, memory-bound)
   read weights + KV cache ──► append new K,V ──► logits ──► sample ──► EOS / max length?
        ▲                                                                   │ no
        └───────────────────────────────────────────────────────────────────┘
                                                                            │ yes
                                                                            ▼
                                            free KV blocks ──► detokenize ──► stream text to user

Levers:  FlashAttention (faster attention)   Quantization (fewer bytes per weight)
         Speculative decoding (more tokens per big-model pass)   Chunked prefill / disaggregation (isolate phases)
```

---

### References
**Primary (papers and official docs)**
1. Vaswani et al., *Attention Is All You Need* (2017): https://arxiv.org/abs/1706.03762
2. Yu et al., *Orca: A Distributed Serving System for Transformer-Based Generative Models*, OSDI '22: https://www.usenix.org/conference/osdi22/presentation/yu
3. Kwon et al., *Efficient Memory Management for Large Language Model Serving with PagedAttention*, SOSP 2023: https://arxiv.org/abs/2309.06180 (code: https://github.com/vllm-project/vllm)
4. Dao et al., *FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness*: https://arxiv.org/abs/2205.14135
5. Leviathan et al., *Fast Inference from Transformers via Speculative Decoding*, ICML 2023: https://arxiv.org/abs/2211.17192
6. *SARATHI: Efficient LLM Inference by Piggybacking Decodes with Chunked Prefills*: https://arxiv.org/pdf/2308.16369
7. NVIDIA, *Mastering LLM Techniques: Inference Optimization*: https://developer.nvidia.com/blog/mastering-llm-techniques-inference-optimization
8. Hugging Face, *LLM inference optimization* (Transformers docs): https://huggingface.co/docs/transformers/main/llm_optims
9. Databricks, *LLM inference performance benchmarking* (metrics): https://docs.databricks.com/en/machine-learning/foundation-models/prov-throughput-run-benchmark
10. Microsoft DeepSpeed-FastGen README: https://github.com/microsoft/DeepSpeed/blob/master/blogs/deepspeed-fastgen/README.md
11. *LLMs as High-Dimensional Nonlinear Autoregressive Models with Attention* (KV cache complexity): https://arxiv.org/pdf/2602.00426
12. *Transformers are RNNs: Fast Autoregressive Transformers with Linear Attention*: https://arxiv.org/pdf/2006.16236
13. Survey, *Efficient Large Language Models*: https://arxiv.org/pdf/2312.03863

**Secondary (tutorials/blogs; verify numbers before relying on them)**
14. Redis, *Prefill vs Decode*: https://redis.io/blog/prefill-vs-decode/
15. Exoscale, *Inside an LLM: From Prompt to Tokens*: https://www.exoscale.com/blog/llm-from-prompt-to-tokens/
16. MachineLearningMastery, *Using a Transformer Model: From Training to Inference*: https://machinelearningmastery.com/using-a-transformer-model-from-training-to-inference/
17. dev.to, *Why LLM Decoding Is Memory-Bound*: https://dev.to/ji_ai/why-llm-decoding-is-memory-bound-prefill-vs-decode-roofline-223f
18. IntuitionLabs, *GPU Inference Performance*: https://intuitionlabs.ai/articles/gpu-inference-performance-prefill-decode-batching
19. BentoML, *LLM inference metrics*: https://bentoml.com/llm/inference-optimization/llm-inference-metrics
20. Baseten, *How to optimize LLM inference*: https://www.baseten.co/blog/how-to-optimize-llm-inference-speed-and-reduce-costs-in-production/
21. Weka, *Prefill and Decode technical guide*: https://www.weka.io/learn/ai-ml/prefill-and-decode/
22. Clarifai, *TTFT vs Throughput*: https://www.clarifai.com/blog/ttft-vs-throughput
23. PremAI, *LLM Batching: Static vs Continuous*: https://blog.premai.io/llm-batching-static-vs-continuous-and-why-it-matters-for-throughput
24. DigitalOcean, *LLM Inference Optimization Part 1*: https://www.digitalocean.com/community/tutorials/llm-inference-optimization-stack-part-1
25. Osteele, *Under the Hood: LLM Inference Costs*: https://viz.osteele.com/llm-visualizations/
26. TheoremPath, *Inference Systems Overview*: https://theorempath.com/topics/inference-systems-overview
27. PromptLayer / ZeroEntropy PagedAttention glossaries: https://www.promptlayer.com/glossary/pagedattention , https://www.zeroentropy.dev/concepts/paged-attention/
