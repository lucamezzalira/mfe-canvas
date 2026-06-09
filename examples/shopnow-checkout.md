# Micro-Frontend Canvas

> One micro-frontend, one canvas. This document captures the key architectural and implementation decisions for this micro-frontend. Create new iterations rather than overwriting previous versions.
>
> _Micro-Frontend Canvas v1.0 — © Luca Mezzalira — CC BY-NC 4.0_
> _https://buildingmicrofrontends.com_

---

## System Details

| Field | Value |
|-------|-------|
| **Software System** | ShopNow E-commerce |
| **Owned by Team** | Frosties Team |
| **Canvas Date** | 30th March 2026 |
| **Iteration** | 1 |

---

## Micro-Frontend Description

_Name and brief description: what does this MFE do? What user journey or business capability does it enable?_

> Checkout Flow: Handles complete order submission from cart review to confirmation. Includes order summary, shipping selection, payment processing, and confirmation screens.

---

## Split Strategy

_Horizontal split (multiple MFEs in the same view) or Vertical split (one MFE per view or group of views)?_

> Vertical split: Owns all /checkout/* routes exclusively.

---

## Boundaries Validation

_Verify the micro-frontend has minimal API surface, is context-aware and self-contained, coarse-grained in nature, and can be deployed independently._

> - Receives only `userId` and `cartId` props
> - Fetches checkout data independently via GraphQL APIs
> - Represents complete checkout flow (cart review to order confirmation)
> - Deployed independently via dedicated CI/CD pipeline

---

## Dependencies

_External dependencies such as shared libraries, application shell, or APIs._

> **Shell Provides:**
> - React 18.2, React Router 6.x, auth context, event emitter
>
> **APIs:**
> - Payment Gateway v3
> - Shipping Service v2
> - Discount Service v1
>
> **Libraries:**
> - Design System v4.1
> - Stripe SDK v3

---

## Communication Methodology

_How data flows into and out of the micro-frontend, including contracts and versioning._

**Inputs:**

> - `cartId` from route parameters
> - `userId` from auth context
> - Auth token from HTTP-only cookie (managed by shell)

**Outputs:**

> - `checkout:started` event (with cartId, userId, timestamp)
> - `checkout:completed` event (with orderId, orderTotal)
> - `checkout:abandoned` event (with cartId, reason)
>
> All events emitted via shell's event emitter.

**Contracts & Versioning:**

> - Contracts versioned using semantic versioning
> - Breaking changes require 2-sprint deprecation window
> - Cart team owns `cartId` contract; Payments team owns `checkout:completed`

---

## Organisational Constraints

_Any organisational requirement that limits the freedom of decision (budget, timeline, compliance, team size, mandated tools)._

> - Q1 2026 delivery deadline for peak season
> - PCI-DSS Level 1 compliance required
> - AWS-only infrastructure
> - 5-engineer team limit
> - Must use existing payment gateway
> - EU legal review required for terms display

---

## Technical Constraints

_Any technical requirement that restricts the freedom of decision (stack, infrastructure, architectural patterns, SLAs)._

> - React 18+ and TypeScript 5+
> - AWS CloudFront/S3 deployment
> - 300KB bundle limit (gzipped)
> - OAuth 2.0 auth via shell
> - BFF pattern for APIs
> - 2-second response SLA
> - CSP headers mandatory

---

## Composition Context

_How is this micro-frontend composed and integrated into the application (e.g., CSR or SSR, Module Federation, transclusion, web components)?_

> - Client-side rendering with Module Federation v2
> - Loaded by application shell on /checkout navigation
> - Runs as standalone React app within shell container

---

## Governance & Observability

_How is this MFE monitored, who responds to incidents, and what shared standards are enforced?_

**Observability:**

> - RUM monitoring via Datadog tracks all checkout events
> - Key metrics: response time (p50/p95/p99), error rate, abandonment rate
> - Distributed tracing correlates checkout with cart and payment MFEs
> - Alerts trigger when LCP exceeds 3 seconds or error rate exceeds 1%

**Incident Response:**

> - Checkout MFE owned by Frosties Team (primary on-call)
> - Shell/Platform team owns composite application health
> - Escalation: Frosties Team → Platform Lead → VP Engineering
> - All incidents logged in shared channel; post-mortems within 48 hours

**Shared Standards:**

> - All MFEs must maintain: Lighthouse >90, bundle <300KB, error rate <0.5%
> - CI/CD gates enforce automatically; violations block deployment
> - Design System v4.1 required; v3.x deprecated with 6-month notice

---

## Quality Guardrails

_Define the three most important guardrails to implement (e.g., bundle size limits, performance budgets, accessibility standards)._

1. Bundle max 300KB gzipped with code-splitting by route. Lazy load Stripe SDK on payment step only.
2. Lighthouse >90, LCP <2.5s, FID <100ms, CLS <0.1. RUM monitoring in production.
3. ArchUnit tests enforce no circular dependencies, validate BFF pattern, verify module boundaries.

---

## Challenges & Risks

_Identify current challenges and risks. Include mitigation strategies._

| Challenge | Mitigation |
|-----------|------------|
| Cart team owns pricing logic that checkout depends on, creating hidden coupling | Define explicit contract between teams; schedule regular sync |
| Shell team is a bottleneck for routing changes across 5 MFEs | Semantic versioning for shell API; consumer-driven contracts |
| No clear incident owner for composite checkout-to-payment flow | Define escalation path; platform team owns composite health |
| Design System v4.1 evolving faster than MFEs can adopt | 4-week notice for breaking changes; version pinning per MFE |
| Q2 PCI audit may require architecture changes mid-implementation | Document security decisions now; review with compliance early |
| Stripe SDK 85KB impacts bundle budget | Lazy load on payment step only; validate in CI |

---

## Canvas History

_Track iterations and key changes over time._

| Iteration | Date | Key Changes |
|-----------|------|-------------|
| 1 | 30th March 2026 | Initial canvas |
| | | |
