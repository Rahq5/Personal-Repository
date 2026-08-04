this notes taken from [hands on RAG for production] 
here is where to dive 


# Diving into basics 

- **what a rag retrieval part actually is ?:**
	- 1. vector database: has all your data and turns them into embeddings
	- 2. search ALGORITHM: is the core logic that has two types, sparse and dense 
	- 3. python code (glue): 
	- 4. Reranking models: small specialized machine learning model that only double checks retrieved results and re-sort them from most-relevant to least-relevant 

- How vectors are compared and labeled as "most-relevant" and "least-relevant"