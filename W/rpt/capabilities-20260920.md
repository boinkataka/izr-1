# Cloudflare MCP Server — Available Capabilities Report

**Date:** 2026-09-20  
**Source:** Cloudflare MCP server (3 tools) + Cloudflare OpenAPI spec (168 products)

## Table of Contents

1. [Tool Overview](#1-tool-overview)
2. [Capability 1: Documentation Search](#2-capability-1-documentation-search)
3. [Capability 2: API Execution](#3-capability-2-api-execution)
4. [Capability 3: OpenAPI Spec Search](#4-capability-3-openapi-spec-search)
5. [API Product Coverage](#5-api-product-coverage)
6. [API Operation Coverage](#6-api-operation-coverage)
7. [Documentation Coverage](#7-documentation-coverage)
8. [Key Findings](#8-key-findings)

---

## 1. Tool Overview

The Cloudflare MCP server exposes **3 tools**, each with a distinct capability:

| # | Tool | Purpose |
|---|------|---------|
| 1 | `mcp__cloudflare_docs` | Search Cloudflare documentation |
| 2 | `mcp__cloudflare_execute` | Execute JavaScript against the Cloudflare API |
| 3 | `mcp__cloudflare_search` | Search the Cloudflare OpenAPI spec |

---

## 2. Capability 1: Documentation Search

**Tool:** `mcp__cloudflare_docs`

**One-line summary:** Searches Cloudflare's official documentation (Workers, Pages, R2, Zero Trust, AI, Billing, etc.) and returns semantically similar text chunks for any question about Cloudflare products or features.

**Schema:**
```ts
type Args = {
  query: string; // Cloudflare documentation search query
};
```

**Coverage:** The tool explicitly covers these product families:
- **Compute & Platform:** Workers, Pages, R2, Images, Stream, D1, Durable Objects, KV, Workflows, Hyperdrive, Queues
- **AI:** AI Search, Workers AI, Vectorize, AI Gateway, Browser Rendering
- **Security:** Zero Trust, Access, Tunnel, Gateway, Browser Isolation, WARP, DDoS, Magic Transit, Magic WAN
- **Networking & CDN:** CDN, Cache, DNS, Zaraz, Argo, Rulesets, Terraform, Account and Billing

**Observed behavior:** Returns results as semantically similar chunks with URL, title, and text metadata.

---

## 3. Capability 2: API Execution

**Tool:** `mcp__cloudflare_execute`

**One-line summary:** Executes JavaScript async arrow functions that call the Cloudflare API via `cloudflare.request()`, enabling read, create, update, and delete operations across all Cloudflare resources.

**Schema:**
```ts
type Args = {
  code: string; // JavaScript async arrow function to execute
  account_id?: string; // Cloudflare account ID to scope execution
};
```

**API capabilities exposed:**
- `GET` — read/list resources
- `POST` — create resources
- `PUT` — update/replacement
- `PATCH` — partial updates
- `DELETE` — delete resources
- `cloudflare.request()` supports `query`, `body`, `contentType`, and `rawBody` options
- The `accountId` variable is pre-set to the authenticated account

**Observed behavior:** Successfully executed against the live API (verified by successful responses from the Cloudflare API).

---

## 4. Capability 3: OpenAPI Spec Search

**Tool:** `mcp__cloudflare_search`

**One-line summary:** Searches the complete Cloudflare OpenAPI specification (168 products, all `$refs` pre-resolved inline) via JavaScript code, allowing discovery of endpoints by product, method, path, or operation summary.

**Schema:**
```ts
type Args = {
  code: string; // JavaScript async arrow function to search the OpenAPI spec
};
```

**Access pattern:**
```ts
async () => {
  const results = [];
  for (const [path, methods] of Object.entries(spec.paths)) {
    for (const [method, op] of Object.entries(methods)) {
      if (op.summary) results.push({ method: method.toUpperCase(), path, summary: op.summary });
    }
  }
  return results;
}
```

**Observed behavior:** Successfully returned the full endpoint catalog (first 200 results visible, response truncated at ~6,000 tokens).

---

## 5. API Product Coverage

The OpenAPI spec contains **168 products** (per tool documentation). Verified tags include:

### Core Platform & Compute
- **Workers:** Worker Script, Worker Deployments, Worker Environment, Worker Routes, Worker Subdomain, Worker Cron Trigger, Worker Tail Logs, Worker Versions, Workers AI, Workers KV Namespace, Workers Smart Placement, Workers for Platforms
- **Pages:** Pages Project, Pages Deployment, Pages Assets, Pages Domains, Pages Build Cache
- **R2:** R2 Account, R2 Bucket, R2 Catalog, R2 Catalog Management, R2 Object, R2 Super Slurper
- **D1:** Database management
- **Durable Objects:** Namespace, Namespace Management
- **Workflows:** Workflow orchestration
- **Hyperdrive, Queues, Stream, Images, Vectorize**

### Security & Zero Trust
- **Access:** Applications, Groups, Identity Providers, mTLS certificates, Custom Pages, SAML certificates, Service Tokens, Users, Policy Tester, SCIM, JIT request logs
- **Gateway:** Zero Trust Gateway rules, locations, proxy endpoints, PAC files, DNS destination pairs
- **WAF:** Firewall rules, WAF packages, WAF rule groups, WAF overrides, API Shield (API Discovery, Schema Validation 2.0, Schema Validation Settings, WAF Expression Templates)
- **Cloudflare One / CASB:** DLP (Data Classes, Datasets, Profiles, Tags, Settings), Security Center (Audit Log, Insights, Scans), CASB posture findings
- **Turnstile, Spectrum, Load Balancers, Health Checks**

### AI
- **AI Search:** Instances, Jobs, Namespaces, Tokens, Account Search
- **AI Gateway:** Gateways, Providers, Provider Configs, Datasets, Dynamic Routes, Logs, Evaluations, Custom Domains, Account Provider Costs
- **Workers AI:** Inference, Finetune
- **AutoRAG:** RAG, RAG Search, Jobs
- **AI Security for Apps, AI Audit**

### Networking & DNS
- **DNS:** DNS Records (Zone/Account), DNS Settings, DNSSEC, DNS Firewall, DNS Analytics
- **Magic:** Magic Interconnects, Magic BGP, Magic Connectors, Magic GRE tunnels, Magic IPsec tunnels, Magic Network Monitoring, Magic Site ACLs, Magic Static Routes
- **Radar:** BGP, ASN Intelligence, IP, Domains, HTTP, DNS, Robots.txt, CT, Bots, Verified Bots, Leaked Credential Checks
- **IP Address Management:** Address Maps, BGP Prefixes, Leases, Prefixes, Service Bindings

### Email
- **Email Sending:** Sending, subdomains, suppressions
- **Email Routing:** Destination addresses, routing rules, settings, suppressions
- **Email Security:** Settings, Analytics, Email Auth

### Observability & Logs
- **Logpush:** Jobs (Zone/Account), transformers
- **Logpull, Live Tail, Live streams, Analytics, Analytics Engine, Analytics SQL, Web Analytics**
- **Log Explorer:** Datasets, Queries, Saved Queries
- **Audit Logs, Request Tracer**

### Account & Billing
- **Accounts:** List, Create, Update, Delete, Move
- **Billing:** Account Billing, Billing History, Billing Profile, Payment Methods, Invoices, Subscriptions, Usage, Billable Usage, Billable Usage V2
- **IAM:** Roles, Permission Groups, Members, API Tokens, Invites, Organizations
- **Settings:** Account Settings, Zone Settings, Account Resource Groups

### Additional Products
- **Builds, Container Images, Container Instances, CNIs, Deploy Hooks, Deployment Groups, Environment Variables, Field Extractors, GitHub Integration, Keyless SSL, Meetings, MoQ Relays, Observability, Observatory, Page Rules, Registrar Domains, Repository Connections, Rollouts, SCIM Discovery, Secrets Store, Tagging, Table Maintenance, Token Validation, URL Scanner, Waiting Room, Web3 Hostname, Webhooks, Zaraz**

---

## 6. API Operation Coverage

Verified operations include (from the returned catalog):

| Resource | Operations |
|----------|-----------|
| `/accounts` | GET (List), POST (Create), POST (Batch move) |
| `/accounts/{account_id}` | GET (Details), PUT (Update), DELETE (Delete) |
| `/accounts/{account_id}/access/apps` | GET, POST, PUT, DELETE |
| `/accounts/{account_id}/access/apps/{app_id}/policies` | GET, POST, PUT, DELETE |
| `/accounts/{account_id}/access/users` | GET, POST, PUT, DELETE |
| `/accounts/{account_id}/access/certificates` | GET, POST, PUT, DELETE |
| `/accounts/{account_id}/access/mcp/servers` | GET, POST, PUT, DELETE, Sync |
| `/accounts/{account_id}/access/mcp/portals` | GET, POST, PUT, DELETE |
| `/accounts/{account_id}/d1/database` | GET, POST (per tool example) |
| `/accounts/{account_id}/workers/scripts` | GET, PUT, POST (per tool example) |
| `/accounts/{account_id}/v1/images` | GET, POST, PUT, PATCH, DELETE |
| `/accounts/{account_id}/v2/images/direct_upload` | POST |
| `/accounts/{account_id}/abuse-reports` | GET, POST |
| `/accounts/{account_id}/addressing/address_maps` | GET, POST, PATCH, DELETE |
| `/accounts/{account_id}/access/organizations` | GET, POST, PUT |
| `/accounts/{account_id}/access/service_tokens` | GET, POST, PUT, DELETE |
| `/accounts/{account_id}/access/groups` | GET, POST, PUT, DELETE |
| `/accounts/{account_id}/access/identity_providers` | GET, POST, PUT, DELETE |
| `/accounts/{account_id}/access/tags` | GET, POST, PUT, DELETE |
| `/accounts/{account_id}/access/policies` | GET, POST, PUT, DELETE |
| `/accounts/{account_id}/access/users/{user_id}` | GET, PUT, DELETE |
| `/accounts/{account_id}/access/users/{user_id}/mfa_authenticators/{authenticator_id}` | DELETE |
| `/accounts/{account_id}/access/saml_certificates` | GET, POST |
| `/accounts/{account_id}/access/saml_certificates/{saml_cert_set_id}/rotate` | POST |
| `/accounts/{account_id}/access/keys` | GET, PUT, POST (rotate) |
| `/accounts/{account_id}/access/policy-tests` | POST, GET |

**HTTP methods supported:** GET, POST, PUT, PATCH, DELETE

---

## 7. Documentation Coverage

The `mcp__cloudflare_docs` tool returned documentation pages for the Cloudflare Agents SDK, including:

| Documentation Page | Description |
|-------------------|-------------|
| **Agents** | Create stateful AI agents with persistent memory, real-time WebSocket connections, and scheduled tasks |
| **Agents API** | Reference for Agent class, lifecycle hooks, SQL storage, and error handling |
| **Agent Memory** | Add durable memory recall and ingestion to an agent |
| **Workflows** | Integrate Cloudflare Workflows with Agents for durable, multi-step background processing |
| **Chat Agents** | Build AI chat interfaces with AIChatAgent and useAgentChat |
| **Email Agent** | Build an agent that sends, receives, routes, and replies to email |
| **Voice Agent** | Build a real-time voice agent with speech-to-text, LLM processing, and text-to-speech |
| **Think Harness** | Opinionated chat agent framework with built-in tools, persistent memory, streaming, and sub-agent RPC |
| **Programmatic Submissions** | Durably accept a Think turn with `submitMessages()` |
| **MCP Handler APIs** | Create stateless or legacy MCP server handlers for Cloudflare Workers |
| **Build Code Mode MCP Server** | Replace an MCP server's individual tools with one sandboxed Code Mode tool |
| **MPP (Machine Payments Protocol)** | Accept and make payments using MPP with Cloudflare Workers |
| **Charge for HTTP Content** | Gate HTTP endpoints with MPP payments |
| **getCurrentAgent()** | Access the current agent context from external utility functions |
| **MCP Client API** | `listTools()` to inspect the raw MCP catalog |
| **MCP Handler API** | Create stateless or legacy MCP server handlers |

---

## 8. Key Findings

1. **The Cloudflare MCP server has exactly 3 tools:** documentation search, API execution, and OpenAPI spec search.
2. **The OpenAPI spec is comprehensive** — 168 products covering compute, security, AI, networking, email, observability, and billing.
3. **API execution is powerful** — any Cloudflare API endpoint can be called via `cloudflare.request()` with full CRUD support.
4. **MCP is itself a Cloudflare product** — the spec includes MCP Portals and MCP Servers under Zero Trust Access.
5. **All `$refs` are pre-resolved inline** in the spec, making programmatic exploration straightforward.
6. **The `execute` tool is pre-scoped** to the authenticated account.
7. **Documentation is extensive** — covering the Agents SDK, MCP, Workflows, Think, MPP, and other developer tooling.
