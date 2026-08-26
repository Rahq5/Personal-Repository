# Machine Learning
Machine learning is a branch of Artificial Intelligence that focuses on developing models and algorithms that let computers learn from data without being explicitly programmed for every task. In simple words, ML teaches systems to think and understand like humans by learning from the data.

Machine Learning is mainly divided to 3 sub core types:
1. **Supervised Learning**: Trains models on labeled data to predict or classify new, unseen data.
2. **Unsupervised Learning**: Finds patterns or groups in unlabeled data, like clustering or dimensionality reduction.
3. **Reinforcement Learning**: Learns through trial and error to maximize rewards, ideal for decision-making tasks.

>Note: label means to mark a group of things as something , like grouping animals and label them with "mammals"


## Neural Network 
to view more about neural network visit this file [[Neural_Network]]

## Types of machine learning
#### Supervised Learning
Supervised Learning are generally categorized into two main types:
1. **Classification:** goal is to predict discrete labels or categories. (guess strings like names or labels)
2. **Regression:** aim is to predict continuous numerical values. (guess numbers like prices or percentage)

##### Supervised learning algorithms
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

#### Unsupervised Learning algorithms
Unsupervised learning are again divided into ****three main categories**** based on their purpose:
1. **Clustring:** Clustering algorithms group data points into clusters based on their similarities or differences
   
2. **Association Rule mining:** Find patterns between items in large datasets, typically used in ==market basket analysis==
   
3. **Dimensionalality Reduction:** Dimensionality reduction is used to simplify datasets by reducing the number of ==features== while retaining the most important information.

> Defining:
> - features: single piece of information like car name or color
>   
> - market basket analysis: is a technique used in data mining to analyze combine items purchased (probably the one used in predictions of online stores)


#### Reinforcement Learning 
Reinforcement learning interacts with environment and learn from them based on rewards.
goes with these steps in general:
1. agent does an action
2. action affects on environment
3. environment give a reward if guessed right 

#### Semi-Supervised Learning
It uses a mix of labeled and unlabeled data making it helpful when labeling data is costly or it is very limited