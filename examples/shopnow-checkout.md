# Micro-Frontend Canvas — ShopNow Checkout

> Filled example from the ShopNow e-commerce case study in the [Micro-Frontend Canvas PDF](../micro-frontend-canvas.pdf).
>
> _Micro-Frontend Canvas v1.0 — © Luca Mezzalira — CC BY-NC 4.0_

---

## System Details

| Field | Value |
|-------|-------|
| Software System | ShopNow e-commerce |
| Owned by Team | Checkout Team |
| Canvas Date | 30th March 2026 |
| Iteration | 1 |

---

## Micro-Frontend Description

_Checkout Flow — handles complete order submission from cart review to confirmation. Includes order summary, shipping selection, payment processing, and confirmation screens._

> Enables the end-to-end checkout user journey after a customer leaves the cart.

---

## Split Strategy

_Vertical split — owns all `/checkout/*` routes exclusively._

> No other micro-frontend shares the checkout view. Coordination with other teams happens through events and APIs, not shared UI composition.

---

## Boundaries Validation

_Receives only `userId` and `cartId` props. Fetches checkout data via GraphQL APIs. Represents the complete checkout flow. Deployed independently via a dedicated CI/CD pipeline._

> Minimal API surface (2 props). Self-contained data fetching. Coarse-grained boundary covering the full checkout capability. Independent deployment without cross-team coordination.

---

## Dependencies

**Application shell:** React 18.2, React Router 6.x, auth context, event emitter.

**APIs:** Payment Gateway v3, Shipping Service v2, Discount Service v1.

**Shared libraries:** Design System v4.1, Stripe SDK v3.

---

## Communication Methodology

**Inputs:**

> - `cartId` from route params
> - Auth token from cookie

**Outputs:**

> Emits `checkout:started`, `checkout:completed` (with `orderId`, `orderTotal`), and `checkout:abandoned` events via the shell's event emitter.

**Contracts & Versioning:**

> Shell deployment coordination requires semantic versioning. Event payloads versioned alongside shell releases.

---

## Organisational Constraints

> - Q1 2026 delivery deadline for peak season
> - PCI-DSS Level 1 compliance required
> - AWS-only infrastructure
> - 5-engineer team limit
> - Must use existing payment gateway
> - EU legal review required for terms display

---

## Technical Constraints

> - React 18+ and TypeScript 5+
> - AWS CloudFront/S3 deployment
> - 300 KB bundle limit
> - OAuth 2.0 auth via shell
> - BFF pattern for APIs
> - 2-second response SLA
> - CSP headers mandatory

---

## Composition Context

_Client-side rendering with Module Federation v2. Loaded by the application shell on `/checkout` navigation. Runs as a standalone React app within the shell container._

---

## Governance & Observability

**Observability:**

> RUM monitoring in production. Track Lighthouse score (> 90), LCP (< 2.5s), FID (< 100ms), and CLS (< 0.1).

**Incident Response:**

> Checkout Team owns on-call for checkout-related production incidents.

**Shared Standards:**

> Company-wide Design System v4.1. Shared shell auth and event emitter contracts.

---

## Quality Guardrails

1. Bundle max 300 KB with code-splitting by route. Lazy-load Stripe SDK on the payment step.
2. Lighthouse > 90, LCP < 2.5s, FID < 100ms, CLS < 0.1. RUM monitoring in production.
3. ArchUnit tests enforce no circular dependencies, validate BFF pattern, and verify module boundaries.

---

## Challenges & Risks

| Challenge | Type | Mitigation |
|-----------|------|------------|
| Hidden coupling on cart pricing logic | Arch | Define explicit pricing contract with Cart team; avoid direct cart module imports |
| Stripe SDK 85 KB impacts bundle budget | Tech | Lazy-load Stripe SDK on payment step only |
| Payment Gateway 95% SLA | Tech | Retry logic and fallback payment flow |
| Shell deployment coordination | Arch | Semantic versioning for shell and MFE releases |
| Q2 PCI audit may require architecture changes | Org | Schedule pre-audit architecture review in Q1 |
| Third-party address validation reliability | Tech | Circuit breaker around validation service |
| 10x holiday traffic spike | Tech | Load testing before peak season |

---

## Canvas History

| Iteration | Date | Key Changes |
|-----------|------|-------------|
| 1 | 30th March 2026 | Initial canvas from half-day workshop |
