The encoder-decoder structure is key to transformer models. The encoder processes the input sequence into a vector, while the decoder converts this vector back into a sequence. Each encoder and decoder layer includes self-attention and feed-forward layers.

>Note: "input sequence" is actually that sequence of tokens

to read more click this [[Encoder-Decoder Architecture]]
# Understanding Encoder-Decoder
The encoder-decoder model is a neural network used for tasks where both input and output are sequences. It is commonly applied in areas like translation, summarization and speech processing.

- The encoder processes the input sequence and converts it into a fixed representation (context vector)
- The decoder uses this representation to generate the output sequence step by step
- Works well for tasks where input and output lengths are different


- **Encoder**:
	  The encoder processes the input sequence and converts it into a fixed representation (context vector) using an RNN or LSTM.
	  - Processes input tokens sequentially and updates hidden states
	  - Captuers relations between words in the sequence
	  - produces final hidden and cell states forming the context vector

- **Decoder**:
	  The decoder uses the context vector from the encoder to generate the output sequence step by step.
	  - takes previous outputs and context vector from encoder to predict next token
	  - generates output sequentially until an end token is reached
	  - initializes its states using the encoder's final states


## Working of Encoder-Decoder model:

**Step 1: Tokenizing the Input Sentence**
- The sentence "I am learning AI" is first broken into tokens: ["I", "am", "learning", "AI"].
- Each word (token) is converted into a vector that a machine can understand. This process is called embedding.

**Step 2: Encoding the Input**
- The encoder processes these embeddings sequentially using an LSTM network.
- At each step, it updates its hidden state based on the current word and previous context. This helps the model understand the sequence order and relationships between words.
- After processing the full sentence, the encoder generates a context vector (final hidden and cell states), which represents the meaning of the entire input sentence.

**Step 3: Passing the Context to the Decoder**
- The Context Vector is passed to the Decoder as shown in image.
- It acts like a summary of the full input sentence.

**Step 4: Decoder Generates Output Step-by-Step**
- The Decoder uses the context and starts creating the output one word at a time.
- First it predicts the first word then uses that to predict the second word and so on.

**Step 5: Attention Mechanism**
- Basic encoder-decoder uses a single context vector, which can limit performance for long sequences.
- Attention mechanism helps the decoder focus on different parts of the input at each step.
- Improves accuracy by not relying only on one fixed representation.

**Step 6: Producing the Final Output**
- The decoder continues generating until the full translated sentence is produced.
- Each output token depends on the previous ones and the input context. You finally see the output tokens generated on the right side of the diagram completing the translation.



## Applying attention in different parts

**1. Encoder Self-Attention** Query, Key, and Value all come from the Encoder's previous layer. Every word in the input can attend to every other word in the input — the Encoder is not restricted by word order or position. This full-context access lets the Encoder build long-range meaning, e.g. connecting a word early in the sentence with a word much later.

**2. Decoder Self-Attention (Masked)** Query, Key, and Value all come from the Decoder's previous layer. The Mask blocks future tokens from being seen — meaning each position in the Decoder can only attend to tokens the Decoder has already generated, never tokens that come after. This masking is what keeps the Decoder's generation **auto-regressive** — auto-regressive means the Decoder predicts one token at a time, each new token conditioned only on previously generated tokens.

**3. Encoder–Decoder Attention (Cross-Attention)** The Query comes from the Decoder. The Key and Value come from the Encoder's final output. This setup lets the Decoder consult the Encoder's understanding of the input sentence at every generation step, rather than relying only on the Decoder's own internal state.

**Together:** The Encoder reads the full input sentence at once (via Encoder Self-Attention). The Decoder then generates the output one token at a time (via Decoder Self-Attention, kept orderly by the Mask), while continuously checking back against the Encoder's output (via Cross-Attention) to stay grounded in the original input.

