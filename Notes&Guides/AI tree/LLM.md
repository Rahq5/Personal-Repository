# Intro

>Note: notes in this article will have alot of shortcuts and outside links for some topics like transformers, cuz it's already written there and it's a wast of time to mention again and again for me "writer"

Large language models (LLMs) are a category of [deep learning](https://www.ibm.com/think/topics/deep-learning) models trained on immense amounts of data, making them capable of understanding and generating natural language and other types of content to perform a wide range of tasks. LLMs are built on a type of [neural network](https://www.ibm.com/think/topics/neural-networks) architecture called a [transformer](https://www.ibm.com/think/topics/transformer-model) which excels at handling sequences of words and capturing patterns in text

# Working of LLM
LLMs primarly uses transformer architecture which enables them to learn long range dependineces and contextual meanings

## Work of LLM (in a Nutshell)
it works through bunch of things which are:
1. **input embedding**: Converting text into numerical vectors.
2. **postional-encoding**: Adding sequence/order information
3. **self-attention**: Understanding relationships between words in context.
4. **feed-forward layers**: Capturing complex patterns.
5. **decoding**: Generating responses step by step.
6. **multi-Head attention**: Parallel reasoning over multiple relationships.

which has several steps which are:
1. **tokenization and input**
2. **transformer architecture**
3. **pre-training**
4. **fine-tuning and Alignment**
5. **Inference**

Steps in a nutshell:
- **Tokenize & embed**: Text → subword tokens → numeric IDs → embedding vectors, with positional info attached so order isn't lost.
  
- **Transformer pass**: Self-attention lets every token weigh every other token simultaneously (not sequentially), producing context-aware representations in one parallel pass.
  
- **Pre-training**: Trained on massive raw text via next-token/masked-token prediction; backpropagation updates weights until grammar, facts, and patterns are baked in.
  
- **Fine-tuning & alignment**: Supervised fine-tuning on curated Q&A pairs teaches "assistant" behavior; RLHF then trains a reward model from human rankings and uses it to further steer the weights.
  
- **Inference**: Given a prompt, the model outputs a probability distribution over the next token, samples from it (not just argmax), appends the result, and repeats.

## Work of LLM (in Detail)

### Pretraining LLMs
1. **Pre-Training:**
	   Training starts with a massive amount of data—billions or trillions of words from books, articles, websites, code and other text sources.
	   Data scientists oversee cleaning and pre-processing to remove errors, duplication and undesirable content.
 
2. Tokenizing
	   This text is broken down into smaller, machine-readable units called “tokens,” during a process of “[tokenization](https://www.ibm.com/think/topics/tokenization).” These units maps to the original word


- **How it's trained:**
  LLMs are trained with self-supervised learning.
   
- **what is Self-supervised**:
	  an artificial intelligence method where a model teaches itself by creating its own training labels from raw, unlabeled data

### Transformer
then model passes tokens through a transformer network. Transformer primarily uses self-attention mechanisms which explained previously in [[Transformer Architecture |Transformer Arch]] 

### Fine-tuning LLMs
after the model is trained (or specifically "pretrained"), the model now has a general knowledge let's say so now the step is to make it deep in specialized domain which this called **Fine-tuning**.

as example: Giving model an intensive large dataset of legal papers and files in order to make a legal chatbot for legal field 

for further i will mention some other fine-tuning types and their purposes.
Types of fine-tuning:

- **Supervised fine-tuning:**
	  Supervised fine-tuning is a process that takes a pre-trained general-purpose AI model and customizes it to excel at specific tasks or industries by training it on smaller, labeled datasets.
	  
- **RLHF (reinforced feedback with humans)**
	  RLHF is a training technique where humans evaluate and rank AI model outputs,
	  
- **Reasoning models:**
	- Reasoning models are specialized LLMs trained to solve complex problems by breaking them down into smaller, logical steps rather than jumping directly to answers—a capability that supervised fine-tuning alone cannot develop
	  
	- **Limitations:** it doesn't teach genuine _reasoning_. Real reasoning requires abstract thinking and multi-step problem-solving—like
	  
- **Instruction tuning:** 
	- Instruction tuning is a training technique that teaches LLMs to understand and follow human instructions more effectively, making them better at doing what users actually want them to do.
	  
	- **Problem**: Pre-trained LLMs are trained on massive amounts of text to predict the next word, but this doesn't automatically make them good at _following instructions_. A model might be excellent at pattern recognition and text generation, but it may not prioritize understanding what a user is asking for or responding in the way the user intends.