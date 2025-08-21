Please note that the following text is an overview of the concept of RAG as I understand it.

### Introduction to RAG
Retrieval-Augmented Generation (RAG) is a technique that allows a (large) language model (LLM) to generate text using knowledge retrieved from an external source. As a direct consequence, RAG solves the issue of training an LLM on specific knowledge, which is costly and time-consuming.

### RAG Architecture
We can define a RAG application as a combination of two main components:
- **Indexing**. we load the data, split it into chunks, store it (e.g. with VectorStore) and index it.
- **Retrieval and generation**: we ask the LM a query, it retrieves the relevant splits, and uses both the prompt and the retrieved data to answer.