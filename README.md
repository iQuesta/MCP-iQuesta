# iQuesta MCP Server

Connecteur [Model Context Protocol](https://modelcontextprotocol.io) pour [iQuesta.com](https://iquesta.com), plateforme de recherche d'offres de stage et d'alternance en France.

Permet à n'importe quel agent IA compatible MCP (Claude, ChatGPT, Gemini...) de rechercher des offres d'emploi directement dans la conversation.

## Endpoint
https://mcp.iquesta.com/api/mcp

**Protocole** : MCP 2024-11-05 · **Transport** : Streamable HTTP · **Stack** : Laravel 10 / PHP 8.1

## Tools disponibles

| Tool | Description |
|---|---|
| `search_jobs` | Recherche des offres par mots-clés, région, discipline, type de contrat |
| `get_job` | Récupère le détail complet d'une offre par son ID |
| `get_similar_jobs` | Trouve des offres similaires à une offre donnée |
| `list_filters` | Liste les valeurs possibles pour les filtres (régions, disciplines, types de contrat) |

## Exemples d'utilisation

### Initialisation

```bash
curl -X POST https://mcp.iquesta.com/api/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{}}'
```

### Liste des tools

```bash
curl -X POST https://mcp.iquesta.com/api/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}'
```

### Recherche d'offres

```bash
curl -X POST https://mcp.iquesta.com/api/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 3,
    "method": "tools/call",
    "params": {
      "name": "search_jobs",
      "arguments": {
        "term": "développeur",
        "regions": 10,
        "contracts": "1"
      }
    }
  }'
```

### Lister les filtres disponibles

```bash
curl -X POST https://mcp.iquesta.com/api/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 4,
    "method": "tools/call",
    "params": { "name": "list_filters", "arguments": { "filter": "all" } }
  }'
```

## Configuration côté client

### Claude.ai / Claude Desktop

Ajouter dans les paramètres de connecteurs :

```json
{
  "mcpServers": {
    "iquesta": {
      "url": "https://mcp.iquesta.com/api/mcp"
    }
  }
}
```

## À propos d'iQuesta

[iQuesta.com](https://iquesta.com) référence des milliers d'offres de stage et d'alternance en France, à destination des étudiants et jeunes diplômés.

## Licence

MIT
