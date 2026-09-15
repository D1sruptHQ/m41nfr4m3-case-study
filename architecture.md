# Architecture

M41NFR4M3 is a Next.js application. The browser talks to a React frontend; that frontend relies on a server and API layer for authorization, persistence, and calls to external systems.

```
Browser
  → Next.js / React
    → server / API layer
      → PostgreSQL
      → Solana / RPC / DAS
      → OAuth providers
      → storage
      → external marketplace APIs
```

Scheduled and background jobs run alongside this request path. They cover work that should not depend on a user remaining on the page — synchronization and other deferred processing.

## Major components

**Browser.** Presents UI and captures user actions: navigation, forms, wallet prompts, marketplace and social interactions.

**Next.js / React.** The App Router application is the user-facing surface.

**Server / API layer.** Next.js Route Handlers and other server-side logic. This is where authentication checks, authorization, input validation, persistence, and outbound calls happen. Privileged work belongs here, not in the browser.

**PostgreSQL.** Application data lives here, accessed through Prisma: accounts and linked identities, collection-related records, ledger entries, marketplace records maintained by the product, and social/governance state.

**Solana / RPC / DAS.** On-chain reads and interactions. Ownership, staking, and other blockchain-backed facts are taken from Solana infrastructure rather than from the client.

**OAuth providers.** Discord and X authenticate third-party identities. Application permissions are still decided on the server after an account exists or is linked.

**Storage.** Vercel Blob provides object storage for assets that do not belong in PostgreSQL.

**External marketplace APIs.** Marketplace features use third-party marketplace systems in addition to application-owned state. The server mediates those calls.

**Scheduled / background jobs.** Periodic or retryable work that does not fit a single request/response cycle, run with GitHub Actions.

## Authorization

Authorization happens on the server. The browser can hide UI; it does not grant access. Client-supplied claims — wallet address, ownership, balance, and similar — are inputs to verify, not facts to accept.

After authentication (OAuth or wallet), the server decides whether the current account may perform an action and what gets persisted.

## Dual system of record

PostgreSQL is the application system of record. It stores accounts, linked identities, rewards and economy data, marketplace records the product maintains, and social/governance state.

Solana is the source of truth for relevant on-chain state such as NFT ownership and staking. The application consults RPC and DAS for those facts.

That split is the main architectural constraint. Product features compose both sources: an NFT holder profile, a staking position, or a marketplace action may need an application record *and* a chain-backed fact. The two will not always match in time — confirmation is asynchronous, and an indexer is not the application database. The server is responsible for composing them without treating the client or a single API response as the whole picture.

This is why the system is neither a purely on-chain app nor a conventional web app with a wallet button attached. External systems (OAuth, object storage, marketplace APIs, job runners) plug into the same server layer; they extend the application, they do not replace authorization or the two records above.
