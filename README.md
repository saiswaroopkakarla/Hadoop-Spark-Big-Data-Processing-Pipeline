# Hadoop - Spark - Big Data Processing Pipeline

An end-to-end big data assignment demonstrating **Apache Hadoop MapReduce** and **Apache Spark** pipelines on real text corpora — covering word count, TF-IDF feature engineering, cosine similarity, and influence network modelling.

**Course:** CSL7110 ML with Big Data, IIT Jodhpur  
**Environment:** Ubuntu 22.04 (WSL2), single-node setup

---

## Repository Structure

```
.
├── spark_assign/
│   ├── metadata_extraction.py   # Spark DataFrame regex metadata extraction
│   ├── tfidf_similarity.py      # TF-IDF pipeline + cosine similarity (Spark UDF)
│   └── author_network.py        # Influence network from similarity edge list
│
├── wc_project/
│   ├── WordCount.java            # Custom MapReduce Mapper + Reducer
│   ├── wc.jar                    # Compiled JAR (ready to run)
│   └── mywc.txt                  # Sample input text
│
└── README.md
```

---

## Part 1 — Hadoop MapReduce

### Built-in WordCount

Executed Hadoop's example WordCount job on a sample dataset to validate HDFS + YARN setup.

### Custom WordCount (Java MapReduce)

Implemented a custom `Mapper` and `Reducer` in Java with:
- Lowercase normalisation
- Punctuation removal
- Tokenisation and aggregation

**Run:**
```bash
hadoop jar wc_project/wc.jar WordCount /input /output
```

### Large Dataset Experiment

Ran custom WordCount on a Project Gutenberg book (~8 MB) uploaded to HDFS. Measured execution time and observed how modifying the HDFS **split size** changes the number of spawned Mappers demonstrating parallelism control.

---

## Part 2 Apache Spark

### Metadata Extraction (`metadata_extraction.py`)

Loads book text into a Spark DataFrame and extracts metadata (title, author) using regex. Implements a fallback title extractor to handle inconsistent headers and computes simple metadata statistics.

```bash
spark-submit spark_assign/metadata_extraction.py
```

### TF-IDF Feature Engineering (`tfidf_similarity.py`)

Full NLP pipeline using Spark MLlib:

```
Raw text → Tokenization → Stopword removal → HashingTF → IDF → TF-IDF vectors
```

Cosine similarity is computed between document vectors using a **Spark UDF**, producing pairwise similarity scores.

```bash
spark-submit spark_assign/tfidf_similarity.py
```

### Influence Network (`author_network.py`)

Constructs a graph-style influence model from TF-IDF similarity scores:
- Builds a similarity edge list between documents
- Constructs a node list
- Computes an **influence score** per node as the sum of its outgoing similarity weights

```bash
spark-submit spark_assign/author_network.py
```

---

## Setup

### Prerequisites

- Hadoop 3.3.6
- Apache Spark 3.5.x
- Java 8+
- Python 3.x + `pyspark`

### Hadoop Configuration

Key files configured for single-node pseudo-distributed mode:
- `core-site.xml` — NameNode URI
- `hdfs-site.xml` — replication factor = 1
- `yarn-site.xml` — ResourceManager settings

Verify setup:
```bash
jps   # Should show NameNode, DataNode, ResourceManager, NodeManager
```

### Python Dependencies

```bash
pip install pyspark
```

---

## Key Concepts Demonstrated

| Concept | Where |
|---|---|
| HDFS storage model | WordCount experiment |
| MapReduce execution flow | Custom Java MapReduce |
| Split size impact on parallelism | Large dataset experiment |
| Replication factor tradeoffs | HDFS configuration |
| Regex extraction + fallback logic | metadata_extraction.py |
| TF vs IDF weighting intuition | tfidf_similarity.py |
| Vector cosine similarity via UDF | tfidf_similarity.py |
| Graph influence scoring | author_network.py |

---

## Author

**Kakarla Sai Swaroop**  
M25DE1023 IIT Jodhpur, M.Tech Data Engineering
