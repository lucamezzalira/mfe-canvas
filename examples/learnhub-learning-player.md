# Micro-Frontend Canvas

> One micro-frontend, one canvas. This document captures the key architectural and implementation decisions for this micro-frontend. Create new iterations rather than overwriting previous versions.
>
> _Micro-Frontend Canvas v1.0 — © Luca Mezzalira — CC BY-NC 4.0_
> _https://buildingmicrofrontends.com_

---

## System Details

| Field | Value |
|-------|-------|
| **Software System** | LearnHub E-learning Platform |
| **Owned by Team** | Team Learning Experience |
| **Canvas Date** | 12th April 2026 |
| **Iteration** | 1 |

---

## Micro-Frontend Description

_Name and brief description: what does this MFE do? What user journey or business capability does it enable?_

> Learning Player: Enables learners to consume course content through video playback with synchronised lesson notes. Covers the core learning experience from lesson selection through video viewing, note-taking, and progress tracking within a single course.

---

## Split Strategy

_Horizontal split (multiple MFEs in the same view) or Vertical split (one MFE per view or group of views)?_

> Vertical split: Owns all /course/:courseId/lesson/* routes exclusively. The video player and lesson notes are composed within this MFE, not as separate micro-frontends, because they share tight interaction patterns (timestamp sync, scroll-to-note, note-at-timestamp).

---

## Boundaries Validation

_Verify the micro-frontend has minimal API surface, is context-aware and self-contained, coarse-grained in nature, and can be deployed independently._

> - Receives only `courseId`, `lessonId`, and `userId` props
> - Fetches all lesson content, video URLs, and notes independently via REST APIs
> - Represents complete learning experience (video + notes + progress) as a single cohesive unit
> - Deployed independently via dedicated CI/CD pipeline
> - Video player and notes kept inside this MFE (splitting them would create tight coupling between two separate MFEs sharing playback state)

---

## Dependencies

_External dependencies such as shared libraries, application shell, or APIs._

> **Shell Provides:**
> - React 18.3, React Router 6.x, auth context, event emitter
>
> **APIs:**
> - Content Delivery API v2 (lesson metadata, notes, curriculum structure)
> - Video Streaming Service v1 (signed URLs, adaptive bitrate manifests)
> - Progress Tracking API v1 (completion status, resume position, bookmarks)
>
> **Libraries:**
> - Design System v2.3
> - Video.js v8 (player framework)
> - Markdown renderer (for lesson notes)

---

## Communication Methodology

_How data flows into and out of the micro-frontend, including contracts and versioning._

**Inputs:**

> - `courseId` from route parameters
> - `lessonId` from route parameters
> - `userId` from auth context
> - Auth token from HTTP-only cookie (managed by shell)

**Outputs:**

> - `lesson:started` event (with courseId, lessonId, timestamp)
> - `lesson:completed` event (with courseId, lessonId, watchTimeSeconds, completionPercentage)
> - `lesson:progress` event (with courseId, lessonId, currentPosition, notesCount) emitted every 30 seconds
> - `course:completed` event (with courseId, totalLessons, completionDate) when final lesson finishes
>
> All events emitted via shell's event emitter.

**Contracts & Versioning:**

> - Contracts versioned using semantic versioning
> - Catalog team consumes `lesson:completed` to update course progress indicators
> - Certification team consumes `course:completed` to trigger certificate generation
> - Breaking changes require 3-sprint deprecation window (longer than default due to downstream dependencies)

---

## Organisational Constraints

_Any organisational requirement that limits the freedom of decision (budget, timeline, compliance, team size, mandated tools)._

> - Q2 2026 delivery for summer course launch campaign
> - WCAG 2.1 AA compliance required (accessibility is a legal requirement for educational content)
> - GDPR compliance for learner progress data and note storage
> - 4-engineer team limit
> - Must use existing video hosting provider (Mux)
> - Content team requires 48-hour SLA for new lesson availability after upload

---

## Technical Constraints

_Any technical requirement that restricts the freedom of decision (stack, infrastructure, architectural patterns, SLAs)._

> - React 18+ and TypeScript 5+
> - AWS CloudFront for static assets, Mux for video delivery
> - 400KB bundle limit (gzipped), excluding Video.js (loaded separately)
> - Video.js loaded via dynamic import on route entry
> - Offline-capable: lesson notes must work without network (service worker caching)
> - 3-second response SLA for lesson load (including video player initialisation)
> - CSP headers mandatory (strict policy for embedded video content)

---

## Composition Context

_How is this micro-frontend composed and integrated into the application (e.g., CSR or SSR, Module Federation, transclusion, web components)?_

> - Client-side rendering with Module Federation v2
> - Loaded by application shell on /course/:courseId/lesson/* navigation
> - Runs as standalone React app within shell container
> - Video.js initialised after MFE mount to avoid blocking first paint
> - Lesson notes rendered from Markdown served by Content Delivery API

---

## Governance & Observability

_How is this MFE monitored, who responds to incidents, and what shared standards are enforced?_

**Observability:**

> - RUM monitoring tracks lesson load time, video start time, buffering events, and note rendering
> - Key metrics: time-to-first-frame (video), lesson load time (p50/p95), error rate, completion rate
> - Video playback errors tracked separately (Mux data integration)
> - Alerts trigger when video start time exceeds 5 seconds or error rate exceeds 0.5%

**Incident Response:**

> - Learning Player MFE owned by Team Learning Experience (primary on-call)
> - Video streaming issues escalated to Mux support (external dependency)
> - Shell/Platform team owns composite application health
> - Escalation: Team Learning Experience ? Platform Lead ? CTO
> - All incidents logged in shared channel; post-mortems within 48 hours

**Shared Standards:**

> - All MFEs must maintain: Lighthouse >85, bundle <400KB (excl. video player), error rate <0.5%
> - WCAG 2.1 AA compliance verified via automated accessibility tests in CI
> - CI/CD gates enforce automatically; violations block deployment
> - Design System v2.3 required; v1.x fully deprecated

---

## Quality Guardrails

_Define the three most important guardrails to implement (e.g., bundle size limits, performance budgets, accessibility standards)._

1. Bundle max 400KB gzipped (excluding Video.js). Video.js loaded via dynamic import. Code-split lesson notes from video player.
2. WCAG 2.1 AA compliance: keyboard navigation for video controls, captions support, screen reader compatibility for notes. Automated axe-core tests in CI.
3. Lighthouse >85, time-to-first-frame <3s, lesson load time <2s. Synthetic monitoring in production for key learning paths.

---

## Challenges & Risks

_Identify current challenges and risks: organisational, architectural, and technical. Include mitigation strategies._

| Challenge | Type | Mitigation |
|-----------|------|------------|
| Catalog team and Learning team both claim ownership of lesson metadata, creating unclear responsibility | Org | Define explicit data ownership: Catalog owns curriculum structure, Learning owns playback and progress |
| Video player and lesson notes share playback state tightly, tempting future teams to split them into separate MFEs | Arch | Document in this canvas why they're kept together; add architectural fitness function to prevent splitting |
| Certification team depends on `course:completed` event but has different release cadence | Org | 3-sprint deprecation window for contract changes; consumer-driven contract tests |
| Mux video hosting is a single external dependency for core business capability | Arch | Implement fallback UI when Mux is unavailable; monitor Mux status page; evaluate backup provider annually |
| Offline notes capability adds service worker complexity that could conflict with shell's caching strategy | Tech | Coordinate cache namespaces with platform team; test offline scenarios in CI |
| Accessibility compliance is legally required but team has limited accessibility expertise | Org | Invest in accessibility training; integrate automated a11y testing; schedule quarterly manual audit |

---

## Canvas History

_Track iterations and key changes over time._

| Iteration | Date | Key Changes |
|-----------|------|-------------|
| 1 | 12th April 2026 | Initial canvas |
| | | |
