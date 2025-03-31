# Typo Tolerance

Meilisearch's typo tolerance feature enhances search accuracy by allowing for minor misspellings in user queries. This document explains how typo tolerance works, how to configure it, and provides examples of its behavior in different scenarios.

## How Typo Tolerance Works

Meilisearch automatically handles typos in search queries by using a word-based approach. It calculates the number of allowed typos based on the word length:

- Words with 1-4 characters: No typos allowed
- Words with 5-8 characters: 1 typo allowed
- Words with 9+ characters: 2 typos allowed

A typo is defined as a character insertion, deletion, or substitution.

## Configuring Typo Tolerance

You can configure typo tolerance settings using the following methods:

### 1. Authorize Typos

You can enable or disable typo tolerance globally for an index:

```rust
// Enable typo tolerance (default)
index.put_authorize_typos(wtxn, true)?;

// Disable typo tolerance
index.put_authorize_typos(wtxn, false)?;
```

### 2. Minimum Word Length for Typos

You can set the minimum word length required for one-typo and two-typo tolerance:

```rust
// Set minimum word length for one typo (default is 5)
index.put_min_word_len_one_typo(wtxn, 4)?;

// Set minimum word length for two typos (default is 9)
index.put_min_word_len_two_typos(wtxn, 8)?;
```

### 3. Exact Words

You can specify words for which typo tolerance should be disabled:

```rust
let exact_words = fst::Set::from_iter(["exact", "words"])?;
index.put_exact_words(wtxn, &exact_words)?;
```

### 4. Exact Attributes

You can disable typo tolerance for specific attributes:

```rust
index.put_exact_attributes(wtxn, &["title", "category"])?;
```

## Examples

Here are some examples of how typo tolerance behaves in different scenarios:

1. Default behavior:
   - Query: "restaurent"
   - Matches: "restaurant" (1 typo)

2. Disabled typo tolerance:
   - Configuration: `index.put_authorize_typos(wtxn, false)?;`
   - Query: "restaurent"
   - No matches

3. Custom minimum word length:
   - Configuration: `index.put_min_word_len_one_typo(wtxn, 7)?;`
   - Query: "resturant"
   - No matches (word length is 8, below the new minimum for one typo)

4. Exact words:
   - Configuration: `index.put_exact_words(wtxn, &fst::Set::from_iter(["restaurant"])?)?;`
   - Query: "restaurent"
   - No matches for "restaurant" (exact word match required)

5. Exact attributes:
   - Configuration: `index.put_exact_attributes(wtxn, &["category"])?;`
   - Document: `{ "category": "restaurent", "description": "Great food" }`
   - Query: "restaurent"
   - Matches the document's description field, but not the category field

## Best Practices

1. Use exact words for brand names, technical terms, or other words that should never be misspelled.
2. Apply exact attributes to fields like SKUs, IDs, or other precise identifiers.
3. Adjust minimum word lengths based on your data and use cases to balance between typo tolerance and search precision.
4. Monitor search logs to identify common misspellings and adjust your typo tolerance settings accordingly.

By leveraging Meilisearch's typo tolerance features, you can create a more forgiving and user-friendly search experience while maintaining control over search precision when needed.