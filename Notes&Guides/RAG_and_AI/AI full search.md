# Introduction
in this page and incoming pages i will fully search what AI is the it's variations and technologies

# What's AI 
It stands for "Artificial Intelligence".
it's a technology that enables computers and machines to simulate human brains in learning, problem solving, decision making, creativity and recognize patterns.

# History
Artificial intelligence was founded as an academic specialization in 1956.
the field went through multiple cycles of optimism until it got disappointed and loss of funding, that time of loss called the **AI winter** which was the time nobody gave care or interest in AI, then it got back in 2012 when GPUs started being used in 2012 to accelerate neural networks and deep learning. got outperformed and this acceleration continued until 2017 with **Transformer architechture**, later in 2020 which called the **AI Boom** with advancement of **generative AI**. 


# AI Engineering stack
There are three layers to any AI application stack: application development, model
development, and infrastructure. When developing an AI application, you’ll likely
start from the top layer and move down as needed:

- **Application development:** this is where "generating a response" is triggered and used, not where it happens internally.
	
  Application development involves providing a model with good
  prompts and necessary context. This layer requires rigorous evaluation.
  Good applications also demand good interfaces.
  
- **Model development:** this is where embeddings, transformers, and the actual "understanding" happens.
	This layer provides tooling for developing models, including frameworks for
	modeling, training, finetuning, and inference optimization. Because data is 
	central to model development, this layer also contains dataset engineering.

- **Infrastructure:** this is what makes the generation physically possible and scalable.
	At the bottom is the stack is infrastructure, which includes tooling for model
	serving, managing data and compute, and monitoring.


# Top-down view on AI tree

## Natural language processing
or called NLP, it allows programs to read , write and communicate in human language. specific problems like speech recognition, machine translation, information extraction.

- Problem:
	Early AI tried to understand language using grammar rules and networks of connected concepts. The main issue: word-sense disambiguation — picking the right meaning when a word has more than one (like "bank" = river edge or money place).
	
	This only worked in "micro-worlds" — tiny, simplified environments with few objects and rules. Outside those, AI failed because of the common sense knowledge problem: it lacked the huge pool of unstated, everyday knowledge humans use to guess meaning correctly.

- fix: 
	Instead of hand-coded grammar rules, modern AI uses word embeddings — turning words into vectors (numbers representing meaning) so similar meanings sit close together mathematically. This lets the system "sense" that "bank" near "river" and "bank" near "money" are different, based on context, without needing explicit rules.
	
	Then transformers (a model architecture using "attention" — a mechanism that lets the model weigh which nearby words matter most for understanding a given word) let AI look at an entire sentence at once instead of word-by-word, capturing context far better than old systems.

**key parts of NLP**:
- understanding: programs figure out what words mean and what the user wants
- generating: computers write or speak clear answers sounds like human 
- processing text: systems break snetences to find names, facts and feeling

How NLP works:
1. **Text or speech input:** 
	   - The system takes written language like sentences or documents which is called text acquisition.

2. **pre-processing:**
	   The text is cleaned from unwanted things and would affect result, and prepared
	   
3. **Language Analysis:**
	   The system studies structure and meaning

4. **Text Representation and Embedding Techniques:**
	   Since machines process numbers, this stage converts text into numerical vectors

5. **Model Training:**
	   Once text is numeric, models are trained to learn patterns and perform NLP tasks
	   
6. **Output Generation**:
	   The system produces results such as text reply, voice, prediction, summary.


**Common NLP tasks:**
- **Text classification:*** Assigning predefined labels to text like spam or topic categories.
- **Sentiment analysis:*** Detecting whether text expresses positive, negative or neutral emotion.
- **Machine translation:*** Automatically converting text from one language to another.
- **Named Entity Recognition:*** Identifying names of people, places, dates, etc in text.
- **Text summarization:*** Generating a shorter version of a document while keeping key meanings.
- **Question answering systems:*** Systems that read text and return exact answers to queries.



mostly used in:
- voice assistants
- translation
- chat bots
- spam filters


## Machine Learning
Machine learning is a branch of Artificial Intelligence that focuses on developing models and algorithms that let computers learn from data without being explicitly programmed for every task. In simple words, ML teaches systems to think and understand like humans by learning from the data.

Machine Learning is mainly divided to 3 sub core types:
1. **Supervised Learning**: Trains models on labeled data to predict or classify new, unseen data.
2. **Unsupervised Learning**: Finds patterns or groups in unlabeled data, like clustering or dimensionality reduction.
3. **Reinforcement Learning**: Learns through trial and error to maximize rewards, ideal for decision-making tasks.

>Note: label means to mark a group of things as something , like grouping animals and label them with "mammals"

### Supervised Learning
Supervised Learning are generally categorized into two main types:
1. **Classification:** goal is to predict discrete labels or categories. (guess strings like names or labels)
2. **Regression:** aim is to predict continuous numerical values. (guess numbers like prices or percentage)

#### Supervised learning algorithms
Some of the most commonly used supervised learning algorithms are:

1. Linear regression: 
	   Linear Regression is a fundamental supervised learning algorithm used to model the relationship between a dependent variable and one or more independent variables. 
	   - assumes that there's a linear relationship between the input and output 
	   - uses a best-fit line to make predictions

2. K-nearest neighbors (K-NN):
	   this model looks for the nearest data points (neighbors) to make predictions, based on similarity.
	   It works by identifying the __K__ closest data points to a given input and making predictions based on the majority class or average value of those neighbors

3. Naive bayes: 
	   Naive Bayes is a machine learning classification algorithm that predicts the category of a data point using probability. It assumes that all features are independent of each other. Naive Bayes performs well in many real-world applications such as spam filtering and document categorization

4. Random Forest (Bagging Algorithm):
	   Random Forest is a machine learning algorithm that uses many decision trees to make better predictions. Each tree looks at different random parts of the data and their results are combined by voting for classification or averaging for regression.

### Unsupervised Learning algorithms
Unsupervised learning are again divided into ****three main categories**** based on their purpose:
1. **Clustring:** Clustering algorithms group data points into clusters based on their similarities or differences
   
2. **Association Rule mining:** Find patterns between items in large datasets, typically used in ==market basket analysis==
   
3. **Dimensionalality Reduction:** Dimensionality reduction is used to simplify datasets by reducing the number of ==features== while retaining the most important information.

> Defining:
> - features: single piece of information like car name or color
>   
> - market basket analysis: is a technique used in data mining to analyze combine items purchased (probably the one used in predictions of online stores)


### Reinforcement Learning 
Reinforcement learning interacts with environment and learn from them based on rewards.
goes with these steps in general:
1. agent does an action
2. action affects on environment
3. environment give a reward if guessed right 

### Semi-Supervised Learning
It uses a mix of labeled and unlabeled data making it helpful when labeling data is costly or it is very limited