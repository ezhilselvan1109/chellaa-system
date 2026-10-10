# Search Index Design (OpenSearch)

Mapping definitions, analyzers, autocomplete edge-ngram tokenizers, and index rebuild strategies.

---

## 1. Product Search Index Mapping (`products_v1`)

```json
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 1,
    "analysis": {
      "analyzer": {
        "autocomplete_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "autocomplete_filter"]
        }
      },
      "filter": {
        "autocomplete_filter": {
          "type": "edge_ngram",
          "min_gram": 2,
          "max_gram": 15
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "productId": { "type": "keyword" },
      "skuList": { "type": "keyword" },
      "title": {
        "type": "text",
        "analyzer": "standard",
        "fields": {
          "suggest": { "type": "text", "analyzer": "autocomplete_analyzer" }
        }
      },
      "description": { "type": "text" },
      "brandId": { "type": "keyword" },
      "brandName": { "type": "keyword" },
      "categoryId": { "type": "keyword" },
      "categoryPath": { "type": "keyword" },
      "minPrice": { "type": "double" },
      "maxPrice": { "type": "double" },
      "averageRating": { "type": "half_float" },
      "reviewCount": { "type": "integer" },
      "tags": { "type": "keyword" },
      "inStock": { "type": "boolean" },
      "attributes": {
        "type": "nested",
        "properties": {
          "name": { "type": "keyword" },
          "value": { "type": "keyword" }
        }
      },
      "publishedAt": { "type": "date" }
    }
  }
}
```

---

## 2. Re-Indexing & Blue-Green Aliases

To rebuild the index without downtime:
1. Create new index `products_v2` with updated analyzers.
2. Replay events or execute bulk extract from `catalog_db`.
3. Atomically switch OpenSearch alias: `POST /_aliases` (remove `products_v1`, add `products_v2` to alias `products_current`).
4. Delete old `products_v1` index after verification.
