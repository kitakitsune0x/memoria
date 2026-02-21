# Memoria

Structured memory system for AI agents with local markdown vault and two-way Notion sync.

> Local-first. Markdown-first. Notion-synced.

## Requirements

- Node.js 18+

## Install

```bash
npm install -g memoria
```

Or run locally:

```bash
git clone <repo-url>
cd memoria
npm install
npm run build
```

## Quick Start

```bash
# Initialize a vault
memoria init ~/memory --name my-brain

# Store memories
memoria remember decision "Use PostgreSQL" --content "Chosen for JSONB and reliability"
memoria remember lesson "Cache invalidation" --content "TTL-based is simpler than event-driven"
memoria store inbox "meeting-notes" --content "Discussed Q2 roadmap" --tags "meetings,planning"

# Search
memoria search "postgresql"

# List documents
memoria list
memoria list decisions

# Session lifecycle
memoria wake
memoria checkpoint --working-on "auth module" --focus "token refresh"
memoria sleep "finished auth module" --next "deploy to staging"

# Check vault status
memoria status
```

## Notion Integration

Memoria syncs your local vault to Notion, giving you a rich UI for browsing and editing memories while keeping local markdown files as the source of truth.

### Setup

1. Create a [Notion integration](https://www.notion.so/my-integrations) and copy the token.
2. Create a root page in Notion and share it with your integration.
3. Copy the page ID from the URL (the 32-character hex string).

```bash
memoria setup-notion --token ntn_your_token_here --page abc123def456...
```

### Sync

```bash
# Push local changes to Notion
memoria sync --push

# Pull Notion changes to local
memoria sync --pull

# Two-way sync (default)
memoria sync

# Preview changes without modifying anything
memoria sync --dry-run

# On conflict, prefer Notion's version over local
memoria sync --pull --prefer-notion
```

### How Sync Works

- **Push**: Local documents are compared against the last sync state. New or updated files are created/updated in Notion.
- **Pull**: Notion databases are queried for pages modified since the last sync. Changes are written back to local markdown.
- **Conflicts**: When both sides have changed, local is preferred by default. Use `--prefer-notion` to override.
- **Databases**: One Notion database is created per category (decisions, lessons, facts, etc.) under the root page.

## CLI Reference

| Command | Description |
|---------|-------------|
| `memoria init <path>` | Initialize a new vault |
| `memoria remember <type> <title>` | Store a typed memory |
| `memoria store <category> <title>` | Store in explicit category |
| `memoria search <query>` | Search by text query |
| `memoria list [category]` | List documents |
| `memoria get <id>` | Retrieve a document |
| `memoria wake` | Start a session |
| `memoria sleep <summary>` | End session with handoff |
| `memoria checkpoint` | Mid-session snapshot |
| `memoria status` | Vault stats and session state |
| `memoria setup-notion` | Configure Notion integration |
| `memoria sync` | Sync with Notion |

### Memory Types

Used with `memoria remember <type>`:

- `fact` -- raw information, data points
- `decision` -- choices made with reasoning
- `lesson` -- insights, patterns learned
- `commitment` -- promises, goals, obligations
- `preference` -- likes, dislikes, how you want things
- `relationship` -- people, connections
- `project` -- active work, ongoing efforts

Each type maps to a category folder in the vault.

## Vault Structure

```
~/memory/
  .memoria.json           # vault configuration
  .sync-state.json        # Notion sync tracking
  decisions/
    use-postgresql.md
  lessons/
    cache-invalidation.md
  facts/
    ...
  sessions/
    current.md            # active session state
    handoffs/
      2026-02-21.md       # session handoff documents
```

Each document uses YAML frontmatter:

```markdown
---
title: Use PostgreSQL
type: decision
tags:
  - database
  - infrastructure
created: 2026-02-21T10:00:00Z
updated: 2026-02-21T10:00:00Z
---
Chosen for JSONB support and reliability. Evaluated against MySQL and SQLite.
```

## AGENTS.md Integration

Add these to your agent's instructions for session-aware memory:

```markdown
## Memoria
- Run `memoria wake` at session start.
- Run `memoria checkpoint --working-on "<task>"` during heavy work.
- Run `memoria sleep "<summary>" --next "<next steps>"` before ending.
- Use `memoria search "<query>"` before complex decisions.
- Use `memoria remember <type> "<title>" --content "<details>"` for important observations.
```

## License

MIT
