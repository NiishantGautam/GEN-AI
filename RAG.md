
> Why even care for RAG (Retrieval-Argumented Generation) ?

> With RAG, the model can retrieve relevant information from a data source, then uses that info to generate accurate, grounded responses.

Common Examples includes:

- **HR Assistant:** A company can use RAG to answer employee questions about vacation policies, benefits, or procedures by retrieving the latest HR documents.
- **Customer Support:** E-commerce sites use RAG to pull up-to-date return policies or product info from their knowledge base, so customers get accurate answers.
- **Legal Research:** Law firms use RAG to fetch relevant case law or statutes, then generate summaries or recommendations based on current legal documents.
- **Immigration Assistant**: Retrieve the latest immigration policies or government updates from official sources.  Generate clear, up-to-date answers for people asking about visa requirements or application steps.
----

#### History and Introduction

<mark style="background: #BBFABBA6;">Phase 1: Classic IR Systems</mark>

Early systems (like library databases or early Google) focused on keyword matching.  We type something and get list of articles, and we had to read them all to find the answer we were looking for. This is how information Retrieval (IR) used to work before.

<mark style="background: #BBFABBA6;">Phase 2: Generative AI Breakthrough</mark>

With advances in NLP, LLMs like GPT-3 moved beyond just retrieving documents to generating relevant, fluent responses. But they had weaknesses: **factual reliability** (_hallucinations_, where models make up facts they don't know about) and **static knowledge** (e.g., an LLM trained on data up to 2021 can't know 2023 medical guidelines). Imagine asking, "What's the latest policy for treating this particular disease?" and getting a plausible-sounding, but outdated or wrong answer.

<mark style="background: #BBFABBA6;">Phase 3: RAG Bridges the Gap</mark>

RAG solves this by first retrieving up-to-date or domain-specific data from a designated source (e.g., latest guidelines, internal documents, ...). The model isn't relying solely on internal parameters—it's explicitly pulling relevant, up-to-date data. 

In simple terms, RAG uses IR techniques to fetch the most relevant data first and then feeds that data into a generative model. As the model crafts its response, it draws on these retrieved sources, which **ground** its output in reality (reducing the risk of making facts up).

- **Factual Guardrails**: Retrieval acts as a "safety net" against hallucinations, making RAG systems less likely to invent or mix up information.
- **Dynamic Knowledge**: Update answers by updating the data source — no need to retrain the LLM! (_Note: the retrieval system must be periodically refreshed with new data - RAG is not a "set and forget" technique._)
- **Domain-Specific Expertise**: RAG can be tailored using specialized databases or documents, such as medical records or legal documents.

-----
#### Simple RAG Workflow

The idea is if we don't have any production data, we can always start with text chunks and start producing synthetic questions that we can verify are correct. Then once we have a baseline set  of evals, we can take these and turn them into few shot examples or larger data sets that we can use to fine tune dataset. 

> - Starting with text chunks and generating synthetic questions is a common way to bootstrap a knowledge base and evaluation set when real data is missing.

> - These synthetic Q&A pairs help us test retrieval and generation, and can later be used as few-shot examples or for fine-tuning models.


<mark style="background: #FFF3A3A6;">Common Terminologies we have:</mark>

- **Baselines** help you measure if new methods are actually better or just different. Without them, you can’t tell if you’re improving.
- **Recall**: Out of all relevant documents, how many did your system find? (High recall = few missed answers.)
- **Precision**: Out of all documents your system returned, how many were actually relevant? (High precision = few wrong answers.)
- **Lexical search**: Finds matches based on exact words (like keyword matching).
- **Semantic search**: Finds matches based on meaning, even if the words are different (often uses embeddings).
- **Re-rankers**: After initial search, these reorder results to put the most relevant ones at the top (using more advanced models).






To build a simple RAG workflow, there are 4 steps 

1. **Indexing**: Documents are structured in a way that makes them easy to search.
2. **Retrieval**: The most relevant piece of text is fetched based on a user query.
3. **Prompt (Query) Augmentation**: The retrieved text is combined with the user's question to form a context-rich prompt.
4. **Generation**: A language model processes the prompt and produces a final answer anchored to the provided text.

For Indexing our documents can be anything like articles, emails, PDFS, web pages etc. We can have various formats of our documents like:
- Industry standard formats depend on the use case and tools:
    - For small/simple projects: JSON, CSV, or dictionaries/arrays (like in Python) are common.
    - For production: Data is often stored in databases (SQL, NoSQL), or indexed in vector databases for RAG.
    - Many systems also extract text from PDFs, DOCX, or HTML, then store it in a structured format.


Now after we have our indexing ready, we need to retrieve it. 
