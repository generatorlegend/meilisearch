---
title: Getting Started with Meilisearch
description: A comprehensive guide to help you get started with Meilisearch, including installation, basic configuration, and common operations.
---

# Getting Started with Meilisearch

Welcome to Meilisearch! This guide will help you get up and running with Meilisearch, a powerful, fast, and easy-to-use search engine. We'll cover installation, basic configuration, and common operations like adding documents, searching, and managing indexes.

## Table of Contents

1. [Installation](#installation)
2. [Basic Configuration](#basic-configuration)
3. [Adding Documents](#adding-documents)
4. [Searching](#searching)
5. [Managing Indexes](#managing-indexes)

## Installation

### Option 1: Using Docker

The easiest way to get started with Meilisearch is by using Docker:

```bash
docker run -it --rm \
    -p 7700:7700 \
    -v $(pwd)/meili_data:/meili_data \
    getmeili/meilisearch:v1.0
```

This command will start Meilisearch on port 7700 and store data in the `./meili_data` directory.

### Option 2: Binary Installation

You can download the latest binary for your operating system from the [Meilisearch releases page](https://github.com/meilisearch/meilisearch/releases).

After downloading, make the binary executable and run it:

```bash
chmod +x meilisearch
./meilisearch
```

## Basic Configuration

Meilisearch can be configured using environment variables or command-line arguments. Here are some important configuration options:

- `MEILI_HTTP_ADDR`: Sets the HTTP address and port (default: `127.0.0.1:7700`)
- `MEILI_MASTER_KEY`: Sets the instance's master key for API authentication
- `MEILI_ENV`: Configures the instance's environment (`development` or `production`)

Example:

```bash
MEILI_HTTP_ADDR=0.0.0.0:7700 MEILI_MASTER_KEY=YOUR_MASTER_KEY ./meilisearch
```

## Adding Documents

To add documents to Meilisearch, you need to create an index and then add documents to it. Here are examples in different programming languages:

### Python

```python
import meilisearch

client = meilisearch.Client('http://127.0.0.1:7700', 'YOUR_MASTER_KEY')

# Create an index
index = client.create_index('movies')

# Add documents
documents = [
    {'id': 1, 'title': 'Carol', 'genres': ['Romance', 'Drama']},
    {'id': 2, 'title': 'Wonder Woman', 'genres': ['Action', 'Adventure']},
    {'id': 3, 'title': 'Life of Pi', 'genres': ['Adventure', 'Drama']}
]
index.add_documents(documents)
```

### JavaScript

```javascript
import { MeiliSearch } from 'meilisearch'

const client = new MeiliSearch({
  host: 'http://127.0.0.1:7700',
  apiKey: 'YOUR_MASTER_KEY'
})

// Create an index
const index = await client.createIndex('movies')

// Add documents
const documents = [
  { id: 1, title: 'Carol', genres: ['Romance', 'Drama'] },
  { id: 2, title: 'Wonder Woman', genres: ['Action', 'Adventure'] },
  { id: 3, title: 'Life of Pi', genres: ['Adventure', 'Drama'] }
]
await index.addDocuments(documents)
```

## Searching

Once you have added documents to your index, you can start searching. Here are examples of how to perform a basic search:

### Python

```python
# Search in the index
results = index.search('wonder')
print(results)
```

### JavaScript

```javascript
// Search in the index
const results = await index.search('wonder')
console.log(results)
```

## Managing Indexes

Meilisearch allows you to manage multiple indexes. Here are some common operations:

### List all indexes

#### Python

```python
indexes = client.get_indexes()
for index in indexes:
    print(index.uid)
```

#### JavaScript

```javascript
const indexes = await client.getIndexes()
indexes.forEach(index => console.log(index.uid))
```

### Delete an index

#### Python

```python
client.delete_index('movies')
```

#### JavaScript

```javascript
await client.deleteIndex('movies')
```

### Update index settings

You can customize various settings for each index, such as searchable attributes, filterable attributes, and ranking rules.

#### Python

```python
index.update_settings({
    'searchableAttributes': ['title', 'description'],
    'filterableAttributes': ['genre'],
    'sortableAttributes': ['release_date']
})
```

#### JavaScript

```javascript
await index.updateSettings({
  searchableAttributes: ['title', 'description'],
  filterableAttributes: ['genre'],
  sortableAttributes: ['release_date']
})
```

This concludes the Getting Started guide for Meilisearch. For more detailed information on advanced features and configurations, please refer to our [full documentation](https://docs.meilisearch.com/).