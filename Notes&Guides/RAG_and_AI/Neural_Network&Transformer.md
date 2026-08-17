# intro 
this section will be about learning neural network and transformers 


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