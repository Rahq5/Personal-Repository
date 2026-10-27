# Introduction
this is my own notes in reading and revising the research with the name `TheArtOfNotForgetting` 
all of these notes will be used for further workbench made by LEAP 

in this research i will try to compare or mention the biological brain alot, in order to best understanding possible

# Abstract
## The core problem: catastrophic forgetting

**Catastrophic forgetting** means a neural network that learns a new task overwrites what it learned earlier, so its old skills collapse. **Continual learning** is the field trying to fix this, so a model can keep learning without forgetting.

## What CMP proposes

CMP (Cognitive Memory Primitive) is a different architecture built on three ideas, each aimed at reducing forgetting:

- **Sparse relational codes:** each input is represented by only a few active units that encode relationships between parts of the input, so different inputs rarely touch the same units.

	- **More explanations about this**: 
	  Imagine a giant wall with 10,000 light switches. Each picture or sentence you show the network is written down by switching some lights on.
		- **Dense code:** you flip about half the switches for every input. Every input uses almost the same switches, so learning a new one messes up the old ones.
		- **Sparse code:** you flip only a handful, say 20 out of 10,000. _Sparse_ means “very few, spread thin.” Two different inputs mostly use different switches, so learning one barely touches the other.
		- **Relational:** instead of describing a face pixel by pixel, you describe how its parts relate: “eyes are close together, the nose is below them.” The switches record _relationships between parts_, not the parts alone.
  
- **Two-tier competitive memory:** a persistent store with two levels, where entries compete for space, so useful memories survive instead of being overwritten.
  
  >Note: the sparse code is only how an input is represented, it does not decide what is kept. A new representation is compared (cosine similarity) with the stored slots. If it matches one above a threshold, that slot is updated. If not, it takes a new slot and evicts the weakest when memory is full. Representations that match repeatedly are promoted from the fast buffer to the slow register.
  
- **Local updates, no end-to-end backpropagation:** standard networks adjust all weights using one global error signal, which is what causes the overwriting. CMP changes the parts relevant to the current input.
>Note: 
>CMP has no end-to-end backpropagation: each readout weight matrix is updated by a local delta rule from its own input and the output error, and the input embeddings are random and fixed.
>
>Forgetting is reduced mainly by plasticity regulation: weights that moved a lot (a movement-based importance score) get smaller future updates.
>
>**plasticity regulation**: in bio-brain is the part where the brain's ability to change, reorganize, and adapt its structure and function in response to learning, experience, or injury.
>And it's relation to forgetting is when you experince a new thing, your neurons communicates with each other and weaken or strengthen new connections that gonna reshape the memory or create it.




## The experiments

- **Setup:** _domain-incremental byte-level language modeling_. The model learns text from one domain after another (15 domains) and predicts text one byte at a time.
  
- **Baseline:** a _parameter-matched_ Transformer (same number of weights, so the comparison is fair) trained with **online EWC** (Elastic Weight Consolidation, a method that penalizes **"punishment"** changing weights that were important for old tasks).
  
- **Metric:** **backward transfer** measures how much performance on old domains changes after learning new ones. Positive BWT means forgetting, so a lower BWT means less forgetting. The error here is bits-per-byte (BPB, lower is better).. CMP showed substantially less forgetting, across three random seeds and in different domain orders.

### The honest negatives

The authors also report:

- A **large single-domain accuracy gap**: the Transformer is simply better at any one domain.
- A **null result** on a vision benchmark (an experiment that showed no meaningful effect).
- A **documented failure** to combine CMP with another mechanism that improves accuracy.

## Bottom line

CMP forgets much less, but it is less accurate on any single domains. The authors present it as a promising direction, not a replacement for Transformers. The phrase "under the reported experimental settings" is a hedge meaning the results may not hold outside these exact conditions.

my notes and linking to brains:
this research has inspired how exactly a brain memorizes an old task by remembering small key-details, and the result was also excepted that even the human brain although it **remembered** but still dont remember the details which would enhance the accuracy of the brain. Maybe i would find the answer of "how to remember everything accurately" in understanding how human brains actually memorize much details, such as those who memorize the qurran.
Further..
I found a brain illnees cause the brain to over memorize so i should go into that and see if someone really talked about that before 



# Introduction - Research


the author introduced the previous points and added that there was more methods tried to mitigate forgetting before which was EWC and Progressive Networks, but they generally worked around the backpropgation-based with adding additional things.
So what he gonna do is not "how to correct the forgetting after it emerges" but instead he tries to think of another architechture that are built around local learning and sparse representations in order to see if that resists catastrophic forgetting.

Then introduced the CMP that integrates bunch of of things:
- sparse relational binding 
- competitive content-addressed memory inspired by Sparse Distributed Memory  
- modern Hopfield networks 
- predictive-coding principles 
- local learning through a weight-local delta-rule readout.

all of these combined into a unified continual-learning system with a movement based plasticity regulations that doesnt require end-to-end gradient propagation

> Note: for better understanding read this part to know how brain memorizes better 
> 
> when you try to memorize the OSI model in order you might find kind of difficulty in that and may forget the order of some layers, until you learn the famous phrase  "All People Seem To Need Data Processing" where each first letter of each word represents a layer like (All -> Application) (People -> Presentation) (Seem -> Session) and so on. 
> Now after memorizing this phrase your brain, there is new communications happens in your neurons that made you remember better.




## What this paper does not claim

it doesnt claim that CMP is more accurate than transformer. And doesnt gain superiority over attention-based architechtures

The second paragraph gives the bigger picture. If forgetting can be reduced through architecture and local learning, without replay or gradient-based importance scores, that gives researchers another direction for building continual-learning systems. It could work alongside existing methods rather than replace them.


# CMP Architecture
Before the equations begin, the paper gives a one-paragraph roadmap of CMP, and it reads like a pipeline.

It is the same pipeline as the figure, told as one sentence per stage:

1. Two input bytes become one sparse code.
2. That code goes into a memory that can be searched later.
3. A coarser summary tries to predict the code, and the leftover error is kept.
4. A readout combines the code, the memory output, the summary, and the error into a next-byte prediction.
5. The readout learns from its own error only, with no backpropagation through the earlier stages.
6. A limiter shrinks updates to weights that have already changed a lot.
7. The last section compares all this with self-attention.

## CMP Architecture Diagram

