Please note that the following text is an overview of the concept of RAG as I understand it.

### Introduction to RAG
Retrieval-Augmented Generation (RAG) is a technique that allows a (large) language model (LLM) to generate text using knowledge retrieved from an external source. As a direct consequence, RAG solves the issue of training an LLM on specific knowledge, which is costly and time-consuming.

### RAG Architecture
We can define a RAG application as a combination of two main components:
- **Indexing**. we load the data, split it into chunks, store it (e.g. with VectorStore) and index it.
- **Retrieval and generation**: we ask the LM a query, it retrieves the relevant splits, and uses both the prompt and the retrieved data to answer.

When retrieving the data, we can use different strategies:
- **Dense vector search**. The content gets converted into embedding vectors, capturing semantic meaning (also called semantic search or similarity search). We can use an embedding model to convert the text into vectors, a vector store to store the vectors, and a similarity search algorithm (like *cosine similarity*) to find the most relevant vectors.
- **Sparse vector search**. The content gets converted into sparse high-dimensional vectors with mostly zero values. They represent text with non-zero entries for the terms that appear. We can use TF-IDF (Term Frequency-Inverse Document Frequency) to encode the text, but a more efficient method is BM25. This ranking function doesn't build a full document vector, only an inverted index to compute stats and apply the ranking score formula. The remaining steps are the same as for the dense vector search.

Example of dense and sparse vectors:

```python
dense = [0.2, 0.3, 0.5, 0.7, ...]  # several hundred floats
sparse = [{331: 0.5}, {14136: 0.7}]  # 20 key value pairs
```

Sparse vectors are important in information retrieval, especially in ranking and search systems. They are computationally efficient but less dense, as they focus on key features (e.g. which words are present in the text). They are very useful for niche domains like medical or legal texts, where there are rare keywords. They are less useful when we want to capture nuanced relationships between words.

Note: TF-IDF gives more importance to rare words and less to common words. TF-IDF(doc, term) = TF(doc, term) * log(N / DF(term)), where N = # of documents, DF = # of documents containing the term.

We usually prefer dense vectors in RAG applications, but hybrid search is also possible. In single-webpage-rag-application.ipynb and offline-documents-rag-application.ipynb, we will use dense vectors. In haystack-rag-application.ipynb, we will use a hybrid approach using a cross-encoder model to rank results from keyword-based and semantic search.

The cross-encoder model re-ranks the results from the two retrieval methods. It takes a pair of (query, document) as input and it outputs the relevance score of the document for the query. This differs from the bi-encoder model, which separately encodes the query and the document, and then computes the similarity score between the two vectors. The difference is that by concatenating them, the model can capture finer relevance signals than BM25 or dense vector search. 

```scss
[CLS] query text [SEP] document text [SEP]
```

