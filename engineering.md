# Engineering

The browser presents UI and user intent. The server authorizes actions and persists application state. PostgreSQL holds application records; Solana is consulted for on-chain facts such as ownership and staking. The sections below assume that split and focus on distinct problems on top of it.

## 1. Multi-provider identity and account linking

### Problem

Users arrive through Discord, X, and Solana wallets — separate identity systems. The product needs one application account that can accumulate collection data, rewards, marketplace activity, and social participation without forcing a single login method.

### Approach

Identity is modeled as an application account with linked providers, not as “the Discord user” or “the wallet” by itself. OAuth (Discord, X) and wallet authentication prove control of an identity; the server attaches that identity to an account. Sign-in and linking are server-side operations.

### Design consideration

The hard cases are the first link, later additional links, and a provider identity that is already known to the system. Those cases have to be handled explicitly so linking does not create duplicate accounts or attach the wrong identities.

## 2. Solana wallet authentication

### Problem

A displayed wallet address is not proof of control. The application needs wallet login as a first-class identity, including for users who may not have Discord or X connected, with rigor comparable to OAuth.

### Approach

Wallet authentication uses server-verified signed messages. The client requests a signature through Wallet Adapter; the server validates it and associates the wallet with the session and account.

### Design consideration

A verified wallet is an identity claim, not permission to perform every on-chain-related action in the product. Session-authenticated routes still decide what that account may do.

## 3. NFT ownership verification

### Problem

Features for NFT holders — profiles, collection data, access, rewards — depend on whether a wallet owns relevant NFTs. Client-reported ownership is easy to fabricate.

### Approach

Ownership is verified on the server against Solana infrastructure (RPC and DAS, with Metaplex/UMI where the token standard requires it). The API does not treat a client-provided token list as truth.

### Design consideration

Blockchain reads can be delayed or incomplete. Unverified ownership should not unlock privileged behavior.

## 4. On-chain and database synchronization

### Problem

PostgreSQL is the application system of record; Solana is the source of on-chain truth. Those views diverge: transactions confirm asynchronously, users retry, and derived data may be updated after the request that triggered the work.

### Approach

Writes that depend on chain state go through the server, which checks on-chain facts before persisting application effects. Synchronization also runs in scheduled and background jobs so the request path is not the only place derived data is refreshed. The application stores its own records rather than treating the chain as a general database.

### Design consideration

The system has to be safe under delay and retry. Product behavior should not assume that a wallet action visible in the UI is already in PostgreSQL, or the reverse. Re-processing derived records needs to be idempotent where the same work can run more than once.

## 5. Staking lifecycle management

### Problem

Staking is a multi-step lifecycle: initiate, confirm on-chain, represent in the application, later unstake or otherwise change. Failure can happen at any step — the user abandons a wallet prompt, a transaction does not confirm, or the HTTP request ends before chain state is final.

### Approach

Staking is handled as an explicit lifecycle on the server. On-chain state is the authority for whether a position exists; application state records what the product has verified and the application-level effects that follow (access, rewards accounting, UI). Background work reconciles staking activity that did not finish in the original request.

### Design consideration

Partial completion is a normal failure mode, not an edge case. A single request cannot be assumed to cover submit, confirm, and persist. The design has to tolerate abandoned prompts and delayed confirmation without double-applying rewards or losing a position that did confirm.

## 6. Ledger-based economy

### Problem

Rewards, missions, prestige-related grants, and other balances are application-level economic events. A single mutable total is hard to audit and easy to get wrong under retries, concurrent requests, or later rule changes.

### Approach

Economic effects are recorded as ledger entries rather than only as a mutable balance. Balances are derived from those entries. Grants and similar events are authorized on the server and persist as records.

### Design consideration

The ledger is an application construct, not on-chain settlement. Recording events rather than only updating a counter makes history inspectable and makes retries safer, provided the same event is not applied twice.

## 7. Marketplace state management

### Problem

Marketplace behavior depends on application records, external marketplace APIs, and on-chain ownership. Treating any one of those as complete produces wrong listings, sales, or permissions.

### Approach

The server mediates marketplace operations. It consults application state, external marketplace APIs, and ownership checks before updating records or authorizing a user action. Application-owned marketplace state lives in PostgreSQL; third-party systems remain external.

### Design consideration

Those three sources are not guaranteed to agree at the same moment. The engineering problem is coordination: assemble a consistent enough view to authorize an action, without pretending the external marketplace is the application database or that the client’s view of a listing is authoritative.

## 8. Serverless background work and abuse controls

### Problem

The deployment model is serverless (Vercel), with GitHub Actions and scheduled jobs for deferred work. Request handlers are publicly reachable. Expensive Solana reads, marketplace calls, and identity operations can be abused; scheduled work is privileged if it can be triggered like a user request.

### Approach

User-facing routes authenticate and authorize before doing privileged or expensive work. Rate limiting is applied to those APIs. Scheduled jobs are treated as separate, protected operations. Logging (Pino) and error monitoring (Sentry) support operating the system.

### Design consideration

Serverless execution is short-lived and retry-prone. Background work should be time-bounded and idempotent where the same job can overlap or repeat. Abuse controls belong on the server: rate limits, authentication, and input validation.
