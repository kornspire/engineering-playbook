# ADR-002: Next.js for Content-Driven Web Applications

**Status:** Accepted  
**Date:** 2026-09-23  
**Applies to:** Marketing sites, product pages, documentation sites, and web applications with substantial static content and moderate interactivity

---

## Context

When building web applications where the primary job is delivering content — marketing sites, product pages, documentation, portals with authenticated sections — we need a framework that handles static generation well, has a first-class TypeScript story, deploys cleanly to modern hosting, and is maintained by a community large enough to provide long-term security patches and ecosystem support.

This ADR documents the decision made for kornspire.com and the class of projects it represents.

---

## Options considered

**Astro**  
Purpose-built for content sites. Excellent static output, minimal JavaScript by default, supports multiple component frameworks. The island architecture is well-suited to mostly-static pages with isolated interactive components. Strong fit for content-only sites. Weaker fit when a project grows to include authenticated sections, API routes, or server-side personalisation — those use cases require adding adapters that reintroduce complexity Astro deliberately avoids.

**Remix**  
Excellent for applications where most pages require server-side data and mutations are frequent. The loader/action model is clean and explicit. Less suitable for sites where the majority of pages are static and the main concern is build-time optimisation and CDN distribution.

**Gatsby**  
Strong static generation story but increasingly abandoned by its maintainer. The GraphQL data layer adds conceptual overhead for projects that do not need it. Long build times on large sites. Not recommended for new projects.

**Next.js (App Router)**  
Static generation and server-side rendering in the same framework, per-page. React Server Components reduce client-side JavaScript for static-heavy pages. First-class TypeScript. Large ecosystem and community. Azure Static Web Apps, Vercel, and most cloud platforms support it natively. The App Router introduced significant API changes from the Pages Router; the ecosystem has largely caught up.

**Plain HTML + vanilla JS**  
Always a valid option for truly static sites with no dynamic data. Zero framework overhead. Becomes difficult to maintain as the number of pages grows and content needs to be shared across pages.

---

## Decision

**Next.js with the App Router** for content-driven web applications.

**Reasons:**

1. **Hybrid rendering per route.** A marketing site can have fully static pages alongside API routes, server-side personalised sections, and client components — all in one deployment. The framework decides the rendering strategy at build time based on what each route needs. This avoids the architectural fork where a growing content site has to adopt a second framework for its dynamic features.

2. **`generateStaticParams` + `dynamicParams = false` for type-safe, SEO-clean routes.** Dynamic routes generate known paths at build time. Requests for unknown paths return 404 rather than attempting a server render. This makes the URL space explicit and prevents crawlers from indexing stub pages.

3. **TypeScript-first.** The `next.config.ts` API, typed metadata, typed route params, and typed server actions eliminate an entire class of runtime errors.

4. **Deployment targets.** Azure Static Web Apps, Vercel, and self-hosted Node all support Next.js. A project started on Azure can be migrated to self-hosted without a framework change.

---

## Known trade-offs

- **App Router complexity.** The distinction between Server Components and Client Components, and the rules around data fetching in each, has a learning curve. Engineers new to the App Router need time to internalise the mental model.
- **Faster-moving target.** Next.js releases breaking changes more often than a framework like Astro. `package.json` upgrades require more attention.
- **Heavier than necessary for simple static sites.** If a site is 100% static with no server components, no API routes, and no authentication, Astro would produce a smaller, faster output with less framework surface area.

---

## When to choose differently

- **Pure static documentation site with no server logic:** Astro or a static site generator
- **Application where every page requires server-side data and mutations are the primary interaction:** Remix
- **Client has an existing React application using the Pages Router:** stay on Pages Router, do not force a migration
