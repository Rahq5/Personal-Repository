# intro 
this section will be about learning neural network and below:
- Transformer
- RNN
- Generative AI h


# Define
- **what is neural network and how they exactly mimic human brain?**:
	  machine learning models that mimics human brains. they use the same biology concept of neuron cells that has input,neuron and output 

- **components of neural networks?**:
	- **Neurons****: The basic units that receive inputs, each neuron is governed by a threshold and an activation function.
	- ***Connections****: Links between neurons that carry information, regulated by weights and biases.
	- ***Weights and Biases****: These parameters determine the strength and influence of connections.
	- ***propagation Functions****: Mechanisms that help process and transfer data across layers of neurons.
	- **Learning Rule****: The method that adjusts weights and biases over time to improve accuracy.

- **stages of learning in NN?:**
	- input computations: data is fed into the network 
	- output generation: based on the current parameters, the network generates an output 
	- iterative refinement: the network refines it's outputs by adjusting weights and biases, gradually improving it's performance on divers tasks 

- **layers in neural network?**:
	1. ****Input Layer:**** This is where the network receives its input data. Each input neuron in the layer corresponds to a feature in the input data.
	2. ****Hidden Layers:**** These layers perform most of the computational heavy lifting. A neural network can have one or multiple hidden layers. Each layer consists of units (neurons) that transform the inputs into something that the output layer can use.
	3. ****Output Layer:**** The final layer produces the output of the model. The format of these outputs varies depending on the specific task like classification, regression.

# working of neural network
here you gonna see the deeper flow of data inputs the NN.
for simplifying what's coming.
data enters the:

1. **forward propagation part**: which gonna receive inputs passes them to hidden layer then output. in this phase all of inputs, weights and bias are calculated 

2. **back propagation part**: it calculated the actual output came from previous step with predicted output and calculates loss (enhances future result by minimizing loss)

3. **iteration part:** This process of forward propagation, loss calculation, backpropagation and weight update is repeated for many iterations over the dataset. Over time, this iterative process reduces the loss and the network's predictions become more accurate.



before going to details see answers of these questions:

- **what is weight and what decides it?**:
	it starts as a random number, They act as **scaling dials** (multipliers) determining how much attention to pay to a specific input feature.

- **what bias does and why we need it?**: 
	  it shift the line on the plot, In math terms, if weights change the slope of a line, the bias acts as the y-intercept. It moves the activation function up, down, left, or right on a graph. also handles zero inputs which results in total zero (due to multipliction on zero)
	  - why?:
	    Without bias, the network's decisions are forced to pass through the zero-point origin (0,0), making the model too rigid to learn real-world patterns.


## forward propagation
When data is input into the network, it passes through the network in the forward direction, from the input layer through the hidden layers to the output layer. This process is known as forward propagation.

here's what happens during this phase:

1- linear transformation: 
   each neuron recevies inputs which are multiplied by the weights associated with connections.
   these all products are summed togather and add a bias to the sum 
$$
[Tex]z = w_1x_1 + w_2x_2 + \ldots + w_nx_n + b[/Tex]
$$
where :
- "W" is weight
- "X" is input
- "b" is bias 

****2- Activation:**** The result of the linear transformation (denoted as [Tex]z[/Tex]) is then passed through an activation function. The activation function is crucial because it introduces non-linearity into the system, enabling the network to learn more complex patterns.

## Backpropagation
the network guessed something. The guess was off (wrong). Now it needs to fix itself.

1. **Measure the mistake (Loss)**:
	   Compare what the network predicted vs. the correct answer. Get one number that says "how wrong was I?"
    
2. **Find out who's to blame (Gradient)**: 
	    Every weight in the network contributed a little to that wrong answer. This step asks, for _each_ weight individually: "if I tweak you slightly, does the mistake get worse or better — and by how much?" That's all a gradient is: a per-weight "blame + direction" score.
    
3. **Walk backward through the layers**: 
	   The network is layers stacked on each other, so you can't check the blame of an early layer directly — it's buried under later layers. So you start at the loss (the end) and work backward, layer by layer, passing the blame back. This backward walk is why it's called **back**propagation.
    
4. **Fix each weight a little (Update)** :
	   Now that every weight knows its blame + direction, nudge it slightly _opposite_ to the direction that caused the mistake. Small nudge, not a huge jump — controlled by the learning rate.
    
5. **Repeat** — Do this over and over, on many examples. Each round, the network gets a little less wrong

## iterations
really it's just iterating all of that over and over to get less wrong predictions 

## Full flow of neural network
### Neural Network Execution Flow

1. **Input Entry**
   * Features or embeddings enter Layer 1 directly from raw data as numbers ($x_1, x_2, \dots, x_n$).

2. **Forward Pass (Layer by Layer)**
   * At each neuron $j$, weights scale the inputs and bias shifts the total:
     $$z_j = (w_1 \cdot x_1 + w_2 \cdot x_2 + \dots) + b_j$$
   * The sum passes through an activation function (like ReLU or Sigmoid) to add non-linearity:
     $$a_j = \text{Activation}(z_j)$$

3. **Output & Prediction**
   * The final layer outputs the model's prediction ($\hat{y}$).

4. **Loss Computation**
   * The prediction ($\hat{y}$) is compared to the true label ($y$) to measure error:
     $$\text{Loss} = (\hat{y} - y)^2$$

5. **Backward Pass (Backpropagation)**
   * Calculus (the chain rule) runs backward from the loss to calculate gradients for every parameter:
     * $\frac{\partial \text{Loss}}{\partial w}$: how much the weight contributed to the error.
     * $\frac{\partial \text{Loss}}{\partial b}$: how much the bias contributed to the error.

> the "∂" is the partial differential (calculus reference) 

4. **Gradient Descent Update**
   * The optimizer nudges each weight and bias in the opposite direction of the error:
     $$w_{\text{new}} = w_{\text{old}} - (\text{Learning Rate} \times \frac{\partial \text{Loss}}{\partial w})$$
     $$b_{\text{new}} = b_{\text{old}} - (\text{Learning Rate} \times \frac{\partial \text{Loss}}{\partial b})$$

7. **Iterate**
   * Repeat steps 2 through 6 across training batches and epochs until the loss stabilizes at a minimum.

# Generative AI 
## Intro
Generative AI is a new application under NN that produces brand new content like text, images or music

- **What makes Gen AI comes under NN?:** Neural network is a mathimatical architecture made of interconnected nodes, while GenAI is a goal app or category of further goal apps (like parametric and RAG)

to see full details visit this file [[Generative AI]]
## How Gen AI works?
Gen Ai operates in three phases:
- **Training:** to create foundation model that can serve the basis of multiple gen AI apps
- **Tuning:** to focus the model into specific  smaller dataset
- **Generation,evaluation and retuning**:to assess the gen AI application's output and continually improve its quality and accuracy.



# Architectures

## Transformer

### Brief Definition
In a nutshell, what does a transformer do?
Imagine that you’re writing a text message on your phone. After each word, you may get three words suggested to you. For example, if you type “Hello, how are”, the phone may suggest words such as “you”, or “your” as the next word. Of course, if you continue selecting the suggested word in your phone, you’ll quickly find that the message formed by these words makes no sense. If you look at each set of 3 or 4 consecutive words, it may make sense, but these words don’t concatenate to anything with a meaning. This is because the model used in the phone doesn’t carry the overall context of the message, it simply predicts which word is more likely to come up after the last few. Transformers, on the other hand, keep track of the context of what is being written, and this is why the text that they write makes sense.

to head to full article click this --> [[Transformer Architecture]]

## Diffusion model
explain the difference between it and the by media-type one
## GAN

## Encode \ Decode


# Media-type models
here am gonna discuss some media type models , some of them maybe used for multiple things rather than the media type i labeled them 

## RNN (Audio)

### Intro 
Recurrent Neural Network or RNN is a deep NN model that are trained generally on sequential type of data such as time, text, and speech. so our point here is to focus more on audio which include speech.

this model can be used in predicting daily flood levels based on past daily flood (for countries that gets flooded so much). 
Also used for timing or order problems such as: 
- language translation
- NLP
- sentiment analysis
- speech recognition 
- image captioning.

### How RNN works
RNN works the same as forward prop NN and CNN where they use data to learn. They also use their memory as the take info from prior inputs to influence the current input and output. 
Traditional NN assumes that inputs and outputs are independent of each other, while the output of RNN depend on prior elements within the sequence.

simpler...

**The problem RNNs solve**
A normal neural network treats every input as separate — like calling a stateless function with no memory of previous calls. But language isn't like that. To understand "under the weather," you need to remember "under" when you read "weather." Order and memory matter.

**How RNNs fix this**
An RNN processes a sequence one piece at a time (like looping through an array), and at each step it keeps a **hidden state** — think of it as a variable that carries forward a summary of everything seen so far.

to explain more how that happens using a pseduocode:
```
hidden_state=0

for word in sentence:
	hidden_state = update(word, hidden_state)
	output= predict(hidden_state)
```
Thus, `hidden_state` at word 3 was influenced by word 1 and 2

another example:
```python

avg= 0
avg = (avg + 5) / 2  #avg is 2.5 now
avg = (avg + 10) / 2 # avg is 6.5 now 

# avg got overrwritten but it kept the value 6.5 
# which supposed to be 5.0 if there's no old value

```

**Shared weights**
In a normal network, each layer/node has its own separate weights. In an RNN, the _same_ weight values are reused at every time step — like calling the same function repeatedly instead of writing a new one for each input. This is why it can handle sequences of any length.

**Training it (BPTT)**
In a normal network, backprop runs once: output → input, done. But an RNN uses the _same_ weights at every time step, so a single weight actually affected the output multiple times (once per step). That means one error signal isn't enough — you have to calculate the error at each individual time step, then add all of those up before you apply the update. 

It's like a variable getting modified inside a loop: you don't just look at its final value, you have to track and sum its contribution from every iteration to know the total effect


### Limitation
While the RNN is processing word N, it can't predict or use word N+1 or N+2, because those future words haven't been fed into the sequence yet at that point in time — they simply don't exist as inputs the model has access to during that step.

so it's **Unidirectional**
means RNN processes the sequence in a fixed order (past->current->future) and not (after future or backward) 

## LLM (Text)

### Intro 
before going in , we have to revise NN basic knowledge

**Basic NN structure:**
A neural network is layers of nodes: input layer → hidden layer(s) → output layer. Each node passes its output to the next layer _only if_ that output crosses a certain **threshold** (a cutoff value — like an `if value > threshold: pass_forward()` check). If it doesn't cross the threshold, nothing gets sent forward. This "on/off" gate is called **activation**.

**Where CNNs fit in**
Different network types are built for different data:
- RNNs (what we just covered) → good for sequences like text/speech, where order matters.
- CNNs → good for images, where spatial patterns (edges, shapes, textures) matter.


**Why CNNs exist**
Before CNNs, identifying objects in images required **manual feature extraction** — meaning a human had to hand-code rules like "look for this edge pattern" or "check this pixel range" to detect objects. That's slow and doesn't scale.

CNNs automate this. Instead of hardcoded rules, they use **matrix multiplication** (multiplying grids of numbers together — the same math from linear algebra) to automatically detect patterns in an image, like edges, textures, and shapes, at different levels of complexity.

**The cost**
Because CNNs run tons of matrix multiplications across every image, they're computationally heavy — which is why training them typically requires a **GPU** (a processor built to handle massive parallel math operations fast, unlike a regular CPU which processes more sequentially).

### How CNN works
CNN is distinguishable from other NNs be their overall performance, they have three layers:
1. Convolutional layer
2. Pooling Layer
3. Fully-Connected Layer

#### Convolutional layer - pattern detector
Think of the image as a big 2D (or 3D, for color) array of numbers — each pixel is just a number. The **filter/kernel** is a _small_ array (commonly 3x3) that acts like a sliding window function scanning over the big array.

```
for each position in image:
    output[position] = dot_product(filter, image_patch)
```

At each position, it multiplies the filter values with the pixels underneath it and sums the result (a **dot product** — a standard linear algebra operation, sum of pairwise multiplications). It then **shifts** by a fixed number of pixels (called **stride** — like a loop's step size, e.g. `range(0, n, stride)`) and repeats until it's swept the whole image. The result of this whole scan is called a **feature map**.

Key hyperparameters (settings you pick before training, like function parameters):

- **Number of filters** → more filters = more feature maps = deeper output.
- **Stride** → bigger stride = smaller output (fewer positions checked).
- **Zero-padding** → adding fake "0" pixels around the border so the filter fits evenly. _"Fit" here means: does the filter's size divide evenly into the image's size without leftover pixels?_

**Parameter sharing** — _"aptly describes itself"_ is a B1+ phrase meaning "fittingly/appropriately describes itself" — but here it means: the _same_ filter (same weight values) is reused at every position, instead of having separate weights per pixel. Same core idea as RNNs reusing weights across time steps.

#### Pooling layer - shrinking data
Pooling also slides a window across the feature map, but it has **no weights** — it just applies a simple math function:

- **Max pooling**: takes the largest value in the window (most common).
- **Average pooling**: takes the average value in the window.

This is essentially data compression — reduces the array size, cuts computation cost, and helps prevent **overfitting** (a model that memorizes training data too closely and performs poorly on new data).

#### Fully-connected (FC) layer — final decision
Here, _every_ node connects to _every_ node in the previous layer (unlike conv layers, which only connect local regions). This layer takes all the extracted features and outputs a **probability** per class (e.g., 90% cat, 10% dog) using a **softmax function** — a math function that converts raw numbers into probabilities that sum to 1.

## Diffusion models (Images)

## CNN (Images)

