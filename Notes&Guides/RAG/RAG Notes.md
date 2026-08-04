this notes taken from [AI Engineering from O'riley] book in chapter 6 RAG and agents
and it's only to answer questions related to feeding RAG with data and making it efficient (or mitigate extra cost).


# RAG intro 
- what is RAG?: it's a technique that allows LLMs to retrieve information from external data sources. these data sources can be an internal database , user's previous chat or basically the internet 
  
- if RAG was initially the heaviest types of AI that it cant be deployed to public like parametric AIs, how does Gemini's success to make thier own RAG which is the search AI gemini and being deployed globally: ??? 

- how would RAG answer better then parametric AIs ?: simply by providing relevant information to the query. as example: 
	- given the query “Can Acme’s fancy-printer-A300 print 100pps?”, the model will be able to respond better if it’s given the specifications of fancy-printerA300

- what the book means by construct context that RAG uses?: using the same context would lead the AI to re-read the 50 docs again and again but using construct context for each query would first grab the most N relevant (in context) docs then build the answer on these retrieved docs.

- how did they fix the increasing context length problem and made it efficient ?:
	- first of all they didnt 100% fixed , but partially fixed!
	  cuz of the increasing and still increasing context length!
		1. Grew the window size (more raw space).
		2. Built tests (NIAH, RULER) to measure _how well_ a model actually uses that space, not just how big it is.
		3. The practical recommendation coming out of this: if a model's performance drops as your prompt gets longer, **don't just rely on the bigger window** — shorten your prompt instead. And shortening the prompt intelligently (keeping only what's relevant) is exactly what RAG does
  
> fun fact: looking at 3, that's why AI devs tell all users to shorten their prompts and keeping what's relevant 

- (related to prev question) how RAG tried to mitigate extra cost ?:
  The longer the context, the more likely the model is to focus on the wrong part of the context. Every extra context token incurs extra cost and has the potential to add extra latency. **RAG allows a model to use only the most relevant information for each query, reducing the number of input tokens while potentially increasing the model’s performance.**




---
incoming section have related questions....

- what RAG architecture consists of?:
	1. Retriever: retriever that retrieves information from external memory sources
	2. Generator: generates a response based on the retrieved information

- Why does how you index data depend on how you'll retrieve it later?: 
	- There's no single universal indexing method. 
		- you design the index around the _search method_ someone will use later. 
		- Different retrieval approaches need completely different index structures.

- What are the two common retrieval approaches?:
	1. **Semantic (vector) retrieval** — "find documents _similar in meaning_ to this query."
	2. **Metadata/keyword (structured) retrieval** — "find documents matching _specific fields_."

- How do you index for semantic retrieval?: 
	Chunk the document, run each chunk through an embedding model, store the resulting vectors in a vector database (pgvector, Pinecone, Weaviate). At query time, the query gets embedded too, then compared via cosine similarity.

- How do you index for metadata/keyword retrieval?:
	Extract structured fields (date, source, category, author, file type) and store them in a table or index you can filter directly — like a normal SQL `WHERE` clause. No embeddings needed here.

- Do real systems use just one of these?: 
	  Usually not — most use **hybrid retrieval**: filter by metadata first, then run semantic search _within_ that filtered subset. Much faster than semantic-searching the whole dataset every time.

- (related to crawler task) Where does my `metadata.csv` + fixed path layout fit into this? : 
	That's exactly the hybrid indexing groundwork:

| What you built                                | What it enables at retrieval time                                                                                      |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `metadata.csv` (structured fields per file)   | Fast filtering — e.g. "only search government housing docs from 2024," `WHERE source='housing_ministry' AND year=2024` |
| Fixed path layout (predictable file location) | The retriever jumps straight to the right file/chunk without a full scan, once metadata narrows things down            |

- What does the crawler's output actually feed into?**  
	 Two separate things:
		1. **The content itself** → eventually chunked + embedded for semantic search.
		2. **The metadata.csv** → becomes the filter layer that narrows down _which_ chunks even get semantically searched.

- What happens if the crawler skips metadata?:
  Retrieval is forced into brute-force semantic search across everything — slower and less precise. Your setup avoids that by filtering first, searching second.
---

- (to LEAP) reading this paragraph, what retriver does LEAP uses in thier RAG system?  
	- `In today’s RAG systems, these two components are often trained separately, and many teams build their RAG systems using off-the-shelf retrievers and models. However, finetuning the whole RAG system end-to-end can improve its performance significantly`


# RAG retrieving algorithms

- What is term-based retrieval, and give an example of how it works?: 
	  Term-based retrieval is when the model retrieves all documents that contain the exact keyword from the query — for example, searching "AI engineering" returns every document that literally contains that phrase.
  
- Why is a long query problematic for term-based retrieval?: 
	  A long query contains many keywords, and without a way to rank them, all of them appear equally important, making it hard to know which keyword actually matters most for finding the right document.
  
- What does IDF (inverse document frequency) fix, and how?: 
	  IDF fixes the keyword-importance problem. It scores how rare a keyword is across the set of documents that contain it — the rarer (and therefore more distinctive) the keyword, the higher its score, meaning it's treated as more important for retrieval.
  
- Why does a keyword repeating very often inside a single document cause a resource problem?: 
	  If a keyword repeats often, it inflates the document's context length, since more of that keyword's occurrences get pulled into context, consuming more compute/memory resources than needed.

- what is Embedding-based retrieval?:
	converting the original data chunks into embedding then store them as a vectors in the vector database, which consists of two steps:
	- 1. convert the query into an embedding using the same embedding model used for the original data
	- 2. fetch chunks of data that has closest vectors to that query embedding 

- what's the easy and hard part with vector databases that used by embedding-based algorithm?:
	- easy: basically storing vectors
	- hard: searching vectors that are close to the query

- what are the Naive (very simple) steps to search vectors?
	  1. compute similarity scores between query embedding and all vectors in the database and using metrics such as cosine similarity
	  2. Rank all vectors by their similarity
	  3. return N vectors with the highest scores
	- to mention: steps sounds naive and simple but it's computationally heavy and slow. it should be used for small datasets

- since naive vector search cant be used for large datasets, what is used then?:
	  ANN (approximate nearest neighbor)

- some vector search algorithms and how they are special:
	- LSH (locality sensitive hashing): 
		  involves hashing similar vectors into the same buckets to speedup similarity search, trades some accuracy for efficinecy
		  
	- HNSW (Hierarchical Navigable small world):
		  constructs a multi-layer graph where nodes represent vectors, and edges connect similar vectors, allowing nearest-neighbor by traversing graph edges 
		  
	- and more like:
		- IVF (Inverted file index)
		- Annoy (approximate nearest neighbor Oh Yeah) not kidding it's the real name


# RAG optimization



# Drawing architecture
here i will draw some RAG systems arch diagram but first i will make the classic simple one with no additional things 
- no attachment RAG 

RAG components :
- retrieval
- generative model


==below is header of level two is all of 4 diagrams made before== 
## RAG Basic Architecture
simply a user sends a query (prompt), retriever has it's query, then the query goes and fetches the query from the external memory, generates a result in the generative model then responds back to user 

## RAG with Embedding-based retrievals
- **Index Block:** this time we have an indexing block and it simply takes raw data from external memory, chunks it into smaller chunks, embeds them and storing them as vectors in the vector data base 

- **main story:** 
	- 1. user sends query
	- 2. query goes to embedding model to be transformed into a query embedding (helps retriever to get relevant context to the query)
	- 3. retriever gets context relevant to that query then sends it to generative model
	- 4. generative model already has the query (the plain text) and the context relevant to query, generates the result and responds to user

## RAG with Term-based retrievals 
- retrieving data and chunking: this part retreives raw data from external database and chunks it then uses inverted index table , this table allows fast retrieval of a documents given a term 

- main story:
	- 1. user sends query 
	- 2. retriver gets the terms and fetches them from external datasource
	- 3. then sends these raw full data to splitter so context space wont choke 
	- 4. an inverted index table is made for quicker retreival 
	- 5. then generative model takes the query and generates a reponse
	- 6. reponse sent to user 


## RAG with hybrid (embedding + term) basis retrievals 
same scenarios but helps mostly the retriever part to make it efficient and precise 