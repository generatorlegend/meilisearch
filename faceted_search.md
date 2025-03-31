---
title: Faceted Search in Meilisearch
description: Learn how to use faceted search in Meilisearch, including setup, querying, and advanced features.
---

# Faceted Search in Meilisearch

Faceted search is a powerful feature in Meilisearch that allows users to refine their search results by applying filters based on specific attributes of the documents. This guide will explain what facets are, how to set them up, and how to use them in search queries.

## Table of Contents

1. [Understanding Facets](#understanding-facets)
2. [Setting Up Facets](#setting-up-facets)
3. [Using Facets in Search Queries](#using-facets-in-search-queries)
4. [Facet Distribution](#facet-distribution)
5. [Facet Sorting](#facet-sorting)
6. [Advanced Facet Features](#advanced-facet-features)

## Understanding Facets

Facets are attributes of documents that can be used to categorize and filter search results. They provide a way to narrow down search results based on specific criteria, improving the user's ability to find relevant information quickly.

For example, in an e-commerce application, facets might include:

- Product category
- Brand
- Price range
- Color
- Size

## Setting Up Facets

To use faceted search in Meilisearch, you need to specify which fields should be treated as facets. This is done during index creation or by updating the index settings.

```json
{
  "filterableAttributes": [
    "category",
    "brand",
    "price",
    "color",
    "size"
  ]
}
```

By adding these attributes to the `filterableAttributes` array, you're telling Meilisearch to create facets for these fields.

## Using Facets in Search Queries

Once facets are set up, you can use them in search queries to filter results. Meilisearch supports various operators for facet filtering:

- Equality: `category = 'Electronics'`
- Range: `price > 100 AND price < 500`
- Existence: `color EXISTS`
- Geolocation: `_geoRadius(45.472735, 9.184019, 100000)`

Here's an example of how to use facet filters in a search query:

```json
{
  "q": "laptop",
  "filter": "category = 'Electronics' AND price < 1000"
}
```

This query will search for "laptop" within the "Electronics" category and with a price less than 1000.

## Facet Distribution

Meilisearch can provide a distribution of facet values in the search results. This is useful for creating dynamic facet menus or understanding the composition of your search results.

To get facet distribution, include the `facetsDistribution` parameter in your search query:

```json
{
  "q": "laptop",
  "facetsDistribution": ["category", "brand"]
}
```

The response will include a distribution of values for the specified facets:

```json
{
  "facetsDistribution": {
    "category": {
      "Electronics": 42,
      "Computers": 18
    },
    "brand": {
      "Dell": 15,
      "HP": 12,
      "Lenovo": 10
    }
  }
}
```

## Facet Sorting

Meilisearch supports sorting facet values in ascending or descending order. This is particularly useful for numerical facets like price ranges.

The `facet_min_value` and `facet_max_value` functions in the codebase allow for finding the minimum and maximum values of a facet within a set of documents:

```rust
pub fn facet_min_value<'t>(
    index: &'t Index,
    rtxn: &'t heed::RoTxn<'t>,
    field_id: u16,
    candidates: RoaringBitmap,
) -> Result<Option<f64>>

pub fn facet_max_value<'t>(
    index: &'t Index,
    rtxn: &'t heed::RoTxn<'t>,
    field_id: u16,
    candidates: RoaringBitmap,
) -> Result<Option<f64>>
```

These functions can be used internally to implement efficient facet sorting and range queries.

## Advanced Facet Features

### Multi-Value Facets

Meilisearch supports multi-value facets, allowing a document to have multiple values for a single facet. For example, a book might belong to multiple genres.

### Nested Facets

While Meilisearch doesn't natively support hierarchical or nested facets, you can implement this behavior by using a naming convention for your facet attributes, such as `category.level1`, `category.level2`, etc.

### Facet Search

Meilisearch provides a feature called "facet search" that allows searching within facet values. This is particularly useful for facets with a large number of possible values.

```json
{
  "q": "laptop",
  "facetQuery": {
    "brand": "de"
  }
}
```

This query will return facet values for the "brand" attribute that match the prefix "de" (e.g., "Dell").

## Conclusion

Faceted search is a powerful feature in Meilisearch that enhances the search experience by allowing users to refine and filter their results effectively. By properly setting up and utilizing facets, you can create more dynamic and user-friendly search interfaces in your applications.

For more detailed information on specific facet operations and advanced usage, refer to the Meilisearch API documentation.