# Development timeline

How M41NFR4M3 grew from an initial application into a broader platform. These are phases of product and engineering work, not a commit history.

## November 2025 — Foundation

Initial platform development. The goal was a single web application instead of disconnected Discord bots, spreadsheets, marketplace UIs, and similar tools.

This phase established the Next.js / React application, the server and API layer, PostgreSQL via Prisma, and the deployment path — a base that later identity, on-chain, and product systems could attach to.

## Late November 2025 — Authentication and identity

Authentication and identity were added before product features for NFT holders.

Work covered NextAuth, OAuth 2.0 with Discord and X, and Solana wallet authentication via signed messages. Account linking became a core design problem: one application user with multiple proven identities, rather than a separate experience per login method.

## December 2025 – January 2026 — Core functionality for NFT holders

The first substantial product layer on top of identity, for NFT holders: profiles, collection data, traits, prestige, missions, and rewards.

Scope moved from sign-in to representing holder state and granting application-level outcomes. That required server-side ownership verification, derived collection data, and a rewards and economy model backed by PostgreSQL.

## February 2026 — Production hardening

Operational improvements around running the existing system more safely: input validation, rate limiting, logging (Pino), monitoring (Sentry), tests (Vitest), linting (ESLint), and tighter handling of scheduled work.

## February – June 2026 — Staking and on-chain systems

Staking and related on-chain systems expanded backend scope: lifecycle management around on-chain positions, deeper use of Solana RPC, DAS, Metaplex/UMI, and Wallet Adapter, and ongoing synchronization between chain data and application records.

Background jobs became more important in this phase because confirmation and reconciliation do not fit cleanly into a single request.

## July 2026 — Marketplace

Marketplace functionality added a third class of external dependency: marketplace APIs, in addition to OAuth and Solana.

The engineering problem was state across application records, third-party marketplace systems, and on-chain ownership. The server remained the integration point so marketplace actions could be authorized against verified state.

## August 2026 — Social and governance

Social, forum, messaging, and governance functionality moved community activity into the authenticated application.

The same account and identity model now supported user-generated content and governance participation, not only collection and marketplace flows. That widened what authorization and persistence had to cover.

## September 2026 — Broader access

Platform work to make M41NFR4M3 usable beyond the original D1srupt0rs context, toward the wider Solana community, while keeping the same application architecture.
