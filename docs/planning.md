# PullVault Planning Notes

## Problem Statement
Existing comic automation tools are slow today because of past over-aggressive search behavior that led to heavy throttling by indexers. Some are Python-based with dependency drift and plaintext credential storage. Torrent acquisition is especially messy due to batch packs and inconsistent naming.

PullVault aims to be fast, secure, and easy to deploy while supporting multiple acquisition sources and a clean, automated file pipeline.

## Goals
- Single-binary deployment with embedded web UI.
- Secure credential storage (no plaintext secrets).
- Efficient searching with rate limits, caching, and backoff.
- Multi-source acquisition: torrents, Usenet, DDL, AirDC++.
- Metadata-driven monitoring for series and story arcs.
- Optional archive conversion to CBZ, metadata embedding, and renaming.

## Non-Goals (For Now)
- Full parity with existing tools on day one.
- Complex plugin ecosystems before core workflows are stable.
- Solving all torrent naming and pack quality issues immediately.

## Target Users
- Self-hosters with existing download infrastructure.
- Collectors who track series, arcs, and missing issues.
- Users who want an "arr-like" experience without Python runtime management.

## Proposed Architecture
**Core**
- Language: Go for a single, portable binary.
- Storage: SQLite for library data, jobs, and history.
- Optional: Postgres, including support for schema-separated tables (Supabase-style).
- UI: React (TanStack) + Tailwind built and embedded into the Go binary.

**Services / Modules**
- Scheduler: orchestrates metadata refresh, search windows, and retries.
- Metadata: monitors providers for new issues and arc updates.
- Search: coordinates indexer, tracker, web, and AirDC++ queries with rate limits and caching.
- Acquisition: adapters for torrent, Usenet, DDL (with and without JDownloader 2), and AirDC++.
- File Pipeline: identify, normalize, convert CBR/CBZ to CBZ, embed metadata, rename.

**Security**
- Encrypt secrets at rest with an approach that works in servers and containers.
- Do not log sensitive data.

**Performance / Rate Limiting**
- Adaptive throttling per indexer.
- Query caching and de-duplication.
- Backoff and cooldown windows on aggressive sources.

## Workflow (Target)
1. User adds series or story arc lists.
2. Metadata watchers detect new or missing issues.
3. Search workers query configured sources.
4. Acquisition pulls files via configured clients.
5. Pipeline converts, tags, renames, and organizes.

## Roadmap (Draft)
**Phase 0**
- Data model, SQLite schema, basic config.
- Embedded UI shell and navigation.

**Phase 1**
- Metadata provider integration.
- Series tracking and issue calendar.

**Phase 2**
- Torrent and Usenet client adapters.
- DDL via JDownloader 2 and AirDC++ integration.

**Phase 3**
- File pipeline: conversion, metadata embedding, renaming.

**Phase 4**
- Advanced search strategies for torrents and pack releases.

## Open Questions
- Which metadata providers are required day one?
- How should story arcs be modeled and sourced?
- CLI scope vs web-only UI at launch?
- Should adapters be hard-coded modules or a plugin system?
- What are the minimum secrets needed for a first release?
