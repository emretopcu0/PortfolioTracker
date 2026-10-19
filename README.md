# PortfolioTracker

Open-source, multi-currency personal portfolio tracker: private pension funds (BES), gold, FX, BIST equities and cash in one place.

> **Status: early work in progress.** The repository currently contains project documentation only. There is no runnable application, no release and no demo yet. "PortfolioTracker" is a working name and may change.

## Goals

- Track holdings in several asset classes and show their value in TRY, EUR, USD and GBP.
- Derive holdings from a transaction ledger (buy, sell, deposit, withdrawal, dividend, fee, FX conversion) instead of mutable balances.
- Report realized and unrealized profit and loss separately.
- Keep every price and FX rate together with its source, timestamp and staleness. Central bank rates are indicative and the UI will say so.
- Run offline with deterministic demo data. No real financial data is ever included.

## Planned stack

Not implemented yet. This is the intended direction:

- .NET 10, ASP.NET Core Web API, EF Core 10, PostgreSQL
- React, TypeScript and Vite, served from the same origin as the API
- ASP.NET Core Identity with cookie authentication
- xUnit and Testcontainers for tests, Playwright for end-to-end tests later

## Roadmap

The first milestone is an MVP: register/login and portfolios, transactions with cost basis and P&L, TCMB and ECB FX rates, manual prices and a basic dashboard. Gold, BIST and BES price providers depend on their licence and terms and come later.

Progress is tracked through GitHub milestones and issues. Dates are targets, not promises.

## Contributing

The project is at a very early stage and the structure is still being decided. Issues and suggestions are welcome; please open an issue before sending a pull request.

## License

[MIT](LICENSE)
