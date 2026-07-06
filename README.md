# Vector Search in DuckDB: A Comprehensive Guide

Welcome to this repository where we explore the intriguing world of vector search within DuckDB, a fast in-process SQL OLAP database. This guide provides a step-by-step walkthrough, complete with code snippets and explanations, to demonstrate how DuckDB can be utilized as a local, zero-infrastructure vector database — combining SQL metadata filtering with cosine similarity search, without needing a dedicated vector database or a cloud data warehouse.

This is the open-source, 2026 sequel to [Vector Search in Snowflake](https://github.com/dhnanjay/Vector_Search_in_Snowflake). Same clever theme, same "use what you already have" philosophy — rebuilt on a stack that installs in under a minute and runs entirely on your own machine, for free.

Here is the blog entry on Medium: https://dhnanjay.medium.com/you-dont-need-a-vector-database-you-need-a-where-clause-e33407af215c?sharedUserId=dhnanjay

## Prerequisites

Before diving into the code and explanations, please ensure you have the following prerequisites covered:

- Python 3.9 or higher installed on your local machine.
- No cloud account, warehouse, or API key required — DuckDB runs in-process.
- (Optional) [Ollama](https://ollama.com) installed locally if you want to generate embeddings with `nomic-embed-text` instead of `sentence-transformers`. Pull the model with:
  - `ollama pull nomic-embed-text`
- Required Python libraries installed. You can install these libraries using the provided `requirements.txt` file:
  - `pip install -r requirements.txt`

## Repository Structure

This repository is structured as follows:

- `vector_search_duckdb.ipynb`: Jupyter notebook containing the code and detailed explanations for each step.
- `requirements.txt`: A text file listing all the required Python libraries.
- `data/`: See this : https://github.com/dhnanjay/Vector_Search_in_Snowflake
- `README.md`: This file, providing an overview and instructions for using this repository.

## What's Inside

The notebook walks through:

1. **Zero-config connection** — `duckdb.connect()`, no server, no credentials.
2. **The VSS extension** — installing and loading DuckDB's vector similarity search extension for HNSW indexing.
3. **A schema with intent** — a table that treats metadata (`source`, `category`, `published_date`, `sentiment`, `is_executive`) as first-class SQL-filterable columns, alongside a native `FLOAT[]` embedding column.
4. **Local embeddings** — generated via `sentence-transformers` (`BAAI/bge-small-en-v1.5`, 384-dim) or, optionally, Ollama's `nomic-embed-text` (768-dim) running fully offline.
5. **The hybrid search function** — the centerpiece: SQL `WHERE` clauses narrow the candidate set first (cheap, B-tree indexed), then exact cosine similarity runs only on that filtered subset. This pre-filter pattern is more accurate than post-filtering approximate HNSW results, and scales better as the corpus grows.
6. **Showtime** — the same style of worked queries as the original Snowflake notebook, run against sample news data, comparing full-corpus search against metadata-filtered hybrid search.

## Getting Started

1. **Clone the Repository**: Clone this repository to your local machine to get started.
   - `git clone https://github.com/dhnanjay/Vector_Search_in_duckdb.git`
   - `cd Vector_Search_in_duckdb`

2. **Install Dependencies**: Install the required Python libraries using the `requirements.txt` file.
   - `pip install -r requirements.txt`

3. **(Optional) Set Up Ollama**: If you want local LLM-based embeddings instead of `sentence-transformers`, install [Ollama](https://ollama.com) and pull the embedding model:
   - `ollama pull nomic-embed-text`
   - Set `USE_OLLAMA = True` in the notebook's config cell.

4. **Run the Notebook**: Open `vector_search_duckdb.ipynb` and run the cells in order. Embeddings are generated once and persisted to a local `.duckdb` file — subsequent runs skip re-embedding.

## Why DuckDB Instead of a Cloud Warehouse or Dedicated Vector Database

| | Snowflake (2023 approach) | Dedicated Vector DB | DuckDB (this repo) |
|---|---|---|---|
| Setup | Cloud account + warehouse | New infrastructure to deploy | `pip install duckdb` |
| Cost | Compute credits | Hosting / subscription | $0 |
| Embeddings | External API call | External API call | Local, offline |
| Metadata filtering | Manual JSON parsing | Post-filter on approximate results | SQL `WHERE`, pre-filtered, exact |
| Portability | Vendor-locked | Vendor-locked | Single file, runs anywhere |

## License

This project is open source and available under the [MIT License](LICENSE).

Happy coding and exploring! 🚀
