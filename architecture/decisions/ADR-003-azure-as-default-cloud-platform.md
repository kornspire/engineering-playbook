# ADR-003: Azure as Default Cloud Platform

**Status:** Accepted  
**Date:** 2026-09-23  
**Applies to:** Greenfield projects where the client has no existing cloud investment

---

## Context

Most of the projects Kornspire undertakes involve deploying and operating infrastructure. When a client has no existing cloud platform, we need a default recommendation. That recommendation should reflect honest evaluation, not vendor relationships.

This ADR documents why Azure is our default for new Indian clients, and where it is not the right choice.

---

## Options considered

**Amazon Web Services (AWS)**  
The largest public cloud by market share and service breadth. The best choice when the client has existing AWS investment, when the workload requires a service category where AWS is clearly stronger (e.g. SageMaker for ML pipelines, Kinesis for high-throughput streaming), or when the client's team has deep AWS expertise. AWS's India regions (Mumbai, Hyderabad) are mature. The breadth of services is also a complexity risk: AWS has more ways to accidentally do the same thing, and the IAM model has a steeper learning curve for small teams.

**Google Cloud Platform (GCP)**  
The strongest choice for workloads that are primarily machine learning or data warehousing (BigQuery, Vertex AI are genuinely ahead of AWS and Azure equivalents at the time of this writing). The `Cloud Run` managed container service is operationally simpler than equivalent managed Kubernetes. The weakest of the three for enterprise sales cycles and compliance documentation in the Indian market.

**Microsoft Azure**  
Strong India region coverage (Central India — Pune, South India — Chennai). The compliance story for Indian enterprises — particularly banking, healthcare, and government-adjacent clients — is more developed than GCP's. Azure Active Directory / Entra ID is the enterprise identity standard in India. Azure Static Web Apps provides a genuinely good deployment target for JAMstack applications. The managed database services (PostgreSQL Flexible Server, Azure SQL) are competitive with equivalents on other platforms.

**Self-hosted / VPS**  
Appropriate for cost-sensitive projects where managed services are not justified. DigitalOcean, Hetzner, or equivalent are viable for small workloads. The operational cost (patching, monitoring, backup management) moves to the engineering team; this is acceptable only when the client understands and accepts that trade-off.

---

## Decision

**Azure** as the default for new client projects in India, for the following reasons:

1. **Data residency.** The Central India region (Pune) and South India region (Chennai) allow Indian client data to stay in India with no architectural gymnastics. This matters for clients in regulated industries and increasingly for clients whose customers ask about it.

2. **Enterprise identity integration.** Most Indian enterprises run Microsoft Active Directory or have Microsoft 365. Azure Entra ID (formerly Azure AD) integrates with both without additional identity federation infrastructure.

3. **Static Web Apps.** For JAMstack and Next.js applications, Azure Static Web Apps provides globally distributed hosting, CI/CD integration, preview environments per pull request, and custom domain SSL — all managed. The alternative is assembling the same capability from separate services.

4. **Familiar compliance documentation.** Azure's compliance centre has ISO 27001, SOC 2, and India-specific certifications documented and auditable. For clients whose procurement requires vendor compliance evidence, this reduces the sales cycle.

---

## Known trade-offs

- **Azure's ML/AI services** are behind GCP's at the time of this writing. Projects with significant ML workloads should evaluate GCP or consider AWS SageMaker.
- **Azure's developer experience** for non-Microsoft-stack applications (Linux containers, Python, Node.js) has improved substantially but still lags AWS in some areas. Documentation quality is uneven.
- **Azure costs** for compute-heavy workloads are not consistently lower than AWS or GCP; always estimate.
- **Azure Static Web Apps** does not support host-based routing rules in its configuration file. www-to-apex redirects must be handled in the application layer, not the platform layer.

---

## When to choose differently

| Situation | Recommendation |
|---|---|
| Client has existing AWS infrastructure and team | AWS — do not fragment the client's estate |
| Primary workload is ML pipelines or large-scale analytics | GCP (BigQuery, Vertex AI) or AWS (SageMaker) |
| Cost-sensitive project, small workload, no enterprise compliance requirement | Self-hosted VPS |
| Client is a Microsoft-stack enterprise in India | Azure — this is the natural fit |
| Startup building for global audience, no regional constraint | AWS (broadest ecosystem) or Vercel/Cloudflare for edge-first |
