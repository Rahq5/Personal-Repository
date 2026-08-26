
# Intro
it allows programs to read , write and communicate in human language. specific problems like speech recognition, machine translation, information extraction.
# Problem and fix
- Problem:
	Early AI tried to understand language using grammar rules and networks of connected concepts. The main issue: word-sense disambiguation — picking the right meaning when a word has more than one (like "bank" = river edge or money place).
	
	This only worked in "micro-worlds" — tiny, simplified environments with few objects and rules. Outside those, AI failed because of the common sense knowledge problem: it lacked the huge pool of unstated, everyday knowledge humans use to guess meaning correctly.

- fix: 
	Instead of hand-coded grammar rules, modern AI uses word embeddings — turning words into vectors (numbers representing meaning) so similar meanings sit close together mathematically. This lets the system "sense" that "bank" near "river" and "bank" near "money" are different, based on context, without needing explicit rules.
	
	Then transformers (a model architecture using "attention" — a mechanism that lets the model weigh which nearby words matter most for understanding a given word) let AI look at an entire sentence at once instead of word-by-word, capturing context far better than old systems.

# key Parts of NLP
**key parts of NLP**:
- understanding: programs figure out what words mean and what the user wants
- generating: computers write or speak clear answers sounds like human 
- processing text: systems break snetences to find names, facts and feeling


# How it works and where mostly used?
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

