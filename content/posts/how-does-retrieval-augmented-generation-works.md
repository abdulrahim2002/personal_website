---
date: '2025-12-26T20:58:56+01:00'
draft: true
title: 'How Does Retrieval Augmented Generation Work'
author: 'Abdul'
---

### Introduction

**RAG (Retrieval-Augmented Generation)** is a technique where a model
can generate text based on external information retrieved from a
knowledge base, rather than relying solely on its pre-trained knowledge.  

The knowledge base is a collection of documents  

$$
D = \{ d_1, d_2, \dots, d_n \}
$$  

such that, whenever text is generated, the information is based on the
knowledge base as much as possible.  

There are two components to a RAG model:

1. **Retriever:** Given a query $q$, the retriever finds all documents
   $d \in D$ that may be relevant to the query $q$.  
2. **Generator:** This is the text generation system—typically a large
   language model (like GPT)—that actually generates text given a
   prompt.  

## How It Works

1. **Training Phase (for RAG Retriever only):**  During training, the
   retriever learns to retrieve relevant documents or information from a
   knowledge base. There are various available datasets.  You can also
   use tools like **FAISS**, **ElasticSearch**, or a **pre-trained
   retrieval model** like a **Dense Retriever**.  

2. **Inference Phase (for RAG models):**  Inference involves using a
   pre-trained model to generate responses.  The model performs two key
   steps during inference:
   - **Retrieval:** It pulls in external documents or information from a
     knowledge base or database. This could be a search engine, vector
     store, or large document corpus.  
   - **Generation:** The model then uses the retrieved information to
     generate a more accurate and context-aware response.  

## Training the Retriever

The process for training a retrieval model generally involves these key
steps:

1. **Prepare the Dataset:**  

 Before training a retriever, you'll need:
   - A **large document corpus** (e.g., all Wikipedia articles, a
     collection of articles, or PDF files). This acts as our knowledge
     base $D$.
   - A **query-document pair dataset** containing queries and their
     corresponding relevant documents. This is crucial for training a
     retrieval model.  

   **Example:**  
   - Query: *"What is the capital of France?"*  
   - Document: *"...The capital of France is Paris..."* (content of the
     document)  
   - You might use datasets such as **MS MARCO** or **TREC**.  

2. **Choose a Retrieval Model:**  

There are different types of retrieval models you can use. Common ones include:

   - **TF-IDF (Term Frequency–Inverse Document Frequency):** Represents
     queries and documents as word vectors and computes similarity
     (often using cosine similarity).  
   - **Dense Retrieval Models:** Use deep learning to create dense
     vector representations of both queries and documents. Examples
     include:
     - **DPR (Dense Passage Retrieval):** A two-tower model (one for
       queries, one for documents) using neural networks to map queries
       and documents into vector spaces, then measuring similarity
       between the vectors.  
     - **ColBERT (Contextualized Late Interaction Retrieval):** Uses
       BERT embeddings for dense retrieval, offering a trade-off between
       efficiency and accuracy.  
     - **T5, BERT, or other Transformer-based Models:** These can be
       fine-tuned for retrieval tasks and trained to map documents and
       queries into a shared vector space.  

3. **Retrieve Top-$k$ Similar Documents:**  
   The retriever should be able to fetch the top-$k$ most similar documents $d \in D$.  

4. **Useful Libraries:**  
   Examples include **FAISS**, **Anserini/Pyserini**, and **DPR** (from Hugging Face).  

## How to Make the Generated Text Cite Retrieved or Context Documents

Once the documents have been retrieved, they are inserted into the
prompt as string input and assigned unique labels (e.g., `doc1`, `doc2`,
etc.).  In the system prompt, you can instruct the model to base its
responses on the given context and provide citations where possible.  

This process works as follows:

### Step 1: User Query

**Query:**  

```
What are the symptoms of iron deficiency?
```

### Step 2: Retriever Fetches Relevant Documents

```
Doc 1: "Iron deficiency symptoms include fatigue, pale skin, dizziness..."  
Doc 2: "Common signs are weakness, shortness of breath, and headaches..."
```

### Step 3: Construct the Prompt

```
SYSTEM:

You are a helpful assistant. Answer the question using only the provided
context.  When you state a fact, cite the source document using [DocX].
If the answer is not in the context, say "Not found in provided
documents."

CONTEXT:

Iron deficiency symptoms include fatigue, pale skin, dizziness...  
Common signs are weakness, shortness of breath, and headaches...

USER:

What are the symptoms of iron deficiency?
```

### Step 4: Generated Output

```
Iron deficiency can cause fatigue and pale skin [Doc1].  
It may also lead to weakness and headaches [Doc2].  
```

Finally, you can hyperlink citations in the user interface to link back
to their original documents—similar to how **Perplexity AI** handles
cited answers.  
