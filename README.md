# M41NFR4M3

Technical case study of the architecture and engineering behind the product.

**Live Product:** https://m41nfr4m3.com/

## Overview

M41NFR4M3 is an authenticated web application that brings identity, collection data, rewards, marketplace functionality, community features and governance into a single platform. It was originally developed for the D1srupt0rs ecosystem and is expanding toward the wider Solana community.

The application brings together work that had previously been spread across Discord bots, spreadsheets, marketplace interfaces, and other separate tools.

## Role

**Founder & Full Stack Developer at D1srupt** — Lukas Introvic

Work covered product development, architecture, frontend, backend, integrations, and deployment.

## Evolution

The platform was built in stages, with each phase adding product surface and engineering scope:

- **November 2025** — foundation and initial application
- **Late November 2025** — authentication and identity
- **December 2025 – January 2026** — core functionality for NFT holders, including profiles, collection data, traits, prestige, missions, and rewards
- **February 2026** — production hardening
- **February – June 2026** — staking and on-chain systems
- **July 2026** — marketplace functionality
- **August 2026** — social and governance systems
- **September 2026** — broader access and platform expansion

A more detailed chronology is in [development-timeline.md](./development-timeline.md).

## What I Built

The work is a full-stack production application: a Next.js / React frontend, a server and API layer, PostgreSQL via Prisma, and deployment on Vercel. The main engineering areas were:

**Application architecture and full-stack development.** App Router UI, Next.js Route Handlers, and the server-side path that authorization, persistence, and integrations run through.

**Identity and authentication.** Multi-provider identity using NextAuth, Discord OAuth, X OAuth, and Solana wallet authentication, with those identities linked to an application account.

**Solana / blockchain integration.** Server-side use of Solana RPC, DAS, Metaplex/UMI, and Wallet Adapter for ownership verification, staking, and other on-chain interactions.

**Data and state management.** PostgreSQL as the application system of record, including accounts, collection-related data, marketplace records, social/governance state, and a ledger-based rewards and economy model.

**Staking and rewards systems.** Staking as an on-chain lifecycle that the application has to represent and reconcile, plus application-level rewards, missions, and prestige.

**Marketplace and community functionality.** Marketplace features that depend on both application state and external marketplace APIs, and later social, forum, messaging, and governance features on the same account model.

**Production operations.** Scheduled jobs, rate limiting, logging (Pino), monitoring (Sentry), and CI-oriented checks (Vitest, ESLint).

## Technology

**Languages**
TypeScript · JavaScript · SQL

**Frontend**
Next.js · React · Next.js App Router · Tailwind CSS

**Backend & Data**
Node.js · Next.js Route Handlers · PostgreSQL · Prisma

**Authentication**
NextAuth · OAuth 2.0 · Discord OAuth · X OAuth · Solana wallet authentication

**Solana / Web3**
Solana Web3.js · Solana Wallet Adapter · Metaplex · UMI · DAS (Digital Asset Standard) API · Solana RPC

**Infrastructure & Operations**
Vercel · Vercel Blob · GitHub Actions · Vitest · ESLint · Pino · Sentry

## Documentation

- [Architecture](./architecture.md)
- [Engineering](./engineering.md)
- [Development timeline](./development-timeline.md)
- [Security](./security.md)

## Source Code

Production source is maintained privately; this repository documents the architecture and engineering behind the product.

## Repository Scope

This repository contains documentation and portfolio material about the project. It does not contain the production source code or private operational data.
