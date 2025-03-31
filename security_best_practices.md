# Security Best Practices for Meilisearch

## Introduction

This guide outlines security best practices for Meilisearch deployments, focusing on securing your instance, managing API keys, setting up authentication and authorization, and protecting sensitive data. Following these recommendations will help ensure a secure production environment.

## Securing Your Meilisearch Instance

### Use HTTPS

Always use HTTPS in production to encrypt data in transit. Meilisearch supports SSL/TLS configuration:

```bash
meilisearch --ssl-cert-path /path/to/cert.pem --ssl-key-path /path/to/key.pem
```

### Set a Strong Master Key

Always set a strong master key in production:

```bash
meilisearch --master-key "a-long-and-secure-master-key"
```

Generate a secure master key:

```rust
use meilisearch::generate_master_key;

let master_key = generate_master_key();
```

### Limit Network Access

Run Meilisearch on a private network or use a firewall to restrict access to trusted IP addresses.

## API Key Management

### Use API Keys Instead of Master Key

Create and use API keys with limited permissions for day-to-day operations:

```rust
let create_key = CreateApiKey {
    description: Some("Search-only API key".to_string()),
    actions: vec![Action::Search],
    indexes: vec!["products".to_string()],
    expires_at: None,
};

let api_key = auth_controller.create_key(create_key)?;
```

### Regularly Rotate API Keys

Periodically generate new API keys and revoke old ones:

```rust
auth_controller.delete_key(old_key_uid)?;
```

### Set Expiration Dates

Use expiration dates for API keys to limit their lifespan:

```rust
let create_key = CreateApiKey {
    // ...
    expires_at: Some(OffsetDateTime::now_utc() + Duration::days(30)),
};
```

## Authentication and Authorization

### Enable SSL Client Authentication

For additional security, enable SSL client authentication:

```bash
meilisearch --ssl-auth-path /path/to/ca.pem --ssl-require-auth
```

### Use Tenant Tokens for Fine-grained Permissions

Implement tenant tokens for more granular control over search permissions:

```rust
let search_rules = SearchRules::Map(HashMap::from([
    (IndexUidPattern::new("products")?, Some(IndexSearchRules {
        filter: Some(json!(["category = 'electronics'"]))
    }))
]));

// Generate tenant token using the search rules
```

## Protecting Sensitive Data

### Sanitize Input Data

Always sanitize and validate input data before indexing or searching to prevent injection attacks.

### Encrypt Sensitive Information

Encrypt sensitive information before indexing, and implement proper key management for decryption.

### Use Secure Environment Variables

Store sensitive configuration values (e.g., master key) in secure environment variables:

```bash
export MEILI_MASTER_KEY="your-secure-master-key"
meilisearch
```

## Production Deployment Recommendations

### Regular Backups

Implement regular snapshot creation and store backups securely:

```bash
meilisearch --snapshot-dir /path/to/snapshots --schedule-snapshot 86400
```

### Monitoring and Logging

Enable metrics for monitoring:

```bash
meilisearch --experimental-enable-metrics
```

Set appropriate log levels:

```bash
meilisearch --log-level INFO
```

### Update Regularly

Keep Meilisearch updated to the latest stable version to benefit from security patches and improvements.

### Resource Limits

Set appropriate resource limits to prevent DoS attacks:

```bash
meilisearch --http-payload-size-limit "10 MB" --max-indexing-memory "2 GB"
```

## Conclusion

By following these security best practices, you can significantly enhance the security of your Meilisearch deployment. Always stay informed about the latest security recommendations and update your configuration accordingly.