# 🍷 RAG Wine Sommelier - AI Expert System

Un sistema completo di sommelier AI con capacità RAG (Retrieval-Augmented Generation) per consulenze enologiche personalizzate.

## 🚀 Caratteristiche Principali

### ✨ Funzionalità Core
- **🔍 Ricerca Semantica**: Trova vini usando ricerca vettoriale intelligente
- **🤖 AI Sommelier**: Consulente esperto con personalità professionale
- **🛒 Integrazione WooCommerce**: Accesso diretto al catalogo vini
- **💬 Chat Interface**: Interfaccia conversazionale pubblica
- **📊 Sistema RAG**: Database vettoriale per consigli contestuali
- **🍽️ Abbinamenti Gastronomici**: Engine di abbinamento cibo-vino

### 🛠️ Stack Tecnologico
- **n8n** - Automazione workflow
- **Qdrant** - Database vettoriale
- **PostgreSQL** - Database relazionale  
- **Ollama** - LLM e modelli embedding locali
- **Docker** - Containerizzazione

## 📋 Prerequisiti

- Docker & Docker Compose
- 8GB+ RAM (per Ollama)
- 10GB spazio disco

## ⚡ Quick Start

### 1. Clone e Avvia
```bash
git clone <your-repo>
cd n8n-wine-sommelier
docker-compose up -d
```

### 2. Accesso Servizi
| Servizio | URL | Credenziali |
|----------|-----|-------------|
| n8n | http://localhost:5679 | admin/admin |
| Qdrant | http://localhost:6335 | - |

### 3. Setup Rapido
```bash
# Verifica servizi
docker-compose ps

# Modelli AI scaricati automaticamente
```

## 🏗️ Configurazione Completa

### 1. Credenziali n8n
1. Vai su http://localhost:5679
2. Login: `admin/admin`
3. Settings → API → Crea nuova API key

### 2. Importa Workflows
Tutti i workflows sono nella cartella `workflows/`:

1. **Ingestion**: `workflows/rag-wine-ingestion-workflow.json`
   - Importa dati WooCommerce → Embeddings → Qdrant/PostgreSQL

2. **Chat RAG**: `workflows/rag-sommelier-chat-workflow.json`  
   - Interfaccia chat con ricerca semantica

3. **Sommelier Base**: `workflows/sommelier-workflow.json`
   - AI agent sommelier senza RAG

### 3. Configura Credenziali in n8n

#### PostgreSQL
```
Host: postgres
Port: 5432
Database: n8n
User: n8n
Password: n8n_password
```

#### Qdrant
```
URL: http://qdrant:6333
```

#### Ollama
```
Base URL: http://ollama:11434
```

### 4. Prima Esecuzione
1. Esegui workflow **Ingestion** per popolare database
2. Attiva workflow **Chat RAG**
3. Testa interfaccia chat

## 🎯 Utilizzo Chat Sommelier

```
👤 "Cerco un vino rosso corposo sotto i 30 euro"
🤖 🍷 **Primitivo di Manduria DOP 2021**
   📍 Puglia • 💰 €18.50 • 🍇 Primitivo
   📝 Note intense di frutti scuri, speziato
   🍽️ Perfetto con: bistecca, formaggi stagionati

👤 "Cosa abbino con il pesce?"
🤖 Per il pesce consiglio vini bianchi freschi...
```

### Comandi Supportati
- **Tipo**: "vino bianco", "rosso corposo", "spumante"
- **Budget**: "sotto i 20 euro", "fascia media", "pregiato"  
- **Occasioni**: "cena romantica", "aperitivo", "festa"
- **Abbinamenti**: "con il pesce", "per pizza", "formaggi"
- **Regioni**: "vini toscani", "del Piemonte", "siciliani"

## 🔧 Configurazione Avanzata

### Modelli Ollama
```bash
# Modelli installati automaticamente
docker exec n8n-ollama ollama list

# Modelli aggiuntivi
docker exec n8n-ollama ollama pull llama3.2:1b    # Più veloce
docker exec n8n-ollama ollama pull all-minilm     # Embeddings leggeri
```

### Database Vettoriale
```bash
# Stato collezione Qdrant
curl http://localhost:6335/collections/wine_knowledge

# Info database
curl http://localhost:6335/collections
```

## 🐛 Troubleshooting

### Problemi Comuni

**Servizi non si avviano**
```bash
docker-compose down
docker system prune -f
docker-compose up -d
```

**Ollama memoria insufficiente**
```bash
# Riduci parametri nel workflow n8n
"num_ctx": 2048     # da 4096
"num_predict": 512  # da 2000
```

**Collection Qdrant non trovata**
```bash
# Ricrea automaticamente eseguendo workflow Ingestion
# O manualmente:
curl -X PUT "http://localhost:6335/collections/wine_knowledge" \
  -d '{"vectors": {"size": 384, "distance": "Cosine"}}'
```

### Debug Logs
```bash
# Tutti i servizi
docker-compose logs -f

# Servizio specifico
docker-compose logs -f n8n
docker-compose logs -f ollama
docker-compose logs -f qdrant
```

## 📊 Monitoring

### Health Check
```bash
curl http://localhost:5679/healthz    # n8n
curl http://localhost:6335/health     # Qdrant  
curl http://localhost:11435/api/tags  # Ollama
```

### Risorse Sistema
- **RAM**: Ollama ~2-4GB, n8n ~500MB, altri ~200MB
- **Disk**: Modelli ~2GB, DB ~100MB iniziale
- **CPU**: Picchi durante inferenza LLM

## 🔗 Integrazioni

### n8n MCP (Claude Desktop)
```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": {
        "N8N_API_URL": "http://localhost:5679",
        "N8N_API_KEY": "your_api_key"
      }
    }
  }
}
```

### WooCommerce Setup
1. WooCommerce → Settings → Advanced → REST API
2. Crea chiave con permessi Read/Write
3. Configura credenziali in n8n

## 🚀 Deployment Produzione

### Environment Variables
```env
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=strong_password
POSTGRES_PASSWORD=secure_password
N8N_ENCRYPTION_KEY=your_32_char_encryption_key
```

### Backup
```bash
# PostgreSQL
docker exec n8n-postgres pg_dump -U n8n n8n > backup.sql

# Qdrant
curl -X POST http://localhost:6335/collections/wine_knowledge/snapshots
```

## 📁 Struttura Progetto

```
n8n-wine-sommelier/
├── docker-compose.yml          # Orchestrazione servizi
├── .env                       # Variabili ambiente
├── README.md                  # Questa documentazione
├── CLAUDE.md                  # Istruzioni per AI
├── setup-rag-credentials.md   # Guida credenziali
└── workflows/                 # Workflow n8n
    ├── rag-wine-ingestion-workflow.json
    ├── rag-sommelier-chat-workflow.json
    └── sommelier-workflow.json
```

## 🤝 Contribuire

1. Fork repository
2. Feature branch: `git checkout -b feature/new-feature`
3. Commit: `git commit -m 'Add feature'`
4. Push: `git push origin feature/new-feature`
5. Pull Request

---

<div align="center">
  <strong>🍷 Il tuo sommelier AI personale</strong><br>
  <sub>Powered by n8n + RAG + Ollama</sub>
</div>
