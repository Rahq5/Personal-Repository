# Intro
Generative AI is a new application under NN that produces brand new content like text, images or music

- **What makes Gen AI comes under NN?:** Neural network is a mathimatical architecture made of interconnected nodes, while GenAI is a goal app or category of further goal apps (like parametric and RAG)
# How Gen AI works?
Gen Ai operates in three phases:
- **Training:** to create foundation model that can serve the basis of multiple gen AI apps
- **Tuning:** to focus the model into specific  smaller dataset
- **Generation,evaluation and retuning**:to assess the gen AI application's output and continually improve its quality and accuracy.

## Training
Generative AI begins with a foundation model, a deep learning model that serves as the basis for multiple different types of generative AI applications. The most common foundation models today are [[LLM]], created for text generation applications, but there are also foundation models for image generation, video generation, and sound and music generation as well as multimodal foundation models that can support several kinds content generation.

**steps of training:**
1. feed the model a huge volumes of unlabeled, unstructured raw data
2. model should perform evalutions of millions of "fill in the blank" exercises trying to teach the model to predict next elements
3. repeating this would minimize the difference between predictions and actual answers
4. result: a neural network of parameters

>Note: meant of parameter here is bias and weighs of these entities, patterns and relations between them


## Tuning
after the training done, the training it self was general, now it's time to give the model a domain to focus on. This step has two types:
1. Fine tuning
2. Reinforcement learning with human feedback (RLHF)

### Fine tuning
fine tuning means to feed the model a labeled data for this specific domain until he learns it too well.

**steps:**
1. give the model labeled data in specific wanted domain
2. repeat util it learnt the domain too well

**Why fine-tuning might not be a good idea?:**
	Fine-tuning is labor-intensive. Developers often outsource the task to companies with large data-labeling workforces.

### Reinforcement learning with human feedback (RLHF)
human users respond to generated content with evaluations the model can use to update the model for greater accuracy or relevance. Often, RLHF involves people ‘scoring’ different outputs in response to the same prompt. But it can be as simple as having people type or talk back to a chatbot or virtual assistant, correcting its output.

**simple terms**: see when you give your AI a task , and he does it wrong and you correct him or ask him t get something right? this is RLHF

## Generation, evaluation, more tuning
People who build and use AI apps keep checking how well the outputs work, and they adjust ("tune") the model often — sometimes weekly — to make it more accurate. But the core AI model itself (the "foundation model") only gets rebuilt from scratch rarely, maybe once a year or so.

RAG lets an AI model pull in outside information (documents, databases, live sources) instead of relying only on what it learned during training. Two benefits:

1. **Always current** — since it fetches info in real time, it can answer with up-to-date facts even if the original training data is old.
2. **Transparent sourcing** — when RAG is used, you can usually see _which_ document or source the answer came from. With the model's own trained knowledge, there's no such trail — you can't point to where inside its parameters an answer came from.

# Agents
# Retreival-Augmented Generation

to see full details visit these files:
- [[RAG Notes]]
- [[RAG Notes 2]]