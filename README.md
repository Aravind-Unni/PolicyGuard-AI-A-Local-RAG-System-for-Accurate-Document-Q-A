# PolicyGuard AI: Local RAG for Document Q&A 🛡️

**PolicyGuard AI** is a privacy-first **Retrieval-Augmented Generation (RAG)** system designed to answer user questions based on specific policy documents (e.g., Return & Refund Policies).

It runs **entirely locally** using **Llama 3.2**, ensuring that sensitive data never leaves your machine. The system includes a robust evaluation pipeline to measure accuracy, hallucination avoidance, and answer clarity.

---

## 🚀 Key Features

* **Local Inference:** Uses `Llama 3.2` via Ollama for zero-cost, private processing.
* **Vector Search:** Utilizes **ChromaDB** and huggingface `sentence-transformers`model for semantic retrieval .
* **Hallucination Guardrails:** Custom prompt engineering strictly bounds the LLM to the provided context.
* **Evaluation Suite:** Includes an "LLM-as-a-Judge" module to grade answers on accuracy and clarity automatically.
* **Smart Retrieval:** Implements text chunking (800 chars) and overlap (150 chars) to maintain context across boundaries.

---

## 🏗️ Architecture Overview

The pipeline follows a standard RAG workflow optimized for local execution:

1.  **Ingestion:** The PDF policy document (`Bazaar-Return-Refund.pdf`) is loaded using `PyPDFLoader`.
2.  **Chunking:** Text is split into chunks of 800 characters with a 150-character overlap using `RecursiveCharacterTextSplitter`.
3.  **Embedding:** Chunks are converted into vector embeddings using the `sentence-transformers/all-MiniLM-L6-v2` model accessed using huggingface API key.
4.  **Storage:** Embeddings are stored locally in a **ChromaDB** vector database.
5.  **Retrieval:** When a user asks a question, the system retrieves the top 3-6 most relevant chunks.
6.  **Generation:** The retrieved context and user question are passed to **Llama 3.2** (via Ollama) using a strict prompt template.

---

## 🛠️ Setup Instructions

### 1. Prerequisites
* **Python 3.10+**
* **Ollama:** Download and install from [ollama.com](https://ollama.com/).

### 2. Install Dependencies
Navigate to the `src` folder and install the required Python libraries:

```bash
cd src
pip install -r requirements.txt
ollama pull llama3.2

```
📝 Prompt Engineering

You are a specific Policy Assistant for Rainbow Bazaar.
Your goal is to answer the user question based ONLY on the provided context chunks.

CONTEXT:
{context}

USER QUESTION:
{question}

---
STRICT RULES:
1. **Focus:** Answer the question directly. Do not comment on the quality or repetition of the context text.
2. **Grounding:** If the answer is found in *any* part of the context, use it. Ignore duplicates.
3. **No Filler:** Do not start with "I apologize" or "The context mentions." Start directly with the answer.
4. **Citation:** Support your answer with source IDs.
5. **Missing Info:** If the answer is strictly NOT in the context, say: "I cannot find this information in the policy."

FORMAT:
**Answer:** [Direct Answer]
**Details:** [Bullet points with citations]


📊 Evaluation Results


| Metric | Score | Description |
| :--- | :--- | :--- |
| **Overall Accuracy** | **88.9%** | Percentage of questions answered correctly according to the rubric. |
| **Hallucination Avoidance** | **66.7%** | Success rate in correctly refusing to answer out-of-scope questions (e.g., "Who is the CEO?"). |
| **Answer Clarity** | **75.6%** | Average score (3.8/5.0) for coherence and formatting. |
