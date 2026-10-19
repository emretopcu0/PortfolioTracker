# PortfolioTracker — Project Guide for Claude

## What this is

Open-source, multi-currency personal portfolio tracker: private pension funds (BES), gold, FX, BIST equities and cash in one place.

"PortfolioTracker" is a working name. Do not use "Wealthfolio" (an existing product).

## Conventions

- Code, identifiers, comments, docs, README, ADRs and commit messages are in English. Commit messages follow Conventional Commits.
- Work in small, verifiable steps: one task at a time, each ending in a reviewable diff.
- For every task, report:
  - what changed
  - why, including the simpler alternative that was rejected
  - how it was verified (the commands actually run and their results)
  - a suggested commit message, plus the files to review
- Never claim a build or test passed unless it was actually run and seen to pass. Never invent badges, metrics or features.

## Git rules

- The maintainer does commits, pushes and merges. Never run `git commit`, `git push`, `git merge`, `git rebase`, `git reset --hard` or `git config --global`; they are also denied in `.claude/settings.json`.
- Never change author/committer identity or rewrite history.
- Workflow: short-lived feature branch → PR → CI green → squash merge to `main`.
- Sessions start in Manual permission mode (`permissions.defaultMode` in `.claude/settings.json`).

## Architecture decisions

These are already decided. Record each one as an ADR in `docs/adr/` when it is implemented.

- .NET 10 (LTS), ASP.NET Core Web API, EF Core 10, PostgreSQL.
- Modular monolith with at most three modules: **Users**, **Portfolio** and **MarketData**. Start simple with one API project organised by feature folders, a separate Domain project, and test projects. Do not split each module into separate Domain/Application/Infrastructure/Api projects.
- No MediatR, no AutoMapper (both moved to commercial licences in 2025, and neither is needed) and no generic repository pattern. Prefer plain services/handlers and explicit mapping.
- Frontend: React + TypeScript + Vite, served from the same origin as the API.
- Auth: ASP.NET Core Identity with `MapIdentityApi` and **cookie** auth. No JWT in localStorage.
- Tests: xUnit; integration tests against real PostgreSQL via Testcontainers; Playwright E2E later (Phase 8).
- Introduce Redis, OpenTelemetry, API versioning and similar pieces only when a phase actually needs them.

## Financial correctness rules

- Money is `decimal` and always paired with a currency (a Money value object). Never use `double`/`float` for money.
- Holdings are derived from a **transaction ledger** (buy, sell, deposit, withdrawal, dividend, fee, FX conversion). Never store them as mutable balances.
- Each transaction stores its date, currency, price, quantity, fees and the FX rate used.
- Keep realized and unrealized P&L separate. XIRR covers money-weighted returns (Phase 7).
- BES: when we get there, model fund units, unit price, contributions, state contribution (devlet katkısı) and vesting (hak ediş) as separate concepts.
- Every price and rate keeps its source, as-of timestamp and staleness. TCMB rates are indicative (gösterge) and differ from real buy/sell rates; the UI says so.

## Market data rules

- All providers sit behind interfaces (e.g. `IExchangeRateProvider`, `IPriceProvider`), with a deterministic demo provider so the app runs offline.
- FX uses two real providers: **TCMB** (TRY-based) and **ECB reference rates** (e.g. via Frankfurter). Two implementations make the abstraction genuine.
- Gold and BIST use **manual price entry plus a demo provider** until a provider's licence and terms are verified. BIST data is licensed through Borsa İstanbul's distributors; do not scrape or redistribute it.
- BES fund prices are published on TEFAS. Check its terms before any programmatic use (Phase 7).
- If a provider fails, keep working with the last good value and mark it as stale. Provider errors never crash the app.

## Security and public-repo hygiene

- **This repository is public.** Never put personal information about anyone into tracked files, commit messages, PRs or issues. Maintainer-specific instructions live in the untracked `CLAUDE.local.md`.
- Secrets live in `dotnet user-secrets` locally and in environment variables / CI secrets elsewhere. Never commit secrets, tokens or connection strings with passwords.
- Users can only access their own data. Enforce this in queries and cover it with tests.
- No real financial data. Demo data is fake and deterministic.

## Roadmap

Dates are targets, not promises.

| Phase | Scope | Target |
|---|---|---|
| 0 | Tools, GitHub, repo hygiene, docs skeleton | Oct–Nov 2026 |
| 1 | Requirements, domain model, ADR-001 architecture, ADR-002 cost-basis method | Nov 2026 |
| 2 | Solution skeleton, PostgreSQL via Docker Compose, EF Core, xUnit + Testcontainers, GitHub Actions build/test | Dec 2026 |
| 3 | Transactions and financial core | Jan 2027 |
| 4 | Identity, authorization, REST API, ProblemDetails, validation | Jan–Feb 2027 |
| 5 | TCMB + ECB providers, BackgroundService refresh, caching, staleness | Feb 2027 |
| 6 | React dashboard, portfolio screens, TR/EN i18n | Feb–Mar 2027 → **MVP** |
| 7 | Gold/BIST/TEFAS providers (licence permitting), XIRR, history charts | Spring 2027 |
| 8 | Hardening: security review, Playwright E2E, performance, OpenTelemetry | Spring 2027 |
| 9 | Docker images, deployment, public demo with fake data → **v1.0** | Summer 2027 |
| 10 | Extract the FX client as a NuGet package | After v1.0 |
| Later | .NET 12 LTS migration (Nov 2027) | 2027–2028 |

The **MVP** covers:
- register/login and portfolios
- transactions, cost basis, and realized/unrealized P&L
- TCMB and ECB FX rates, with values shown in TRY/EUR/USD/GBP
- manual prices and a basic dashboard

## Current status

Phase 0 is in progress.

- [x] Repo created with README, VisualStudio .gitignore and MIT licence
- [x] This file and `.claude/settings.json` (git deny rules, Manual mode by default)
- [x] `.gitattributes` (line endings) and `.editorconfig`
- [x] Extend `.gitignore` for Node/Vite
- [x] Honest work-in-progress README skeleton
- [ ] GitHub repo description and topics
- [x] `docs/adr/` with an ADR template
- [x] Basic issue/PR templates
- [ ] GitHub labels and a milestone for Phase 1

Update this section at the end of every task.
