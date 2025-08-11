# CLAUDE.md - AI Assistant Instructions

## Project Overview
This is a complete RAG (Retrieval-Augmented Generation) system for wine expertise, combining n8n workflow automation with vector search capabilities using Qdrant, PostgreSQL, and Ollama.

## Architecture

### Core Components
1. **n8n** - Workflow automation platform
2. **Qdrant** - Vector database for semantic search
3. **PostgreSQL** - Relational database for metadata
4. **Ollama** - Local LLM and embedding models
5. **WooCommerce** - E-commerce integration for wine catalog

### System Design
```
WooCommerce → n8n Ingestion → Embeddings (Ollama) → Qdrant Vectors
                    ↓
              PostgreSQL Metadata
                    ↓
            RAG Chat Interface → User
```

## Key Files

### Docker Configuration
- `docker-compose.yml` - Container orchestration
- `.env` - Environment variables

### Workflows
- `rag-wine-ingestion-workflow.json` - Data pipeline for vectorization
- `rag-sommelier-chat-workflow.json` - Chat interface with RAG
- `sommelier-workflow.json` - Original AI agent workflow

### Documentation
- `README.md` - User documentation
- `setup-rag-credentials.md` - Credential configuration guide

## Working with this Project

### For Code Modifications
1. **Docker Compose Changes**: Always validate YAML syntax and service dependencies
2. **Workflow Updates**: Test in n8n UI before exporting JSON
3. **Database Schema**: Maintain backward compatibility for PostgreSQL tables
4. **Vector Dimensions**: Ensure embedding model output matches Qdrant collection size (384 dimensions)

### For AI Assistants
When helping with this project:

1. **Preserve Data Integrity**
   - Never drop database tables without backup
   - Maintain vector collection schemas
   - Keep credential configurations secure

2. **Service Dependencies**
   - n8n depends on PostgreSQL
   - RAG workflows depend on Qdrant and Ollama
   - Ensure all services are healthy before testing

3. **Testing Approach**
   - Test individual services first
   - Verify API connectivity
   - Check vector search accuracy
   - Validate chat responses

4. **Common Issues**
   - Port conflicts: Check for existing services
   - Memory usage: Ollama models require significant RAM
   - Network connectivity: Ensure containers can communicate
   - Model availability: Download required Ollama models

## API Endpoints

### n8n
- Web UI: `http://localhost:5679`
- API: `http://localhost:5679/api/v1`
- Webhooks: `http://localhost:5679/webhook/{id}`

### Qdrant
- API: `http://localhost:6335`
- Dashboard: `http://localhost:6336`

### Ollama
- API: `http://localhost:11435`

### PostgreSQL
- Internal: `postgres:5432`
- Database: `n8n`
- User: `n8n`

## Development Guidelines

### Adding New Features
1. Create feature branch
2. Update relevant workflows
3. Test with sample data
4. Update documentation
5. Validate Docker setup

### Debugging
- Check container logs: `docker-compose logs [service]`
- Verify network: `docker network inspect n8n_n8n-network`
- Test embeddings: `curl http://localhost:11435/api/embeddings`
- Query vectors: `curl http://localhost:6335/collections/wine_knowledge`

### Performance Optimization
- Limit vector search results (topK)
- Cache frequent queries
- Optimize embedding batch size
- Use connection pooling for PostgreSQL

## Security Considerations
- API keys are stored in environment variables
- Use Docker secrets for production
- Implement rate limiting for public endpoints
- Sanitize user inputs in chat interface
- Regular security updates for all containers

## Maintenance Tasks
- Daily: Check service health
- Weekly: Backup PostgreSQL and Qdrant data
- Monthly: Update Docker images
- Quarterly: Review and optimize workflows

## Contact
For issues or improvements, consider:
- Workflow optimization
- Vector search tuning
- LLM prompt engineering
- Database indexing strategies