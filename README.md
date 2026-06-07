# Company Intelligence Extractor

An end-to-end ETL and clustering pipeline that extracts, deduplicates, and maps UK companies from the Companies House API, then clusters them by semantic similarity using NLP embeddings.

## Overview

Built to surface market intelligence across the UK tech and software sector. The pipeline extracts company metadata and web content, generates dense semantic embeddings, and applies unsupervised clustering to group organisations by business focus — producing an interactive 2D visualisation of the UK company landscape.

## Architecture

```
Companies House API
        ↓
Extract & Paginate (50 parallel workers)
        ↓
Deduplicate on Company Number
        ↓
Filter by SIC Code & Incorporation Date
        ↓
Discover & Verify Websites (parallel HTTP)
        ↓
Scrape Web Content
        ↓
Embed with sentence-transformers (all-MiniLM-L6-v2)
        ↓
Cluster with HDBSCAN
        ↓
Reduce to 2D with UMAP → Visualise with Plotly
```

## Results

- **74,000+** UK companies extracted across 8 SIC codes
- **13,177** companies after deduplication and verification
- **50 parallel workers** for website discovery
- Clusters visualised interactively in 2D

## Stack

| Component | Tool |
|-----------|------|
| Data Source | Companies House REST API |
| Parallelisation | `concurrent.futures.ThreadPoolExecutor` |
| Data Processing | `pandas` |
| Embeddings | `sentence-transformers` (all-MiniLM-L6-v2) |
| Clustering | `HDBSCAN` |
| Dimensionality Reduction | `UMAP` |
| Visualisation | `Plotly` |

## Setup

1. Install dependencies:
```bash
pip install pandas requests sentence-transformers hdbscan umap-learn plotly tqdm
```

2. Get a free Companies House API key at [developer.company-information.service.gov.uk](https://developer.company-information.service.gov.uk)

3. Set your API key:
```bash
export COMPANIES_HOUSE_API_KEY=your_key_here
```

4. Run the notebook:
```bash
jupyter notebook ai_scrapper_api.ipynb
```

## Output Files

| File | Included | Description |
|------|----------|-------------|
| `companies_api.csv` | ✅ | Raw API extract |
| `companies_api_clean.csv` | ✅ | Deduplicated and filtered |
| `companies_verified.csv` | ✅ | With verified web presence |
| `companies_meta.csv` | ✅ | Company metadata |
| `companies_clustered.csv` | ✅ | With cluster assignments |
| `companies_final.csv` | ✅ | Final cleaned dataset |
| `clusters_final.html` | ✅ | Interactive UMAP visualisation |
| `clusters_final_labelled.html` | ✅ | Cluster visualisation with labels |
| `companies_clean.csv` | ❌ | Excluded — 43MB |
| `companies_scraped.csv` | ❌ | Excluded — 23MB |
| `embeddings.npy` | ❌ | Excluded — 20MB, regenerate by running the notebook |

> **Note:** To regenerate `embeddings.npy`, run the embedding cell in the notebook. It will recompute from `companies_final.csv` using `sentence-transformers`.

## SIC Codes Covered

- 62011 — Ready-made interactive leisure and entertainment software development
- 62012 — Business and domestic software development
- 62020 — Information technology consultancy activities
- 62090 — Other information technology service activities
- 63110 — Data processing, hosting and related activities
- 63120 — Web portals
- 63990 — Other information service activities
- 64999 — Other financial service activities

## Related Project

This project's embeddings and company data power the [Company Intelligence Q&A RAG System](../company-rag/) — a retrieval-augmented generation pipeline for natural language queries over the extracted company dataset.
