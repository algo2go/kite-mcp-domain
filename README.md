# kite-mcp-domain

[![Go Reference](https://pkg.go.dev/badge/github.com/algo2go/kite-mcp-domain.svg)](https://pkg.go.dev/github.com/algo2go/kite-mcp-domain)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Domain-Driven Design (DDD) value objects + entities for the algo2go
ecosystem. Defines the canonical shape of trading-domain types
(Money, Quantity, Order, Position, Holding, Profile, Session, Alert,
Family, Glossary) plus domain events (TierChangedEvent,
OrderPlacedEvent, etc.) and an EventDispatcher.

Used by [`Sundeepg98/kite-mcp-server`](https://github.com/Sundeepg98/kite-mcp-server)
across 165+ files for trading domain modeling — riskguard checks,
billing tier changes, audit projections, paper-trading engine,
order workflows, alert evaluations, etc.

## Why a separate module?

Domain types are the canonical interop layer between trading-domain
consumers (broker dashboards, monitoring, future broker adapters,
tier billing). Hosting as a module:

- Centralizes the trading-domain vocabulary across consumers
- Lets DDD entities + value objects version independently of
  application logic
- Pairs cleanly with `algo2go/kite-mcp-broker` (DTO interop) and
  `algo2go/kite-mcp-money` (Money type) for a coherent
  trading-types stack

## Stability promise

**v0.x — unstable.** Type signatures may evolve as DDD review
surfaces issues. Pin `v0.1.0` deliberately. v1.0 ships only after
the public type surface (entities + value objects + events) is
reviewed for stability and at least one external consumer ships
against it.

## Install

```bash
go get github.com/algo2go/kite-mcp-domain@v0.1.0
```

## Public API (selected highlights)

### Value objects
- `Money` — wraps `algo2go/kite-mcp-money.Money` with domain semantics
- `Quantity` — order size with sign + precision
- `Specs` — instrument lookup keys

### Entities
- `Order` — order aggregate with invariants (CanPlace, IsTerminal, ...)
- `Position` — open/closed positions with PnL projection
- `Holding` — long-term holdings with FIFO cost basis
- `Profile` — user profile with tier + region
- `Session` — auth session with IST-timezone expiry
- `Alert` / `CompositeAlert` — threshold + multi-condition alerts
- `Family` — family-mode subscription ledger

### Domain events
- `TierChangedEvent`, `OrderPlacedEvent`, `OrderCancelledEvent`,
  `OrderFilledEvent`, `AlertTriggeredEvent`, ...
- `EventDispatcher` for in-process pub/sub

## Dependencies

- `github.com/algo2go/kite-mcp-broker` — DTO interop (Order/Position/Holding/Profile)
- `github.com/algo2go/kite-mcp-isttz` — IST timezone helper for Session
- `github.com/algo2go/kite-mcp-money` — Money value object
- `github.com/stretchr/testify` — assertions

All deps are algo2go-published modules; no upstream `replace`
directives needed.

## Reference consumer

[`Sundeepg98/kite-mcp-server`](https://github.com/Sundeepg98/kite-mcp-server)
— consumed across:
- `kc/usecases/*.go` — every use case threads domain types
- `kc/eventsourcing/*.go` — aggregates project domain events
- `kc/riskguard/*.go` — checks operate on Order/Position
- `kc/billing/*.go` — TierChangedEvent + Money for tier accounting
- `kc/alerts/*.go` — Alert entity + AlertTriggeredEvent
- `kc/papertrading/*.go` — Order lifecycle in virtual portfolio
- `kc/audit/*.go` — domain-event projection for audit trail
- `mcp/trade/*.go`, `mcp/analytics/*.go` — domain types in tool args/returns

## License

MIT — see [LICENSE](LICENSE).

## Authors

Original DDD design + entity implementations: [Sundeepg98](https://github.com/Sundeepg98)
(Zerodha Tech). Multi-module promotion (2026-05-10): algo2go contributors.
