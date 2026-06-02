# @dance-suite/database

Shared Prisma client and schema for the DanceSuite platform. Published to GitHub Packages — consume this in any DanceSuite service that needs database access.

## Installation

```bash
npm install @dance-suite/database
```

Requires a `.npmrc` pointing at the GitHub Packages registry:

```
@dance-suite:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=YOUR_GITHUB_TOKEN
```

## Usage

```ts
import { PrismaClient } from '@dance-suite/database'

const prisma = new PrismaClient()
```

All Prisma model types and enums are re-exported from the package root, so you don't need to import from `@prisma/client` directly.

```ts
import { competition, DancerLevel, HeatStatus } from '@dance-suite/database'
```

Set `DATABASE_URL` in your environment before instantiating the client:

```
DATABASE_URL=postgresql://user:password@host:5432/dbname
```

## Schema overview

Models are split into individual files under `prisma/schemas/`. The main entities are:

| Model | Description |
|---|---|
| `users` | Platform accounts — dancers, organizers, scrutineers, parents |
| `dancer_profile` | A dancer's competitive identity (name, level, age group, studio) |
| `competition` | A competition event with sections, entries, and program |
| `sections` | A category within a competition (e.g. Adult Gold Latin) |
| `competition_entry` | A dancer's registration for a competition |
| `heat` | A single heat within a section, with a start list and judging panel |
| `adjudicator` | A judge assigned to a competition, identified by letter |
| `panels` | A named group of adjudicators assigned to heats |
| `heat_marks` | Marks submitted by an adjudicator for a heat |
| `marks` | Individual per-dancer mark within a `heat_marks` submission |
| `heat_result` | Callbacks and positions determined after a heat |
| `section_result` | Final placement for each dancer in a section |

Key enums: `CompetitionStatus`, `SectionCategory`, `SectionEntryType`, `DancerLevel`, `DancerAgeGroup`, `HeatStatus`, `HeatType`.

## Local development

### Prerequisites

- Node.js 22+
- PostgreSQL (local or Docker)

### Setup

```bash
npm install
cp .env.example .env   # set DATABASE_URL
```

### Useful commands

```bash
# Validate all schema files
npx prisma validate

# Regenerate the Prisma client after schema changes
npx prisma generate

# Push schema changes to your local database (no migration file)
npx prisma db push

# Open Prisma Studio
npx prisma studio
```

Schema changes go in `prisma/schemas/` — each model has its own file. The root `prisma/schema.prisma` only contains the datasource and generator config.

## Releases

Releases are automated via semantic-release on push to `main` or `develop`.

| Branch | Release type | npm tag |
|---|---|---|
| `main` | Stable (`1.2.3`) | `@latest` |
| `develop` | Pre-release (`1.2.3-develop.1`) | `@develop` |

Commit messages must follow [Conventional Commits](https://www.conventionalcommits.org/) to trigger a release:

```
feat: add ticket model
fix: correct heat cascade delete
```

The CI pipeline (`.github/workflows/release.yml`) validates the schema, generates the client, and publishes to GitHub Packages. After a `develop` pre-release, a PR from `develop → main` is opened automatically.