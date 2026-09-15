# Security

M41NFR4M3 combines session-based web authentication with wallet identity and on-chain reads. The practical requirement is that those paths meet the same bar: a UI action is not an authorized action until the server says it is.

## Server-side authorization

The server decides whether the current account may perform an action and whether the resulting state change is allowed. Hiding a control in the client is not access control.

## Authenticated operations

Sensitive reads and writes require an authenticated session. That includes identity linking, staking-related application updates, ledger entries, marketplace actions, and social and governance writes. Sending a wallet address or provider user id in a payload does not authenticate the caller.

## Wallet signature verification

Wallet authentication uses server-verified signed messages. A displayed address is not sufficient. A verified wallet identifies the keypair used at sign-in; later application actions still go through authenticated, server-authorized routes.

## Client and server

Secrets and privileged work stay on the server: OAuth handling, signature verification, database writes, marketplace API credentials, and storage access. The client handles UI, wallet prompts, and presenting results.

## Scheduled work

Scheduled and background jobs are privileged. They should not be invocable as ordinary user requests. Serverless and scheduled runs can overlap or repeat, so that work should be safe to run more than once.

## Input validation and rate limiting

User input is validated on the server before it is written to PostgreSQL, sent to storage, or forwarded to an external API — including form fields, identifiers, pagination, and social content.

Rate limiting applies to expensive or sensitive operations: authentication, identity linking, Solana-backed reads, marketplace integrations, and user-generated content. The aim is to keep a serverless request path from becoming an unbounded RPC, API, or write amplifier.

## Logging, monitoring, and credentials

Production operation uses structured logging (Pino) and error monitoring (Sentry). Logs should not carry secrets, tokens, private keys, or unnecessary personal data.

Production credentials stay in private configuration. Repository contents are described in [README.md](./README.md).

## On-chain state

Ownership and staking depend on chain data, but a client-reported token list, signature, or pending transaction is not enough to grant application effects. Server-side verification comes first. Where application records and chain data disagree, product rules treat Solana as the source for those on-chain facts and PostgreSQL as the application record.
