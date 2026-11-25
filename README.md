# PayLink

PayLink is a year-work software engineering project: a bill-payment and digital-wallet backend for customers in Egypt. Customers can use a wallet to inquire about and pay electricity, water, mobile top-up, and internet bills through biller integrations or an agent.

The project focuses on safe money movement, clear payment status, modular biller integrations, automated tests, and a repeatable local deployment.

## Project status

**Selected stack:** Python 3, FastAPI, and PostgreSQL.

**Current milestone (Phase 1 completed):** Requirements pack, all six UML/ER design diagrams, prompt log, Docker & Docker Compose foundation (FastAPI health checks + PostgreSQL), and GitHub Actions CI workflow (lint, unit tests, and Docker image build) are in place.

## Features in scope

- Customer registration and verification levels.
- Wallet top-ups, available and held balances, and transaction history.
- A ledger as the source of wallet balance calculations.
- Billers and services with their required inquiry fields and payment limits.
- Bill inquiry, fee calculation, fraud checks, and wallet-funded bill payment.
- Idempotent payment requests and an explicit payment lifecycle.
- Biller adapters for different protocols and response styles.
- SMS and push notifications, including retry handling.
- Admin configuration, support payment search, audit logging, and reconciliation.
- Agent-assisted bill payments and commission tracking.

## Payment safety rules

- Use exact decimal types for money; do not use floating-point values.
- Record balance changes in the wallet ledger; derive balances from ledger entries.
- Hold the payment total before calling the biller.
- A live-biller timeout changes the payment to **Unknown**. Keep its funds held until evidence confirms success or failure; do not automatically refund it.
- Accept only the payment-state transitions defined in the requirements pack.
- A repeated payment request with the same idempotency key must not create a second charge.

## Biller integrations

The required integrations are:

| Biller | Service | Protocol / behavior |
|---|---|---|
| ElectroCo | Electricity | REST/JSON; immediate response |
| AquaCo | Water | SOAP-style XML over HTTP; slower response and bill-specific validation |
| TopUpNet | Mobile top-up | REST/JSON; asynchronous callback result |
| NetCo | Internet | Optional daily settlement-file flow |

## Design direction

The Phase 1 design targets a layered or hexagonal modular monolith. Planned module boundaries include Accounts and Wallet, Biller Catalog, Payment Processing, Biller Integration, Fees and Commissions, Notifications, Fraud Checks, and Reconciliation. The language/framework choice is Python 3 with FastAPI.

## Project documents

- [Requirements pack (Markdown)](docs/requirements.md) - problem summary, stakeholders, all functional requirements as user stories, Given/When/Then acceptance criteria, business rules, NFRs, technical constraints, and open questions.
- [Requirements pack (PDF)](output/pdf/paylink-requirements-pack.pdf) - report version of the requirements pack.
- [Design diagrams (PlantUML)](docs/design/) - editable sources for use-case, domain class, pay-a-bill sequence, refund-failed-payment sequence, payment state, and ER diagrams.
- [AI prompt log](docs/prompt-log.md) - record of AI prompts, tools/models, answer summaries, and the team's verdicts.

## Repository layout

```text
.
├── .github/
│   └── workflows/
│       └── ci.yml
├── docs/
│   ├── requirements.md
│   ├── prompt-log.md
│   └── design/
│       ├── domain-class-diagram.puml
│       ├── er-diagram.puml
│       ├── pay-bill-sequence.puml
│       ├── payment-state.puml
│       ├── refund-sequence.puml
│       └── use-case.puml
├── output/
│   └── pdf/
│       └── paylink-requirements-pack.pdf
├── tests/
│   └── test_main.py
├── .dockerignore
├── .env.example
├── .gitignore
├── compose.yaml
├── Dockerfile
├── main.py
├── README.md
└── requirements.txt
