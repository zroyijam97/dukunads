# AI Connector (MCP) — technical guide

The Dukun Ads AI Connector is a [Model Context Protocol](https://modelcontextprotocol.io) server secured with **OAuth 2.1 + PKCE** and **Dynamic Client Registration**. It works with any MCP client that supports remote OAuth connectors — including **Claude** and **ChatGPT**.

## Endpoints

Base: `https://dukunads.com`

| Purpose | Method & path |
|---------|---------------|
| MCP endpoint | `POST /api/mcp` (Bearer access token) |
| Protected-resource discovery | `GET /.well-known/oauth-protected-resource` |
| Authorization-server discovery | `GET /.well-known/oauth-authorization-server` |
| Dynamic client registration (RFC 7591) | `POST /api/mcp/oauth/register` |
| Authorize | `GET /api/mcp/oauth/authorize` |
| Token | `POST /api/mcp/oauth/token` |

## Flow

1. The client `POST`s to `/api/mcp` with no token → server responds `401` with
   `WWW-Authenticate: Bearer resource_metadata="https://dukunads.com/.well-known/oauth-protected-resource"`.
2. Client reads the discovery documents, then **registers** itself via DCR.
3. Client sends the user to `/api/mcp/oauth/authorize` (PKCE S256). If not signed in, Dukun Ads shows its sign-in page, then auto-approves and returns a single-use code (10-min TTL).
4. Client exchanges the code at `/api/mcp/oauth/token` for an **access token** (30-day) + **refresh token**.
5. Client calls `/api/mcp` with `Authorization: Bearer <access token>`. Every call is scoped to that user.

## Setup in Claude

1. **Settings → Connectors → Add custom connector**
2. URL: `https://dukunads.com/api/mcp`
3. **Connect** → sign in to Dukun Ads → **Allow**

## Setup in ChatGPT

1. **Settings → Connectors** (Developer mode) → **Add custom connector**
2. URL: `https://dukunads.com/api/mcp`
3. **OAuth** → sign in → **Allow**

## Security

- **No token in the URL** — the connector URL is safe to share; each person authenticates into their own account.
- Access tokens are per-user and scope every tool call to that user's own data.
- A legacy token-in-URL mode (`/api/mcp/{token}`) exists for MCP clients without OAuth support — that URL **is** sensitive and must not be shared.

See the full tool list in [TOOLS.md](TOOLS.md).
