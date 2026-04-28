# pgvector for Semantic Search

## Installation

```sql
-- Enable pgvector extension
CREATE EXTENSION IF NOT EXISTS vector;
```

## Vector Column Setup

```sql
-- Create table with vector column (1536 dims for OpenAI)
ALTER TABLE posts ADD COLUMN embedding vector(1536);

-- Or create table with vector
CREATE TABLE documents (
    id BIGSERIAL PRIMARY KEY,
    content TEXT NOT NULL,
    embedding vector(1536),
    created_at TIMESTAMP DEFAULT NOW()
);

-- Create index for similarity search
CREATE INDEX ON documents USING ivfflat (embedding vector_cosine_ops)
WITH (lists = 100);
```

## Vector Operations

### Insert Embeddings

```sql
-- Manual embedding (for testing)
INSERT INTO documents (content, embedding)
VALUES ('Sample text', '[0.1, 0.2, ...]');

-- Generate embedding from text (requires openai extension)
SELECT embedding_from_text('search query');
```

### Similarity Search

```sql
-- Cosine distance (lower = more similar)
SELECT id, content, embedding <=> embedding_from_text('search query') AS distance
FROM documents
ORDER BY embedding <=> embedding_from_text('search query')
LIMIT 10;

-- L2 distance (Euclidean)
SELECT id, content, embedding <-> embedding_from_text('search query') AS distance
FROM documents
ORDER BY embedding <-> embedding_from_text('search query')
LIMIT 10;

-- Inner product (higher = more similar)
SELECT id, content, embedding <#> embedding_from_text('search query') AS similarity
FROM documents
ORDER BY embedding <#> embedding_from_text('search query')
LIMIT 10;
```

## Chunking for Long Text

```sql
-- Create chunks table
CREATE TABLE document_chunks (
    id BIGSERIAL PRIMARY KEY,
    document_id BIGINT REFERENCES documents(id),
    chunk_text TEXT NOT NULL,
    chunk_index INTEGER NOT NULL,
    embedding vector(1536),
    created_at TIMESTAMP DEFAULT NOW()
);

-- Search with reranking
SELECT d.id, d.content, dc.chunk_text,
    (dc.embedding <=> query_embedding) AS distance
FROM document_chunks dc
JOIN documents d ON d.id = dc.document_id,
    embedding_from_text('search query') AS query_embedding
ORDER BY distance
LIMIT 5;
```

## Best Practices

| Aspect | Recommendation |
|--------|-------------|
| Dimensions | 1536 (OpenAI) or 768 (BERT) |
| Index | IVFFlat with `lists = rows / 1000` |
| Distance | Cosine for semantic search |
| Chunk size | 500-1000 chars with overlap |
| Filtering | Apply WHERE before similarity |

## Laravel Integration

```php
// Generate embedding
$embedding = OpenAI::embeddings()->create([
    'model' => 'text-embedding-3-small',
    'input' => $text,
])->embeddings[0]->embedding;

// Store
DB::table('documents')->insert([
    'content' => $text,
    'embedding' => json_encode($embedding),
]);

// Search
$queryEmbedding = OpenAI::embeddings()->create([
    'model' => 'text-embedding-3-small',
    'input' => $searchQuery,
])->embeddings[0]->embedding;

$results = DB::table('documents')
    ->orderByRaw('embedding <=> ?', [json_encode($queryEmbedding)])
    ->limit(10)
    ->get();
```