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
```
## System Pipeline

### 1. Preprocessing

The data is cleaned and normalized. A repository-level split is used to reduce data leakage by ensuring that the same repository does not appear in multiple splits.

### 2. Candidate Pool Generation

For each query, candidate pools are created with one correct code snippet and many hard negatives.

### 3. Representation Extraction

Several representations are tested:

* TF-IDF
* BM25
* Manual handcrafted features
* CodeBERT embeddings
* UniXcoder embeddings
* GTE embeddings
* FastText embeddings

### 4. Scoring and Ranking

Different scoring and ranking methods are evaluated:

* Cosine similarity
* Logistic Regression
* Random Forest
* LightGBM pointwise ranking
* LightGBM pairwise ranking
* Dual-Encoder Deep Learning ranker
* BM25 hybrid fusion

### 5. Evaluation

The ranked results are evaluated using standard information retrieval metrics.

---

## Evaluation Metrics

Because this is a retrieval task, ranking metrics are more important than classification metrics.

The main metrics used are:

* MRR – Mean Reciprocal Rank
* Recall@1
* Recall@5
* Recall@10

MRR measures how high the first correct result appears in the ranked list. Recall@K measures whether the correct code appears within the top K retrieved results.

---

## Methods Tested

### Lexical Baselines

The first baseline methods were:

* TF-IDF with cosine similarity
* BM25 ranking

These methods are useful starting points, but they depend heavily on token overlap between the query and code.

### Manual Feature-Based Models

A branch of the project used **22 handcrafted features**, grouped into:

* Textual similarity features
* Code-structural features
* Query/function-level features

The following models were tested:

* Logistic Regression
* Random Forest
* LightGBM

Manual features gave strong results, especially with LightGBM, but the approach did not scale well to very large candidate pools because feature extraction and pool generation became slow and memory-intensive.

### Pretrained Embeddings

The project tested both code-specific and general-purpose embeddings:

* CodeBERT
* UniXcoder
* GTE
* FastText

GTE achieved the strongest single-representation performance, even though it is not specifically trained for code retrieval.

### Learning-Based Rankers

Machine learning and deep learning rankers were tested on top of embeddings.

The results showed that learning-based rankers helped weak embeddings such as FastText, but they did not clearly improve over direct cosine similarity when the embedding was already strong, especially for GTE.

### BM25 Hybrid Fusion

Hybrid fusion was tested by combining semantic embeddings with BM25 lexical scores.

The main findings were:

* BM25 strongly improved weak FastText embeddings.
* BM25 added only a small improvement to strong GTE embeddings.

---

## Key Results

### Lexical Baselines

| Method | Pool |    MRR | Recall@1 |
| ------ | ---: | -----: | -------: |
| TF-IDF |   25 | 0.5393 |   0.4464 |
| BM25   |   25 | 0.5617 |   0.4772 |
| TF-IDF | 1000 | 0.4397 |   0.3485 |
| BM25   | 1000 | 0.4247 |   0.3265 |

### Manual Feature-Based LightGBM

| Candidate Pool |    MRR | Recall@1 |
| -------------: | -----: | -------: |
|             25 | 0.9119 |   0.8730 |
|            100 | 0.8398 |   0.7773 |
|            500 | 0.7275 |   0.6410 |

### Embeddings with Cosine Similarity

| Representation | Pool |    MRR | Recall@1 |
| -------------- | ---: | -----: | -------: |
| GTE            |   25 | 0.9922 |   0.9874 |
| GTE            | 1000 | 0.9441 |   0.9122 |
| CodeBERT       |   25 | 0.9755 |   0.9608 |
| CodeBERT       | 1000 | 0.8451 |   0.7769 |
| UniXcoder      |   25 | 0.9711 |   0.9542 |
| UniXcoder      | 1000 | 0.8143 |   0.7378 |
| FastText       |   25 | 0.4145 |   0.2531 |
| FastText       | 1000 | 0.0912 |   0.0562 |

### BM25 Hybrid Fusion

| Method                              | Pool 1000 MRR | Pool 1000 Recall@1 |
| ----------------------------------- | ------------: | -----------------: |
| FastText cosine                     |        0.0912 |             0.0562 |
| FastText + BM25 + LightGBM pairwise |        0.7058 |             0.6076 |
| GTE cosine                          |        0.9441 |             0.9122 |
| GTE + BM25 + LightGBM pairwise      |        0.9376 |             0.8992 |

### Reliability Test

The larger reliability test was performed using **22,176 queries** and a **1,000-candidate pool**.

| Representation |    MRR | Recall@1 | Recall@5 | Recall@10 |
| -------------- | -----: | -------: | -------: | --------: |
| GTE-base       | 0.9329 |   0.9020 |   0.9698 |    0.9790 |
| CodeBERT       | 0.8014 |   0.7483 |   0.8642 |    0.9039 |
| UniXcoder      | 0.7910 |   0.7295 |   0.8638 |    0.8958 |
| BM25           | 0.4443 |   0.3657 |   0.5246 |    0.5949 |
| TF-IDF         | 0.4424 |   0.3417 |   0.5564 |    0.6452 |
| FastText       | 0.0472 |   0.0197 |   0.0562 |    0.0868 |

---

## Main Findings

The main conclusions of the project are:

* Lexical methods such as TF-IDF and BM25 are useful baselines, but they are limited because they mainly depend on token overlap.
* Manual handcrafted features can perform well, especially with LightGBM, but they are not very scalable.
* Code-specific embeddings such as CodeBERT and UniXcoder perform much better than lexical baselines.
* GTE was the strongest single representation in this project, even though it is a general-purpose embedding model.
* More complex models did not always produce better results.
* Deep learning rankers helped weak embeddings such as FastText but did not outperform direct GTE cosine similarity.
* BM25 fusion greatly improved FastText but only slightly changed GTE performance.
* The larger reliability test confirmed that the model ranking was stable, with GTE remaining the best-performing representation.

---

## Future Work

Possible future directions include:

* Testing on larger datasets
* Evaluating more embedding models
* Applying the system to more programming languages
* Investigating why GTE performs so well on Python code retrieval
* Testing Reciprocal Rank Fusion between the strongest models
* Improving scalability for manual feature extraction
* Building a user-facing interface for practical code search

---

## Repository Status

This repository currently documents the research, experiments, and results of the project.

Code, notebooks, datasets, and additional experiment files may be added in future updates.



