# CODEBUDDY.md This file provides guidance to WorkBuddy when working with code in this repository.

## Project Overview

**FastenerFlow** is an open-source multi-language toolkit for the fastener industry, focused on spec lookup, standard conversion, and terminology translation. The repository is in early development and evolves iteratively.

The broader product vision (documented in `docs/`) is to build **COUPLER (扣环)** — an independent full-chain digital platform for the fastener industry covering: standard library, engineering tools, product catalog, RFQ/trading, custom manufacturing, supplier management, quality traceability, inventory management, and user/permission management.

---

## Architecture

### Existing Production System (reference only — lives outside this repo)

The current working system has two parts:

**Backend** (`/Users/wbtrex/website/localServer/node/edtib/interface/standards`)
- Node.js + RPC-style routing + `knex` ORM against MySQL
- Multi-process via `cluster`, JWT/token middleware
- Core data models: `products`, `formulas`, `variables`, `shapes`, `categories`, `standards`, `interpretations`
- Key fields powering engineering calculations:
  - `products.parameters` (JSON): thread specs, head dimensions — primary inputs for calculators
  - `products.grade` (String): strength class (4.8/6.8/8.8/10.9/12.9) — branches strength calc logic
  - `products.diameter_length` (JSON): spec×length matrix — drives thousand-piece weight calculator
  - `products.formulas` (JSON): formula reference list — pre-wires product↔formula associations
  - `formulas.columnar` (String): formula expression strings parseable by `mathjs`
  - `variables.variable` (String): symbol dictionary (d, p, As, …) — maps formula symbols to values
- API namespace: `/front/standards/*`

**Frontend** (`/Users/wbtrex/website/localServer/node/edtib/client/books/web`)
- Vue 3 + TypeScript + Element Plus + Vite + PWA
- Communicates with backend exclusively via `/front/standards/*` endpoints

### New System: COUPLER (planned, to be built in this repo)

Planned tech stack: **Vue 3 + Fastify + Prisma + MySQL 8 + Redis + Meilisearch**

Module layout (target architecture):
```
/modules/standard     — spec library, multi-standard full-text search
/modules/product      — supplier product catalog, SKU management
/modules/rfq          — RFQ creation, quote management, negotiation
/modules/order        — order lifecycle, payment, logistics tracking
/modules/user         — enterprise accounts, role/permission system
/modules/notify       — async notifications (BullMQ + Redis)
/modules/calculator   — engineering tools: weight, strength, torque
/modules/quality      — inspection records, batch traceability
/modules/inventory    — warehouse stock, reorder alerts
/modules/custom       — custom manufacturing orders, drawing uploads
```

MVP deploys as a **single modular monolith** (one Node.js app, one DB). Microservice extraction happens later, driven by actual load pressure (search first, then trading).

### Database Design Principles

Refer to `docs/05_new-system-requirements-architecture.md` §6 for full E-R design. Key decisions:
- `FastenerSpec.dimensions: JSON` — fastener parameters vary too much by type for rigid columns
- `Product.price_tiers: JSON` — tiered pricing without extra join tables
- `Order.items: JSON` — snapshot at order-time to prevent retroactive price changes
- `QualityRecord.batch_no` — batch-level traceability at minimum cost
- `standard_body: ENUM(GB, ISO, DIN, ANSI, JIS)` — standards are first-class entities, not tags

---

## Key Business Flows

Three primary flows define the platform — any new feature should map cleanly into one:

1. **Standard procurement loop**: spec search → RFQ → quote → order → logistics → QC accept
2. **Custom manufacturing loop**: drawing upload → supplier matching → competitive quote → sample → batch order
3. **Supplier operations loop**: onboarding → product catalog → receive RFQ → quote → ship → collect payment

---

## MVP Scope (Phase 1, 0–90 days)

Must-have for first shipped version (see `docs/05_new-system-requirements-architecture.md` §8–9):
- Standard library search (GB/ISO/DIN, spec parameter visualization)
- Supplier product publishing
- RFQ + quote management
- Order creation and tracking
- Enterprise user registration/login with buyer/supplier roles
- Basic online payment

P1 (ship if time allows): 2 engineering calculators (thousand-piece weight + strength checker), mobile H5.

---

## Engineering Calculator Priority

The fastest-to-build, highest-value tools (reusing existing data assets):

| Tool | Data asset reused |
|------|-------------------|
| Thousand-piece weight calculator | `diameter_length` + `formulas.columnar` |
| Strength checker | `parameters` + `grade` + formula expressions |
| Assembly torque calculator | `parameters` + friction coefficient variables |
| Spec quick-lookup + compare | existing standard query system |

PDF export of calculation reports is the **primary paid feature trigger** — implement before open launch.

---

## Product Commercialization Context

Documented in `docs/04_product-market-strategy.md`. The SaaS calculator product (**螺定 SUREDIM**) is the first revenue vehicle, while COUPLER is the longer-term platform play. Key pricing: Personal ¥199/yr, Professional ¥499/yr, Enterprise ¥2,999/yr (10 seats + API).

---

## Documents Reference

| File | Contents |
|------|----------|
| `docs/01_fastener-toolset-technical-spec.md` | Engineering toolset technical specification |
| `docs/02_pfma-system-feature-list.md` | PFMA system feature list (post-MVP) |
| `docs/03_industry-service-roadmap.md` | Three-phase service roadmap |
| `docs/04_product-market-strategy.md` | Market strategy and commercialization analysis |
| `docs/05_new-system-requirements-architecture.md` | COUPLER full requirements and architecture |
