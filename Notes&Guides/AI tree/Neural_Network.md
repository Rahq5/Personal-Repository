# intro 
this section will be about learning neural network and below:
- Transformer
- RNN
- Generative AI


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


# Media-type models