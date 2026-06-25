# A Python-Based Code Retrieval System

This repository presents a research project on **Python code retrieval**

The goal of the project is to build and evaluate a system that can retrieve relevant Python code snippets from a natural-language or pseudocode-like query. Instead of writing code from scratch, the system helps search, reuse, and modify existing code.


## Motivation

Writing code from scratch can be slow, and AI-generated code often needs repeated correction. Code retrieval offers another approach: finding existing code snippets that match a user’s intent.

However, this task is challenging because natural language and source code have very different structures. A user may describe what they want in English or pseudocode, while the system must find the correct Python function from many possible candidates.

This project treats code retrieval as a **ranking problem**: for each query, the system ranks candidate code snippets so that the correct snippet appears as high as possible.

---

## Research Goal

The main goal is to build and evaluate a Python code retrieval system that ranks relevant code snippets for natural-language or pseudocode-like queries.

The project investigates the following questions:

1. How useful are lexical methods such as TF-IDF and BM25?
2. Can manually designed features help retrieve code effectively?
3. Do code-specific embeddings such as CodeBERT and UniXcoder perform better than lexical methods?
4. Can a general-purpose embedding model such as GTE compete with code-specific embeddings?
5. Do machine learning or deep learning rankers improve retrieval performance?
6. Does combining semantic embeddings with BM25 improve ranking?
7. Are the results stable on a larger evaluation set?

---

## Dataset

The project uses a selected subset of the **CodeSearchNet Python dataset**.

### Main dataset setup

- About **21,000 Python code-description samples**
- Repository-level train / validation / test split
- Query-code pairs generated from natural-language descriptions and Python functions
- Candidate pools created for ranking evaluation

### Candidate pools

Each query is evaluated with a candidate pool containing:

- **1 correct Python code snippet**
- Multiple hard negative snippets

Two main candidate pool sizes were used:

- **25 candidates**
- **1,000 candidates**

A larger reliability test was also performed using:

- **22,176 queries**
- **1,000-candidate pool**

CoNaLa was tested at the beginning but discarded because the results were unrealistically high and did not provide a challenging enough evaluation setting.

---

## System Pipeline

The retrieval pipeline follows these main steps:

```text
Dataset
   ↓
Preprocessing
   ↓
Candidate Pool Generation
   ↓
Representation Extraction
   ↓
Scoring / Ranking
   ↓
Evaluation
