# ShelfMates

A shared fridge app for flatmates, couples and families. Track what's in the fridge, who added it and when it expires, and invite others to join with a code.

> **Status:** early development. See [the roadmap](#roadmap).

## Features (planned)

- **Accounts** - register and log in with a cookie session; passwords hashed with Argon2id.
- **Shared fridges** - create a fridge, invite others with an expiring code, owner/member roles.
- **Items** - name, quantity and unit, expiry date, and who added it.
- **Item lifecycle** - mark items as consumed or thrown away to keep a history and see food waste.

## Tech stack

| Area | Technology |
|---|---|
| API | ASP.NET Core Web API (.NET 10), controllers |
| Data | Entity Framework Core (code-first migrations), PostgreSQL 17 |
| Auth | Cookie authentication, Argon2id password hashing |
| API docs | OpenAPI + [Scalar](https://github.com/scalar/scalar) |
| Local infra | Docker Compose |

## Architecture

The solution is moving to a layered (Clean Architecture-style) structure:

```
src/
  ShelfMates.Domain/          Entities and enums, no dependencies
  ShelfMates.Application/     Services, request/response contracts, abstractions
  ShelfMates.Infrastructure/  EF Core DbContext, configurations, migrations, password hashing
  ShelfMates.Api/             Controllers, authentication, exception handling, startup
```

- Application defines the abstractions it needs, such as `IShelfMatesDbContext`, and Infrastructure implements them.
- There's no generic repository or unit-of-work layer, because EF Core's `DbContext` already does that job.

## Getting started

### Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)

### Run locally

```bash
# 1. Start PostgreSQL (port 5433 on the host)
docker compose up -d

# 2. Run the API
dotnet run --project src/ShelfMates.Api
```

In Development, the root URL redirects to the Scalar API reference at `/scalar/v1`.

| Launch profile | URLs | Notes |
|---|---|---|
| `http` (default for `dotnet run`) | <http://localhost:5196> | |
| `https` | <https://localhost:7007>, <http://localhost:5196> | HTTP requests are redirected to HTTPS |

To use the `https` profile, run `dotnet run --project src/ShelfMates.Api --launch-profile https`. Trust the .NET development certificate once first with `dotnet dev-certs https --trust`.

### Local database

| Setting | Value |
|---|---|
| Host | `localhost` |
| Port | `5433` |
| Database / user / password | `shelfmates` |

These credentials are for local development only. Stop the database with `docker compose down`. Use `docker compose down -v` to also delete its data.

## Roadmap

- [x] API project with OpenAPI + Scalar
- [x] PostgreSQL via Docker Compose
- [ ] Layered solution structure
- [ ] Users: registration, login and logout with cookie auth
- [ ] Fridges and memberships (owner/member roles)
- [ ] Items with expiry tracking and consumed / thrown-away status
- [ ] Fridge invites with expiring codes
- [ ] Integration tests with Testcontainers
- [ ] CI with GitHub Actions
- [ ] Frontend
