# Performance Tuning

This guide provides information on how to optimize search performance, manage resource usage, and scale Meilisearch for large datasets. We'll cover indexing strategies, caching, and hardware recommendations to help you get the best performance out of your Meilisearch instance.

## Table of Contents

1. [Indexing Configuration](#indexing-configuration)
2. [Memory Management](#memory-management)
3. [Search Performance](#search-performance)
4. [Caching](#caching)
5. [Hardware Recommendations](#hardware-recommendations)
6. [Scaling Strategies](#scaling-strategies)

## Indexing Configuration

Proper indexing configuration is crucial for optimal performance. Meilisearch provides several options to tune the indexing process:

### Max Indexing Memory

You can limit the amount of RAM Meilisearch uses during indexing:

```bash
MEILI_MAX_INDEXING_MEMORY=1073741824 ./meilisearch
```

This sets the maximum indexing memory to 1GB. By default, Meilisearch uses no more than two-thirds of available memory.

### Max Indexing Threads

Control the number of threads used for indexing:

```bash
MEILI_MAX_INDEXING_THREADS=4 ./meilisearch
```

By default, Meilisearch uses half of the available CPU cores for indexing to ensure responsiveness for search queries.

### Indexer Configuration

For more fine-grained control, you can adjust the `IndexerConfig` settings:

```rust
let indexer_config = IndexerConfig {
    chunk_compression_type: CompressionType::None,
    chunk_compression_level: None,
    max_memory: Some(1_000_000_000), // 1GB
    max_nb_chunks: Some(10),
    documents_chunk_size: Some(1000),
    // ... other settings
};
```

## Memory Management

Effective memory management is key to Meilisearch's performance:

### Virtual Memory Address Space

Meilisearch allocates a large virtual address space for indexes:

```rust
pub const INDEX_SIZE: u64 = 2 * 1024 * 1024 * 1024 * 1024; // 2 TiB
pub const TASK_DB_SIZE: u64 = 20 * 1024 * 1024 * 1024; // 20 GiB
```

Ensure your system has sufficient virtual memory to accommodate these allocations.

### HTTP Payload Size Limit

Set a limit on the size of HTTP payloads:

```bash
MEILI_HTTP_PAYLOAD_SIZE_LIMIT="100 MB" ./meilisearch
```

## Search Performance

Optimize search performance with these settings:

### Search Queue Size

Control the size of the search queue:

```bash
MEILI_EXPERIMENTAL_SEARCH_QUEUE_SIZE=2000 ./meilisearch
```

Default is 1000. Increase this value if you're experiencing high search traffic.

### Concurrent Searches per Core

Adjust the number of concurrent searches per CPU core:

```bash
MEILI_EXPERIMENTAL_NB_SEARCHES_PER_CORE=8 ./meilisearch
```

Default is 4. Increase this for higher search throughput, but be cautious of resource contention.

### Search Timeout

Set a timeout for search requests:

```bash
MEILI_EXPERIMENTAL_DROP_SEARCH_AFTER=120 ./meilisearch
```

This drops search requests after 120 seconds (default is 60).

## Caching

Meilisearch supports caching for improved performance:

### Embedding Cache

Enable caching for search query embeddings:

```bash
MEILI_EXPERIMENTAL_EMBEDDING_CACHE_ENTRIES=10000 ./meilisearch
```

This sets the maximum number of entries in the cache for each distinct embedder.

## Hardware Recommendations

For optimal performance, consider the following hardware recommendations:

1. **CPU**: Multi-core processors (8+ cores) for faster indexing and search operations.
2. **RAM**: 16GB+ for small to medium datasets, 32GB+ for large datasets.
3. **Storage**: Fast SSDs (NVMe if possible) for quicker data access and indexing.
4. **Network**: High-bandwidth, low-latency network connections for distributed setups.

## Scaling Strategies

As your dataset grows, consider these scaling strategies:

1. **Vertical Scaling**: Upgrade to more powerful hardware (more CPU cores, RAM, faster storage).
2. **Horizontal Scaling**: Distribute your data across multiple Meilisearch instances.
3. **Sharding**: Split your dataset into smaller, more manageable chunks across multiple instances.
4. **Read Replicas**: Create read-only copies of your Meilisearch instance to handle high search traffic.

Remember to monitor your Meilisearch instance's performance regularly and adjust these settings as needed based on your specific use case and workload.