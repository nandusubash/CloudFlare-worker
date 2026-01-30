# Feedback Dashboard

AI-powered customer feedback management system built on Cloudflare's edge platform.

## Features

- **AI Semantic Search** - Find similar feedback using vector embeddings
- **Automated Analysis** - Automatic sentiment, urgency, and theme detection
- **Multi-Source Support** - Collect feedback from Discord, GitHub, Twitter, Email, Support Tickets
- **Real-time Dashboard** - Clean Asana-inspired UI with live updates
- **Edge Performance** - Global deployment with sub-100ms response times

## Tech Stack

Built entirely on Cloudflare's serverless platform:

- **Workers** - Edge compute for API and static serving
- **D1** - Serverless SQL database
- **Workers AI** - Embeddings (`bge-base-en-v1.5`) and LLM (`llama-3.1-8b`)
- **Vectorize** - Vector database for semantic search
- **R2** - Object storage for attachments
- **KV** - Key-value cache
- **Workflows** - Background job processing

## Quick Start

### Prerequisites

- Node.js 18+
- Cloudflare account
- Wrangler CLI

### Installation

```bash
# Install dependencies
npm install

# Login to Cloudflare
npx wrangler login

# Create D1 database
npx wrangler d1 create feedback-db

# Create Vectorize index
npx wrangler vectorize create feedback-embeddings --dimensions=768 --metric=cosine

# Create R2 bucket
npx wrangler r2 bucket create feedback-attachments

# Create KV namespace
npx wrangler kv:namespace create KV

# Update wrangler.jsonc with your resource IDs
```

### Database Setup

```bash
# Apply schema
npx wrangler d1 execute feedback-db --file=schema.sql --remote

# Seed with sample data (optional)
curl -X POST https://your-worker.workers.dev/api/seed
```

### Development

```bash
# Run locally
npx wrangler dev --remote

# Deploy to production
npx wrangler deploy
```

## API Endpoints

### Feedback
- `GET /api/feedback` - List all feedback
- `POST /api/feedback` - Create new feedback
- `GET /api/feedback/:id` - Get feedback details

### AI Features
- `POST /api/search/similar` - Semantic search
- `POST /api/embeddings/index-all` - Generate embeddings
- `POST /api/ai-summarize` - Generate AI summary

### Analytics
- `GET /api/analytics` - Get dashboard metrics
- `GET /api/workflow/status` - Check analysis status

## Configuration

Key settings in `wrangler.jsonc`:

```jsonc
{
  "name": "feedback-dashboard",
  "compatibility_date": "2024-01-01",
  "d1_databases": [{
    "binding": "DB",
    "database_name": "feedback-db",
    "database_id": "your-db-id"
  }],
  "vectorize": [{
    "binding": "VECTORIZE",
    "index_name": "feedback-embeddings"
  }],
  "ai": {
    "binding": "AI"
  }
}
```

## Architecture

```
┌─────────────┐
│   Browser   │
└──────┬──────┘
       │
       ▼
┌─────────────────────────────────┐
│    Cloudflare Workers (Edge)    │
│  • API Routes                   │
│  • Static File Serving          │
│  • CORS Handling                │
└────┬────────────────────────┬───┘
     │                        │
     ▼                        ▼
┌─────────┐            ┌──────────────┐
│   D1    │            │  Workers AI  │
│Database │            │  • Embeddings│
└─────────┘            │  • LLM       │
     │                 └──────┬───────┘
     │                        │
     ▼                        ▼
┌─────────┐            ┌──────────────┐
│   KV    │            │  Vectorize   │
│ Cache   │            │Vector Search │
└─────────┘            └──────────────┘
     │
     ▼
┌─────────┐            ┌──────────────┐
│   R2    │            │  Workflows   │
│Storage  │            │ Background   │
└─────────┘            └──────────────┘
```

## Project Structure

```
CloudFlare-worker/
├── src/
│   ├── index.js          # Main Worker entry point
│   ├── embeddings.js     # AI & vector operations
│   ├── seed.js           # Sample data
│   └── workflow.js       # Background jobs
├── public/
│   ├── index.html        # Dashboard UI
│   ├── styles.css        # Styling
│   └── toast.css         # Notifications
├── schema.sql            # Database schema
├── wrangler.jsonc        # Cloudflare config
└── package.json
```

## Environment Variables

No environment variables needed! All configuration is in `wrangler.jsonc` and uses Cloudflare bindings.



## License

MIT

## Support

For issues or questions, please open a GitHub issue.
