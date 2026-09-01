# Introduction
A phone's word-suggestion feature only looks at the last few words to guess the next one, with no memory of the message's overall context — that's why picking suggested words repeatedly produces a message that seems fine in short 3-4 word chunks, but is meaningless overall. Transformers work differently: they track the full context of everything written so far, which is why their output stays coherent and meaningful as a whole, not just word-to-word.

simply: it keeps track of the whole context instead of just few words like the phone keyboard does 

so a **Transformer is:** a neural network architecture used for various machine learning tasks, especially in natural language processing and computer vision. It focuses on understanding relationships within data to process information more effectively.

- **what is Transformer Arch:**
	  a type of deep-learning neural network architecture that processes entire data sequences at once and uses "self-attention" to weigh the importance of different parts of the input

- **Key Ideas of Transformer:**
  - Uses attention mechanisms to capture relationships between inputs
  - Processes entire sequences at once instead of step by step
  - Improves performance on tasks involving context and dependencies
  - Widely used across NLP, vision and other AI applications


# Core concepts of Transformers
1. **Self-Attention:**
	   Lets each word in a sequence look at every other word and weigh how relevant they are to it, to build context-aware representations.
	   
2. **Multi-Head Attention:**
	   Runs several self-attention operations in parallel (each "head" focusing on different relationships), then combines the results for a richer understanding.
	   
3. **Positional Encoding:**
	   Injects information about word order into the embeddings, since self-attention alone has no built-in sense of sequence position.
	   
4. **Position-wise Feed-Forward Networks**:
	   small neural network applied identically to each position's vector after attention, adding extra non-linear processing power.
	   
5. **Embeddings:**
	   Converts tokens (words/subwords) into numerical vectors that capture meaning, as discussed earlier in this chat.
	   
6. **Encoder-Decoder**
	   Two-part architecture — the encoder reads/understands the input, the decoder generates the output — used in tasks like translation and summarization

## Self-Attention
Self-attention helps a model understand how words in a sentence are related to each other. It allows the model to look at all words at the same time and decide which ones are important for understanding the meaning of each word. Because of this, self-attention captures context more effectively and plays a key role in models like [Transformers](https://www.geeksforgeeks.org/machine-learning/getting-started-with-transformers/).

> Note: Attention is a mechanism that helps a model focus on the most relevant parts of the input when processing information. It assigns importance to different elements so the model can better understand context and meaning.

### Understanding Self-Attention mechanism 
It processes all words at once and identifies which ones are important for capturing context and meaning. 
Self-attention can be represented mathematically to compute relationships between words using:
- queries 
- keys
- values
were each word of the sequence have in the same time these three things.
$$\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V$$
>Note: (square root of dk) is the **dimensionality** (number of values) of the Key vectors — e.g., if each Key vector has 64 numbers, then `d_k = 64`.


The self-attention mechanism transforms the input into three vectors: Query (Q), Key (K) and Value (V) using learned weight matrices.

1. ****Linear Transformation:**** Each input word is converted into Query, Key and Value vectors using weight matrices W(Q), W(K), W(V).
   
2. ****Query-Key Interaction:**** The query vector of a word is multiplied with the key vectors of all words to compute attention scores, indicating how much focus to give to each word.
   
3. ****Scaling:**** The scores are scaled by dividing by ​​(square root of dk) to prevent very large values and ensure stable training.
   
4. ****Softmax Normalization:**** The scaled scores are passed through a softmax function to convert them into probabilities.
   
5. ****Weighted Sum of Values:**** These probabilities are multiplied with the value vectors to assign importance to each word.
   
6. ****Final Output:**** All weighted value vectors are summed to produce the final representation for each word.





## Multi-head Attention
extends the self-attention mechanism by using multiple attention heads in parallel. Instead of relying on a single attention calculation, it allows the model to focus on different parts of the input sequence at the same time, capturing a wider range of relationships between words.

Multi-head attention extends self-attention by splitting the input into multiple heads, enabling the model to capture diverse relationships and patterns.

### Understanding Multi-head Attention mechanism
Instead of using a single set of $Q, K, V$ matrices, the input embeddings are projected into multiple sets (heads), each with its own $Q, K, V$:

1. **Linear Transformation:** The input $X$ is projected into multiple smaller-dimensional subspaces using different weight matrices:
  $$Q_i = XW_i^Q, \quad K_i = XW_i^K, \quad V_i = XW_i^V$$
  where $i$ denotes the head index.

2. **Independent Attention Computation:** Each head independently computes its own self-attention using the scaled dot-product formula:
  $$\text{head}_i = \text{Attention}(Q_i, K_i, V_i) = \text{softmax}\left(\frac{Q_i K_i^T}{\sqrt{d_k}}\right)V_i$$

3. **Concatenation:** The outputs from all heads are concatenated:
  $$\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, \dots, \text{head}_h)W^O$$

4. **Final Linear Transformation:** A final weight matrix $W^O$ is applied to transform the concatenated output into the desired dimension.


## Positional Encoding
Unlike RNNs, transformers lack an inherent understanding of word order since they process data in parallel. To solve this problem **Positional Encodings** are added to token embeddings providing information about the position of each token within a sequence.

### Understanding Positional-Encoding mechanism
Positional encoding is a technique that adds information about the position of each token in the sequence to the input embeddings. This helps transformers to understand the relative or absolute position of tokens which is important for differentiating between words in different positions and capturing the structure of a sentence. Without positional encoding, transformers would struggle to process sequential data effectively.

1. **What is the problem**
	Word embeddings alone carry meaning but no order — the model would see "cat sat on mat" and "mat on sat cat" as identical, since attention processes all words in parallel with no built-in sense of sequence.

2. **What is the fix and how**
	Add a second vector (the positional encoding) directly on top of each word's embedding — same size, added element-wise (index-by-index addition). This positional vector is generated using sine/cosine waves that produce a unique, consistent pattern for each position (1st word, 2nd word, etc.), so the final vector fed into the model carries both _meaning_ (from the embedding) and _order_ (from the positional encoding) at once.

3. **Math used in a nutshell**
	two general equations:
	- **Even dimensions ($2i$):**
  $$PE_{(\text{pos},\, 2i)} = \sin\left(\frac{\text{pos}}{10000^{\frac{2i}{d_{\text{model}}}}}\right)$$

	- **Odd dimensions ($2i + 1$):**
  $$PE_{(\text{pos},\, 2i+1)} = \cos\left(\frac{\text{pos}}{10000^{\frac{2i}{d_{\text{model}}}}}\right)$$
  **What each part means:**

- **`pos`** — The token's position in the sequence, starting from 0 (e.g., "The" = 0, "cat" = 1, "sat" = 2...).
  
- **`i`** — The dimension-pair index inside the positional vector. Each `i` produces **two** output values: one sine (even slot `2i`) and one cosine (odd slot `2i+1`). So `i = 0` fills slots 0 and 1, `i = 1` fills slots 2 and 3, and so on.
  
- **`d_model`** — The total size (dimensionality) of the embedding vector for the whole model (e.g., 512). This is a fixed constant set when the model is built, same for every word.
  
- **`10000`** — A fixed constant (chosen empirically by the original authors) that controls the overall range of wave frequencies. It doesn't change with position or dimension — it's just a "scale knob" baked into the formula.
  
- **`10000^(2i/d_model)`** — This whole term is the **wavelength divisor**. As `i` increases (later dimensions), this number grows larger, which makes `pos` divided by it smaller, which makes the sine/cosine wave oscillate **slower**. So low dimensions (`i` near 0) = fast-changing waves, high dimensions (`i` near d_model/2) = slow-changing waves.
  
- **`sin(...)` / `cos(...)`** — Standard trigonometric functions (oscillate smoothly between -1 and 1). Using both (not just one) means each position gets a value pair that behaves predictably as position increases, which is what makes the "distance" between positions mathematically meaningful to the model.

**Why mixing fast + slow waves matters:** with only one wave speed, positions could repeat the same output pattern (a cat sitting far apart in the sequence might get numbers identical to a much closer position). Layering many wave speeds together — fast ones for fine-grained distinction between nearby words, slow ones for distinguishing far-apart positions — gives every position in the sequence a **unique combination** across all dimensions, even for very long sequences.
  
  am not gonna dive in the details but the core point from this is:
  ```
  "Positional encoding gives each word a unique numerical fingerprint based on its position, built from overlapping fast and slow sine/cosine waves, so the model can tell word order apart even though it processes every word in parallel."
  ```


## Position-wise Feed-Forward Networks
you maybe familiar with feed-forward, it's actually an architecture that [[Neural_Network#forward propagation|forward propogation]]

A regular feedforward network takes one input vector and passes it through layers (linear transformation → activation function → linear transformation) to produce an output. That part you already know.

**"Position-wise" means this exact same small FFN is applied separately, independently, to _each word's vector_ in the sequence — one at a time, not to the whole sequence together.**

- After self-attention mixes information _between_ words (each word "looks at" other words), the FFN's job is different: it processes _within_ each word's own vector, adding extra non-linear transformation power to what attention already produced.
- The **same FFN (same weights)** is reused for every position in the sequence — so word 1, word 2, word 6... all pass through the identical two-layer network, just with their own individual vector as input. Weights aren't shared _across sentences_ differently; they're shared _across positions within_ one pass.

- **Why "position-wise" instead of processing all positions together:** 
	  since attention already handled the cross-word mixing, the FFN doesn't need to see other positions at all — keeping it independent per position makes it computationally simple (can run in parallel across all positions) and keeps its role clearly separate from attention's role.

## Add & Norm (Residual Connections and Layer Normalization)****

## Embeddings

## Encoder-Decoder Architecture
# Parts of Transformer
it has 4 main parts:
- Tokenization
- Embedding
- Postional encoding
- Transformer block (can be multiple blocks)
- Softmax

> Note: for you, all the parts are easy to understand or you know it already but not for Transformer block



## Defining Core concepts

## Defining parts of transformer
- **Tokenization:** 
	  is the part where sentences gets split (words, punctuation, prefix,etc..) and then converted to numerical values where they can input to neural networks.

- **Embeddings**:
	  
