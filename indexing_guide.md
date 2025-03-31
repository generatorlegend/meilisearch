---
title: Indexing Guide
description: Learn how to effectively index documents in Meilisearch
---

# Indexing Guide

This guide will help you understand how to effectively index documents in Meilisearch. We'll cover the concept of primary keys, methods for adding, updating, and deleting documents, and best practices for structuring documents to achieve optimal search performance.

## Table of Contents

1. [Understanding Primary Keys](#understanding-primary-keys)
2. [Adding Documents](#adding-documents)
3. [Updating Documents](#updating-documents)
4. [Deleting Documents](#deleting-documents)
5. [Batch Operations](#batch-operations)
6. [Asynchronous Indexing](#asynchronous-indexing)
7. [Best Practices for Document Structure](#best-practices-for-document-structure)

## Understanding Primary Keys

In Meilisearch, each document must have a unique identifier called a primary key. This key is used to identify and manage documents within an index.

- The primary key is typically a field in your document, such as `id`, `uuid`, or any other unique identifier.
- You can specify the primary key when creating an index or adding documents for the first time.
- Once set, the primary key cannot be changed for an index.

Example of specifying a primary key:

```json
{
  "id": 1,
  "title": "The Catcher in the Rye",
  "author": "J.D. Salinger"
}
```

In this case, `id` would be the primary key.

## Adding Documents

To add documents to Meilisearch, you can use the `POST` or `PUT` methods:

### Adding New Documents

```http
POST /indexes/{indexUid}/documents
```

This method will add new documents or replace existing ones if they have the same primary key.

### Replacing Existing Documents

```http
PUT /indexes/{indexUid}/documents
```

This method will replace existing documents entirely. If a document with the given primary key doesn't exist, it will be created.

Both methods accept JSON arrays of documents:

```json
[
  {
    "id": 1,
    "title": "Pride and Prejudice",
    "author": "Jane Austen"
  },
  {
    "id": 2,
    "title": "The Great Gatsby",
    "author": "F. Scott Fitzgerald"
  }
]
```

## Updating Documents

To update existing documents partially:

```http
POST /indexes/{indexUid}/documents
```

This method will only update the fields provided in the request, leaving other fields untouched.

Example:

```json
[
  {
    "id": 1,
    "publication_year": 1813
  }
]
```

This will add or update the `publication_year` field for the document with `id: 1`, without affecting other fields.

## Deleting Documents

### Deleting a Single Document

To delete a single document:

```http
DELETE /indexes/{indexUid}/documents/{document_id}
```

### Deleting Multiple Documents

To delete multiple documents by their IDs:

```http
POST /indexes/{indexUid}/documents/delete-batch
```

With a request body containing an array of document IDs:

```json
[1, 2, 3, 4]
```

### Deleting All Documents

To clear all documents from an index:

```http
DELETE /indexes/{indexUid}/documents
```

## Batch Operations

For better performance when dealing with large numbers of documents, use batch operations:

```http
POST /indexes/{indexUid}/documents
```

You can send up to 1000 documents in a single request:

```json
[
  {"id": 1, "title": "Book 1"},
  {"id": 2, "title": "Book 2"},
  ...
  {"id": 1000, "title": "Book 1000"}
]
```

## Asynchronous Indexing

Meilisearch processes indexing operations asynchronously. When you send a request to add, update, or delete documents, Meilisearch returns a `taskUid` that you can use to check the status of the operation:

```json
{
  "taskUid": 1,
  "indexUid": "movies",
  "status": "enqueued",
  "type": "documentAdditionOrUpdate",
  "enqueuedAt": "2021-08-12T09:35:00.666226Z"
}
```

You can check the status of a task using:

```http
GET /tasks/{taskUid}
```

## Best Practices for Document Structure

1. **Keep documents simple**: Use flat structures when possible. Nested objects are supported but may impact performance.

2. **Use appropriate field names**: Choose clear, descriptive names for your fields.

3. **Consistent data types**: Keep data types consistent across documents for the same fields.

4. **Limit document size**: Try to keep individual documents under 1MB.

5. **Index only necessary fields**: Only include fields that are needed for searching or displaying results.

6. **Use appropriate data types**: Use numbers for numeric values, booleans for true/false values, etc.

7. **Consider searchable vs. displayable fields**: Not all fields need to be searchable. Use `searchableAttributes` settings to specify which fields should be searched.

By following these guidelines and understanding the indexing process, you can optimize your Meilisearch implementation for the best possible search experience.