# AI Voice Sales Agent

An AI-powered voice assistant that handles customer support calls for a server components & accessories store. It automates product recommendations, order tracking, and sales conversations — reducing response time from minutes to seconds.

## Problem

Retail stores lose **30-40% of potential sales** because customers can't get immediate answers about product availability, pricing, or order status. Hiring 24/7 support staff costs $2,000-5,000/month per agent.

This solution provides an always-available AI voice agent that:
- Answers product questions with real-time inventory data
- Tracks orders by ID with identity verification
- Recommends products based on customer needs and budget
- Handles objections and closes sales

## Architecture

```
Customer Call
      |
      v
ElevenLabs (Voice AI)
      |
      v
n8n Workflow (Orchestrator)
      |
      +---> GET /products  --> Product catalog (30 items)
      |
      +---> POST /orders   --> Order lookup by ID
                |
                +--> Order exists? --> Return order details
                |
                +--> Not found?   --> Return 404 error
```

### Workflow Detail

```
Webhook (POST /orders)
    |
    v
Validate orderId exists in body?
    |
    +-- YES --> HTTP Request GET /orders/{orderId}
    |               |
    |               v
    |          Order found?
    |               |
    |               +-- YES --> Respond with order data (200)
    |               |
    |               +-- NO  --> Respond "order not found" (404)
    |
    +-- NO  --> Respond "orderId required" (400)
```

## Key Metrics

| Metric | Before | After |
|--------|--------|-------|
| Response time | 2-5 min (human) | < 3 sec |
| Availability | Business hours | 24/7 |
| Cost per interaction | $1-3 (human agent) | $0.02 (API calls) |
| Product accuracy | Varies | 100% (real-time data) |

## Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Voice AI | ElevenLabs Conversational AI | Natural voice interactions |
| Orchestrator | n8n (self-hosted) | Workflow automation, API routing |
| Backend API | json-server (Node.js) | Product catalog + order management |
| Tunnel | ngrok | Expose local services to ElevenLabs |
| Auth | Header-based API key | Secure webhook endpoints |

## Security Features

- API key authentication on all webhook endpoints
- Customer identity verification (name + order ID) before sharing order data
- Phone number fallback verification if name doesn't match
- No sensitive data exposure (internal emails, payment details)
- Prompt injection protection (agent ignores behavior override attempts)

## Quick Start

### Prerequisites

- Node.js v18+
- n8n instance (self-hosted or cloud)
- ElevenLabs account with Conversational AI
- ngrok account (free tier works)

### 1. Install dependencies

```bash
cd my-store
npm install
```

### 2. Configure environment

```bash
cp .env.example .env
# Edit .env with your API keys
```

### 3. Start the backend

```bash
npm start
# API running at http://localhost:3000
```

### 4. Expose with ngrok

```bash
ngrok http 5678
# Use the public URL in ElevenLabs agent config
```

### 5. Import n8n workflow

Import `workflows/Tools Eleven-labs (1).json` into your n8n instance.

## API Endpoints

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/products` | List all products (30 items) | No |
| GET | `/orders` | List all orders (20 items) | No |
| GET | `/orders/:id` | Get specific order | No |
| POST | `/webhook/my-store/products` | n8n webhook - product query | API key |
| POST | `/webhook/my-store/orders` | n8n webhook - order lookup | API key |

## Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| `connection refused` on ngrok | ngrok pointing to wrong port | Restart: `ngrok http 5678` (n8n port) |
| Webhook returns 401 | Missing or wrong API key | Check `x-api-key` header matches `.env` |
| Order not found (false negative) | orderId format mismatch | Use format `XXXX-YYYY` (e.g., `1001-2024`) |
| n8n can't reach json-server | Docker network issue | Use `http://host.docker.internal:3000` |
| ElevenLabs no response | ngrok tunnel expired | Restart ngrok, update URL in ElevenLabs |

## Project Structure

```
my-store/
├── .env.example          # Environment template (API keys)
├── .gitignore            # Ignored files (secrets, node_modules)
├── package.json          # Node.js dependencies
├── README.md             # This file
├── index-agent.html      # ElevenLabs agent widget
├── system-prompt/
│   └── system-prompt.md  # AI agent behavior rules
└── workflows/
    └── Tools Eleven-labs (1).json  # n8n workflow (importable)
```

## License

MIT
