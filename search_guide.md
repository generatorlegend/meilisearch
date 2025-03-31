---
title: Search Guide
description: Learn how to perform searches using Meilisearch, including keyword, semantic, and hybrid search options.
---

# Search Guide

This guide will walk you through the process of performing searches using Meilisearch. We'll cover different search options, including keyword search, semantic search, and hybrid search. You'll also learn how to create complex queries, apply filters, sort results, and interpret search results.

## Table of Contents

1. [Basic Search](#basic-search)
2. [Search Options](#search-options)
   - [Keyword Search](#keyword-search)
   - [Semantic Search](#semantic-search)
   - [Hybrid Search](#hybrid-search)
3. [Advanced Query Techniques](#advanced-query-techniques)
   - [Filtering](#filtering)
   - [Sorting](#sorting)
   - [Pagination](#pagination)
4. [Interpreting Search Results](#interpreting-search-results)
5. [Examples](#examples)

## Basic Search

To perform a basic search using Meilisearch, you can use the `search` method on your index. Here's a simple example:

```rust
let search_query = SearchQuery {
    q: Some("your search query".to_string()),
    offset: 0,
    limit: 20,
    ..Default::default()
};

let search_result = index.search(&search_query).await?;
```

This will return a `SearchResult` object containing the matching documents and other relevant information.

## Search Options

Meilisearch offers different search options to cater to various use cases:

### Keyword Search

Keyword search is the default search mode in Meilisearch. It looks for exact matches or close variations of the search terms in the indexed documents.

```rust
let search_query = SearchQuery {
    q: Some("keyword search".to_string()),
    ..Default::default()
};
```

### Semantic Search

Semantic search uses vector embeddings to find documents that are semantically similar to the query, even if they don't contain the exact keywords.

To use semantic search, you need to configure an embedder and provide a vector:

```rust
let search_query = SearchQuery {
    vector: Some(vec![0.1, 0.2, 0.3, ...]), // Your vector embedding
    ..Default::default()
};
```

### Hybrid Search

Hybrid search combines both keyword and semantic search to provide more comprehensive results. It balances between exact keyword matches and semantic similarity.

```rust
let search_query = SearchQuery {
    q: Some("hybrid search query".to_string()),
    hybrid: Some(HybridQuery {
        semantic_ratio: SemanticRatio(0.5),
        embedder: "default".to_string(),
    }),
    ..Default::default()
};
```

## Advanced Query Techniques

### Filtering

You can apply filters to your search query to narrow down the results based on specific criteria:

```rust
let filter = json!({
    "price": { "$gt": 100, "$lt": 200 },
    "category": "electronics"
});

let search_query = SearchQuery {
    q: Some("smartphone".to_string()),
    filter: Some(filter),
    ..Default::default()
};
```

### Sorting

To sort the search results, you can specify one or more sort criteria:

```rust
let search_query = SearchQuery {
    q: Some("book".to_string()),
    sort: Some(vec!["price:asc".to_string(), "rating:desc".to_string()]),
    ..Default::default()
};
```

### Pagination

Meilisearch supports pagination using the `offset` and `limit` parameters:

```rust
let search_query = SearchQuery {
    q: Some("paginated results".to_string()),
    offset: 20,
    limit: 10,
    ..Default::default()
};
```

## Interpreting Search Results

The `SearchResult` object contains valuable information about the search results:

- `hits`: A list of matching documents
- `query`: The original search query
- `processing_time_ms`: Time taken to process the search
- `facet_distribution`: Distribution of facet values (if requested)
- `semantic_hit_count`: Number of semantic hits (for semantic or hybrid search)

You can access these properties to analyze and present the search results to your users.

## Examples

Here are some examples of complex searches using Meilisearch:

1. Keyword search with filtering and sorting:

```rust
let search_query = SearchQuery {
    q: Some("bestselling books".to_string()),
    filter: Some(json!({ "category": "fiction", "publication_year": { "$gte": 2020 } })),
    sort: Some(vec!["sales:desc".to_string()]),
    limit: 10,
    ..Default::default()
};
```

2. Semantic search with custom embedder:

```rust
let search_query = SearchQuery {
    vector: Some(vec![0.1, 0.2, 0.3, ...]), // Your vector embedding
    embedder: "custom_embedder".to_string(),
    limit: 20,
    ..Default::default()
};
```

3. Hybrid search with faceting:

```rust
let search_query = SearchQuery {
    q: Some("eco-friendly products".to_string()),
    hybrid: Some(HybridQuery {
        semantic_ratio: SemanticRatio(0.7),
        embedder: "default".to_string(),
    }),
    facets: Some(vec!["category".to_string(), "brand".to_string()]),
    ..Default::default()
};
```

By leveraging these search options and techniques, you can create powerful and flexible search experiences for your users with Meilisearch.