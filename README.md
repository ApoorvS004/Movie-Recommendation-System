# Semantic Movie Recommender System

A content-based movie recommendation system that matches user queries and plot descriptions to movies using dense vector embeddings rather than raw keyword matching.

Instead of traditional TF-IDF or BM25 (which fail when a query uses synonyms or describes a vibe without exact keywords), this system maps plot summaries into 384-dimensional vector space and performs nearest-neighbor search to retrieve relevant films.

---

## How It Works

1. **Dataset**: Movie metadata and plot summaries pulled from The Movie Database (TMDB).
2. **Embedding**: Passages are vectorized using `BAAI/bge-small-en-v1.5` run locally on CPU via the FastEmbed ONNX runtime. Generates normalized 384-dimensional vectors.
3. **Indexing**: Vectors and metadata payloads (title, year, genres, TMDB ID) are indexed into a managed **Qdrant Cloud** cluster using HNSW graphs scored with Cosine similarity.
4. **Backend API**: A **FastAPI** service exposes an async endpoint `/recommend` that encodes incoming search text, queries Qdrant for top-k nearest neighbors, and fetches poster art and IMDb links from the TMDB API before returning the response.
5. **Frontend**: Simple React interface to display search results as movie cards with similarity scores.

---

## Pipeline Overview

```text
User Query (e.g. "astronaut stranded near jupiter")
       │
       ▼
[FastAPI /recommend endpoint]
       │
       ▼
[FastEmbed ONNX Runtime (bge-small-en-v1.5)] ──► 384-d Query Vector
       │
       ▼
[Qdrant Cloud (HNSW Index)] ──────────────────► Cosine Similarity Top-K IDs
       │
       ▼
[TMDB API Hydration] ─────────────────────────► Posters, Directors, IMDb Links
       │
       ▼
JSON Response ──► React UI
```
│
## Tech Stack

- **Language:** Python 3.11
- **Embedding Model:** `BAAI/bge-small-en-v1.5` (FastEmbed / ONNX runtime)
- **Vector Database:** Qdrant Cloud (HNSW indexing, Cosine distance)
- **API Framework:** FastAPI, Uvicorn
- **External API:** TMDB REST API (metadata hydration)
- **Frontend:** React
- **Deployment:** Render (Backend API) + Qdrant Cloud (Vector DB)


▼
JSON Response ──► React UI
