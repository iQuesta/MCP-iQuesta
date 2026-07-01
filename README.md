# iQuesta MCP Server

[![smithery badge](https://smithery.ai/badge/julien-yell/iquesta)](https://smithery.ai/servers/julien-yell/iquesta)

Connecteur [Model Context Protocol](https://modelcontextprotocol.io) pour [iQuesta.com](https://iquesta.com), plateforme de recherche d'offres de stage et d'alternance en France.

Permet à n'importe quel agent IA compatible MCP (Claude, ChatGPT, Gemini...) de rechercher des offres d'emploi directement dans la conversation.

## Endpoint
https://mcp.iquesta.com/api/mcp

**Protocole** : MCP 2024-11-05 · **Transport** : Streamable HTTP · **Stack** : Laravel 10 / PHP 8.1

## Tools disponibles

| Tool | Description |
|---|---|
| `search_jobs` | Recherche des offres par mots-clés (titre), descriptif de l'annonce, région, discipline, matière, type de contrat |
| `get_job` | Récupère le détail complet d'une offre par son ID |
| `list_filters` | Liste les valeurs possibles pour les filtres (régions, disciplines, matières, types de contrat) |

> **Note** : le tool `get_similar_jobs` est temporairement désactivé — l'endpoint API correspondant n'existe pas encore côté iQuesta. Il sera réactivé une fois développé.

### Paramètres de `search_jobs`

| Paramètre | Type | Description |
|---|---|---|
| `term` | string | Mots-clés recherchés dans le **titre** de l'annonce |
| `description` | string | Mots-clés recherchés dans le **texte complet** de l'annonce (missions, compétences requises...) |
| `disciplines` | integer | ID de discipline (secteur large), obtenu via `list_filters(filter='disciplines')` |
| `matieres` | integer[] | Liste d'IDs de matières (plus granulaire que `disciplines`), obtenue via `list_filters(filter='matieres')`. Ex : `27` = "Informatique - Développement" |
| `regions` | integer | ID de région française, obtenu via `list_filters(filter='regions')` |
| `contracts` | string | ID du type de contrat : `1` (Stage), `2` (Alternance), `-4` (Emploi), `-5` (Job étudiant) |
| `begin` | integer | Mois de début souhaité (1-12) |
| `duration` | integer | Durée maximale de la mission en mois |
| `limit` | integer | Nombre maximum de résultats (défaut : 10, max recommandé : 20) |

Au moins un des paramètres `term`, `description` ou `matieres` est requis.

`disciplines` (16 valeurs, secteurs larges) et `matieres` (69 valeurs, plus précises) sont deux niveaux de granularité indépendants — utiliser `matieres` pour un filtrage fin (ex : "Développement web" plutôt que "Informatique").

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

### Recherche d'offres par titre et région

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

### Recherche par matière et descriptif

```bash
curl -X POST https://mcp.iquesta.com/api/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 5,
    "method": "tools/call",
    "params": {
      "name": "search_jobs",
      "arguments": {
        "description": "Python",
        "matieres": [27]
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
