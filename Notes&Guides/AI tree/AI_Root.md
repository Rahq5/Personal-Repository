# Introduction
in this page and incoming pages i will fully search what AI is the it's variations and technologies

# What's AI 
It stands for "Artificial Intelligence".
it's a technology that enables computers and machines to simulate human brains in learning, problem solving, decision making, creativity and recognize patterns.

# History
Artificial intelligence was founded as an academic specialization in 1956.
the field went through multiple cycles of optimism until it got disappointed and loss of funding, that time of loss called the **AI winter** which was the time nobody gave care or interest in AI, then it got back in 2012 when GPUs started being used in 2012 to accelerate neural networks and deep learning. got outperformed and this acceleration continued until 2017 with **Transformer architechture**, later in 2020 which called the **AI Boom** with advancement of **generative AI**. 


# How AI Companies bills? 

## per token 

- **Define:** 
	  Per-token pricing means customers are charged based on the exact unit of consumption. That could be tokens, API calls, messages, documents, minutes, images generated, or another measurable unit.
  
- **Deeper details:**
	- this model is best for API-first (dealing with AI model through an API), infrastruct platforms and companies where usage mapped directly to cost 
	   
	- **Advantage**: simplicity, where the more u use the model, the more cost
	- **Disadvantage:** predictability, where it's hard to predict bills which ends up in bill shocks 
  
- **Companies uses this method:** 
	- Anthropic API
	- Open AI

## per credit

- **Define:**
	  means customers buy credits upfront and usage takes off that balance 
  
- **Deeper details:**
  - **For example**, a customer might buy 10,000 credits per month. A basic AI action may cost 1 credit, while a more expensive workflow may cost 20 credits
    
  - This model is useful for platforms with multiple AI features that have different costs. It also works well for marketplaces, AI workspaces, and products where customers use several different capabilities inside one platform.

	- **Advantage:** customers are more predictable about billings cause they now know how much credits they got. even better you can control to auto-buy or stop where credits run out.
	  
	- **Disadvantage**: credit pricing, the company has to pick price for each credits that if they pick a wrong pricing it will hurt the company in both ways (rather expensive or cheap will bleed them)
		  - **if expensive?:** customers start to feel this is arbitrary pricing and it's not linked to something concrete
		  - **if Cheap:** company sells credits in prices are actually less then what it deserves so instead the company get's money, it will start to lose quietly 

    
- **Companies uses this method:** 
	- OpenAI
	- Github (copilot ai)
	- WriteSonic (used for content creation)


## per outcome

- **Define:**
	  Outcome-based pricing charges customers based on successful results.
	  Instead of billing for tokens, calls, or credits, you bill for something the customer actually values. 
	  like:
		  - documents processed
		  - code review completed
		  - claim approved
		  - support ticket resolved
  
- **Deeper details:**
	- In my opinion: this billing type needs a robust, accurate and strong ai system that has small amount of operations failures
	  
	- **Advantage:** This model creates the strongest alignment between price and value. Customers like it because they pay for results, not internal compute.
	  
	- **Disadvantage**: Risk, when AI system fails to processes something, the company will absorb that loss, so if the AI system was a piece of crab, this will bleed the company as hell.
  
- **Companies uses this method:** 
	- **salesForce**: world's largest CRM software provider
	  
	- **Intercom**: AI assistant named Fin, which instantly answers customer questions without human intervention.
	  
	- **ChargeFlow:** e-commerce security and financial defense platform. It automatically gathers shipping, fulfillment, and communication data to fight fraudulent credit card chargebacks for online merchants

## per hybrid billing

- **Define:** Hybrid billing combines a base subscription (a fixed recurring fee) with usage-based charges on top of it.
    
- **Deeper details:**
    
    - Fixed monthly/yearly fee already includes a set amount of usage
        
    - Going over that limit = "overages" (extra charges for extra usage)
        
    - **Example:** $499/month includes 5,000 AI resolutions, extra resolutions cost more
        
    - Best fit for B2B (business-to-business, company sells to other companies) with recurring customers and usage that varies per account
        
    - **Advantage:** Balance — subscription gives predictable recurring revenue, usage part protects margins as customers grow. Customer gets a stable budget, company gets upside.
        
    - **Disadvantage**: Operational complexity — needs accurate metering (measuring exact usage), contract logic, usage tracking, overage calculations, clear invoices. Broken billing system = disputes, missed revenue, confused customers.
        
- **Companies uses this method:**
    
    - B2B companies in general 

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

to see more visit this page [[Natural Language Processing]]
## Machine Learning
Machine learning is a branch of Artificial Intelligence that focuses on developing models and algorithms that let computers learn from data without being explicitly programmed for every task. In simple words, ML teaches systems to think and understand like humans by learning from the data.

Machine Learning is mainly divided to 3 sub core types:
1. **Supervised Learning**: Trains models on labeled data to predict or classify new, unseen data.
2. **Unsupervised Learning**: Finds patterns or groups in unlabeled data, like clustering or dimensionality reduction.
3. **Reinforcement Learning**: Learns through trial and error to maximize rewards, ideal for decision-making tasks.

>Note: label means to mark a group of things as something , like grouping animals and label them with "mammals"

to see more about machine learning see this file [[Machine learning]]
### Neural Network 
to view more about neural network visit this file [[Machine learning#Neural Network]]

