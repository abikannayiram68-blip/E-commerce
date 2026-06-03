File: /project-prompts/PHASES/PHASE-7/EXECUTION_STAGES.md

# PHASE 7 - Analytics, Reports & Non-Functional Hardening: Execution Stages

REQs: REQ-013, REQ-014, REQ-015, REQ-016, REQ-020, REQ-021, REQ-031

---

## STAGE 1 - Setup

**REQ-IDs**: REQ-013, REQ-016, REQ-020, REQ-021
**TEST-IDs**: TEST-013-UT-POS, TEST-016-UT-POS, TEST-020-UT-POS, TEST-021-UT-POS

**Tasks**:
- Set up load testing framework (k6 or Artillery)
- Configure automated Lighthouse CI audits
- Configure Istanbul/Jest coverage reporting thresholds (>=80%)

**TDD Flow**:
1. Write: coverage-config.test.ts - verify jest.config.js enforces 80% coverage
2. Run -> FAIL
3. Implement: Jest coverage thresholds
4. Run -> PASS
5. Refactor: update CI pipeline to fail on low coverage

---

## STAGE 2 - Architecture

**REQ-IDs**: REQ-013, REQ-014, REQ-015, REQ-031
**TEST-IDs**: TEST-013-UT-POS, TEST-014-UT-POS, TEST-015-UT-POS, TEST-031-UT-POS

**Tasks**:
- Define Analytics API contracts: GET /admin/analytics/sales, GET /admin/analytics/inventory
- Establish accessibility guidelines (WCAG 2.1 AA) for all UI components
- Explicitly enforce Single-Vendor architecture in analytics (no vendor filtering)

**TDD Flow**:
1. Write: analytics-types.test.ts - verify metrics payload shape (sales, orders count, low stock)
2. Run -> FAIL
3. Implement: Analytics types and schemas
4. Run -> PASS
5. Refactor: export shared types

---

## STAGE 3 - Database

**REQ-IDs**: REQ-013, REQ-016
**TEST-IDs**: TEST-013-IT-POS, TEST-016-IT-POS

**Tasks**:
- Create indexes for fast aggregation (e.g., Order(createdAt, status), OrderItem(productId))
- Create database views or optimized queries for daily/monthly sales aggregates

**TDD Flow**:
1. Write: analytics-query.test.ts - query monthly sales executes quickly
2. Run -> FAIL
3. Implement: optimized Sequelize groupBy query
4. Run -> PASS
5. Refactor: move complex aggregations to raw SQL if Sequelize is too slow

---

## STAGE 4 - Backend

**REQ-IDs**: REQ-013, REQ-016, REQ-020
**TEST-IDs**: TEST-013-UT-POS, TEST-013-IT-POS, TEST-016-UT-POS, TEST-016-UT-BOUNDARY, TEST-016-IT-POS, TEST-020-UT-POS, TEST-020-UT-BOUNDARY, TEST-020-IT-POS

**Tasks**:
- Implement analyticsService for KPI generation (Total Sales, Total Orders, Average Order Value)
- Implement caching layer (Redis) for analytics data (TTL: 1 hour) to protect DB
- Optimize all API endpoints to meet <300ms SLA
- Audit N+1 queries using Sequelize logging

**TDD Flow**:
1. Write: analytics-cache.test.ts - second request for analytics returns from cache immediately
2. Run -> FAIL
3. Implement: Redis caching wrapper around analyticsService
4. Run -> PASS
5. Refactor: extract generalized cache helper

---

## STAGE 5 - Frontend

**REQ-IDs**: REQ-013, REQ-014, REQ-015
**TEST-IDs**: TEST-013-E2E-POS, TEST-014-E2E-POS, TEST-015-E2E-POS

**Tasks**:
- Build Admin Analytics Dashboard (charts for sales, KPI cards)
- Perform full accessibility audit on frontend (keyboard navigation, aria labels, screen reader testing)
- Perform usability review (contrast checks >=4.5:1, intuitive flows)

**TDD Flow**:
1. Write: a11y-audit.test.tsx - run axe-core on major pages to check for violations
2. Run -> FAIL
3. Implement: add missing aria-labels, fix contrast colors in Tailwind config
4. Run -> PASS
5. Refactor: standardize color palette for contrast

---

## STAGE 6 - State

**REQ-IDs**: REQ-013
**TEST-IDs**: TEST-013-UT-POS

**Tasks**:
- Implement useAnalytics() hook
- Build chart components using Recharts or similar library

**TDD Flow**:
1. Write: Dashboard.test.tsx - renders KPI cards with fetched data
2. Run -> FAIL
3. Implement: Dashboard components
4. Run -> PASS
5. Refactor: split into smaller chart components

---

## STAGE 7 - Auth

**REQ-IDs**: REQ-013
**TEST-IDs**: TEST-013-IT-POS

**Tasks**:
- Ensure analytics endpoints are strictly protected by requireAdmin middleware

**TDD Flow**:
1. Write: analytics-auth.test.ts - verify 403 for normal users
2. Run -> FAIL
3. Implement: attach requireAdmin to analytics routes
4. Run -> PASS
5. Refactor: audit all admin routes

---

## STAGE 8 - Integration

**REQ-IDs**: REQ-013, REQ-014, REQ-015, REQ-016, REQ-020
**TEST-IDs**: TEST-013-IT-POS, TEST-014-IT-POS, TEST-015-IT-POS, TEST-016-IT-POS, TEST-020-IT-POS

**Tasks**:
- Run k6 load tests against API: target 100k simulated users / 50k products scale equivalent (or max staging limits)
- Run Lighthouse CI against frontend: ensure Performance, Accessibility, Best Practices, SEO scores are >= 90
- Ensure ESLint Airbnb rules are strictly enforced (0 errors, 0 warnings)

**TDD Flow**:
1. Write: load-test.js (k6) - script to hit /products and /search heavily
2. Run -> Measure baseline
3. Implement: necessary caching/DB indexes identified during test
4. Run -> Confirm SLA (<300ms) is met
5. Refactor: document scaling strategy

---

## STAGE 9 - Testing

**REQ-IDs**: All Phase 7 REQs
**TEST-IDs**: All Phase 7 TEST-IDs

**Tasks**:
- Run: pnpm test
- Run: pnpm test:integration
- Run: pnpm cypress:run --spec "analytics.cy.ts,a11y.cy.ts"
- Verify coverage >= 80% globally
- Fix all remaining failures

---

## STAGE 10 - Deployment

**REQ-IDs**: REQ-016, REQ-020, REQ-021
**TEST-IDs**: TEST-016-E2E-POS, TEST-020-E2E-POS, TEST-021-E2E-POS

**Tasks**:
- Final production deployment strategy and configuration
- Verify production environment matches staging architecture
- Review and finalize documentation (including MEMORY state updates)
- Final system sign-off against REQUIREMENTS.md
