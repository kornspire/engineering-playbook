# Technology Radar

This radar documents Kornspire's current assessment of technologies relevant to the work we do. It is updated when our assessment changes, not on a fixed schedule.

**Adopt** — We recommend this for new projects where it fits. We use it ourselves.  
**Trial** — We are actively evaluating this. We will use it on selected engagements; not yet a default recommendation.  
**Assess** — Worth understanding. Not yet used in production, but we are watching it.  
**Hold** — We advise against using this for new work. Existing usage should have a migration plan.

A technology on this radar is assessed for the type of work Kornspire does. A technology in **Hold** for new web applications may be entirely appropriate in a client's existing Python monolith.

---

## Languages and runtimes

| Technology | Status | Notes |
|---|---|---|
| TypeScript | **Adopt** | Default for all JavaScript-based work. The type system catches a class of bugs at compile time that are expensive to find at runtime. |
| Python | **Adopt** | Default for data engineering, ML pipelines, scripting, and backend services where the client's team has Python expertise or the ecosystem (NumPy, pandas, scikit-learn) is required. |
| Node.js 22 LTS | **Adopt** | Current LTS. Strong for API servers and tooling. |
| Go | **Trial** | Well-suited to CLI tools, proxies, and services where low resource consumption matters. Small ecosystem footprint and single-binary deployment are genuine advantages. Not a default recommendation for web APIs where TypeScript/Python ecosystems have better support. |
| Bun | **Trial** | Drop-in Node.js replacement with faster startup and built-in bundler. We are evaluating for tooling and serverless use cases. Not yet recommended for production servers. |
| Java / Kotlin | **Assess** | Relevant when the client's existing codebase is JVM-based. Not our default for new work. |
| PHP | **Hold** | We will work in existing PHP codebases. We will not start new PHP projects. The ecosystem has strong options (Laravel, Symfony) but we do not have deep expertise and the default security surface is historically poor. |

---

## Frontend and web

| Technology | Status | Notes |
|---|---|---|
| Next.js (App Router) | **Adopt** | See [ADR-002](../architecture/decisions/ADR-002-nextjs-for-content-driven-web.md). |
| React | **Adopt** | Default UI library for interactive applications. Large talent pool. |
| Tailwind CSS | **Adopt** | Utility-first CSS for component-level styling. Pairs well with component libraries and design systems. |
| Astro | **Trial** | Strong choice for purely static sites and documentation. See [ADR-002](../architecture/decisions/ADR-002-nextjs-for-content-driven-web.md) for when to prefer it over Next.js. |
| Remix | **Trial** | Preferred over Next.js when the application is mutation-heavy and most pages require fresh server data. |
| Vue 3 | **Assess** | Viable alternative to React. We will use it on engagements where the client's team has Vue expertise. Not our default. |
| jQuery | **Hold** | Acceptable only in legacy codebases. Do not introduce to new projects. |
| Class-based React components | **Hold** | Functional components with hooks are the current standard. Class components should be migrated on contact. |

---

## Databases

| Technology | Status | Notes |
|---|---|---|
| PostgreSQL | **Adopt** | Default relational database for new projects. Strong JSON support, mature extension ecosystem (pgvector for embeddings, TimescaleDB for time series), excellent managed service availability on all major clouds. |
| Redis | **Adopt** | Default for caching, session storage, rate limiting, and pub/sub. Not a primary datastore. |
| SQLite | **Adopt** | Appropriate for embedded applications, CLI tools, and single-server deployments where operational simplicity matters more than horizontal scale. |
| MongoDB | **Assess** | Appropriate when the data model is genuinely document-shaped and schema flexibility is required. Not a substitute for a relational database. The "just use MongoDB" pattern has produced more data integrity problems than it has solved. |
| MySQL / MariaDB | **Assess** | We will work in existing MySQL deployments. For new projects, PostgreSQL has a superset of MySQL's capabilities. |
| Elasticsearch / OpenSearch | **Assess** | Appropriate for full-text search at scale. Operationally complex; evaluate whether PostgreSQL full-text search covers the requirement first. |

---

## Cloud and infrastructure

| Technology | Status | Notes |
|---|---|---|
| Azure | **Adopt** | Default for Indian clients. See [ADR-003](../architecture/decisions/ADR-003-azure-as-default-cloud-platform.md). |
| Azure Static Web Apps | **Adopt** | Default deployment target for Next.js and JAMstack applications. |
| Azure Container Apps | **Adopt** | Managed container hosting without Kubernetes operational overhead. Default for containerised services that need autoscaling. |
| Docker | **Adopt** | Default packaging for server-side applications. |
| AWS | **Adopt** | Used when the client has existing AWS infrastructure. See [ADR-003](../architecture/decisions/ADR-003-azure-as-default-cloud-platform.md) for the decision framework. |
| GitHub Actions | **Adopt** | Default CI/CD. Tight integration with source control, reasonable free tier, large action ecosystem. |
| Kubernetes (self-managed) | **Hold** | The operational overhead of self-managed Kubernetes is not justified for most project scales. Use a managed service (Azure Container Apps, AWS ECS, GCP Cloud Run) unless the client has an existing Kubernetes platform and the team to operate it. |
| Serverless functions (cold-start-sensitive paths) | **Hold** | Serverless is appropriate for event-driven, low-frequency workloads. Using it for latency-sensitive user-facing APIs without understanding cold start behaviour produces unpredictable performance. |

---

## Testing and quality

| Technology | Status | Notes |
|---|---|---|
| Vitest | **Adopt** | Default test runner for TypeScript/Node projects. Fast, compatible with Jest's API, native ESM. |
| Playwright | **Adopt** | Default for end-to-end browser testing. |
| pytest | **Adopt** | Default test framework for Python projects. |
| Jest | **Trial** | Acceptable for projects already using it. New TypeScript projects prefer Vitest. |

---

## AI and machine learning

| Technology | Status | Notes |
|---|---|---|
| OpenAI API / Azure OpenAI | **Adopt** | Default for LLM-based features. Azure OpenAI is preferred for clients with data residency requirements. |
| pgvector | **Adopt** | Vector similarity search within PostgreSQL. Preferred over a standalone vector database for workloads that do not require billion-scale similarity search. |
| Hugging Face transformers | **Trial** | For workloads requiring self-hosted models or fine-tuning. Operationally heavier than API-based approaches. |
| LangChain | **Hold** | The abstraction layer adds complexity without proportional benefit for most use cases. We prefer direct API calls with explicit prompt management. |

---

*Last updated: 2026-10-09*
