# Setup RAG System Credentials

## 1. PostgreSQL Configuration
```json
{
  "host": "postgres",
  "port": 5432,
  "database": "n8n",
  "user": "n8n",
  "password": "n8n_password"
}
```

## 2. Qdrant Configuration
```json
{
  "url": "http://qdrant:6333",
  "apiKey": ""  // No API key needed for local
}
```

## 3. Ollama Configuration
```json
{
  "baseUrl": "http://ollama:11434"
}
```

## 4. Required Ollama Models
```bash
# Install embedding model
docker exec n8n-ollama ollama pull nomic-embed-text

# Or alternative lighter model
docker exec n8n-ollama ollama pull all-minilm:l6-v2
```

## 5. Initialize Qdrant Collection
```bash
# Create wine collection
curl -X PUT "http://localhost:6335/collections/wine_knowledge" \
  -H "Content-Type: application/json" \
  -d '{
    "vectors": {
      "size": 384,
      "distance": "Cosine"
    },
    "payload_schema": {
      "wine_id": {"type": "keyword"},
      "name": {"type": "text"},
      "category": {"type": "keyword"},
      "price": {"type": "float"},
      "region": {"type": "keyword"}
    }
  }'
```

## Workflow Import Steps:

1. **Import Ingestion Workflow**:
   - Open n8n: http://localhost:5679
   - Import `rag-wine-ingestion-workflow.json`
   - Configure credentials for PostgreSQL, Qdrant, Ollama
   - Execute to populate the vector database

2. **Import Chat Workflow**:
   - Import `rag-sommelier-chat-workflow.json`
   - Configure same credentials
   - Activate the workflow
   - Test the chat interface

## Test the System:
- "Cerco un vino rosso corposo sotto i 30 euro"
- "Quali vini si abbinano con il pesce?"
- "Mostrami vini della Toscana"
- "Consigliami un vino per una cena romantica"