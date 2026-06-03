File: /project-prompts/PHASES/PHASES.md

# Project Phases

Each phase delivers usable, independently shippable value. No overlap between phases.

## MVP Definition
The Minimum Viable Product (MVP) consists of **Phase 1, Phase 2, and Phase 3**.
This delivers the core e-commerce flow: Users can log in (Phase 1), browse and search products (Phase 2), and complete a checkout with a shopping cart (Phase 3). 
Phases 4 through 7 represent post-MVP enhancements (Order Tracking, Recommendations, Admin Panel, Analytics).

---

## PHASE 1 â€” Foundation & Authentication (MVP Core)

**Goal**: Establish infrastructure, CI/CD pipeline, and core authentication so the application can run end-to-end.

**Requirements Covered**:
- REQ-001 - Google Authentication
- REQ-018 - Security (OAuth 2.0, TLS 1.2+)
- REQ-019 - Privacy (consent recording, privacy policy)
- REQ-022 - Single-Vendor Architecture
- REQ-023 - Google-Only Authentication
- REQ-035 - Assumption: Google OAuth configured

**Deliverables**:
- Project scaffolding (monorepo, Vite + React, Node/Express, MySQL, Redis)
- Google OAuth 2.0 login / logout flow
- JWT in HttpOnly cookies
- Privacy consent UI
- CI/CD pipeline (GitHub Actions + Docker)

**Test IDs**:
- TEST-001-UT-POS, TEST-001-UT-NEG, TEST-001-IT-POS, TEST-001-E2E-POS
- TEST-018-UT-POS, TEST-018-UT-NEG, TEST-018-IT-POS, TEST-018-E2E-POS
- TEST-019-UT-POS, TEST-019-IT-POS, TEST-019-E2E-POS
- TEST-022-UT-POS, TEST-022-IT-POS, TEST-022-E2E-POS
- TEST-023-UT-POS, TEST-023-UT-NEG, TEST-023-IT-POS, TEST-023-E2E-POS
- TEST-035-UT-POS, TEST-035-IT-POS, TEST-035-E2E-POS

---

## PHASE 2 â€” Product Catalog & Search

**Goal**: Users can browse, search, and view products. Forms the public-facing storefront.

**Requirements Covered**:
- REQ-002 - Product Search
- REQ-003 - Typo-Tolerant Search
- REQ-007 - Recently Viewed Products
- REQ-028 - Search Misspelling (Edge Case)

**Deliverables**:
- Product listing page with filtering and sorting
- Keyword search with typo-tolerance (<=2 character errors)
- Product detail page
- Recently viewed products tracking and display

**Test IDs**:
- TEST-002-UT-POS, TEST-002-UT-NEG, TEST-002-IT-POS, TEST-002-E2E-POS
- TEST-003-UT-POS, TEST-003-UT-NEG, TEST-003-IT-POS, TEST-003-E2E-POS
- TEST-007-UT-POS, TEST-007-IT-POS, TEST-007-E2E-POS
- TEST-028-UT-POS, TEST-028-UT-NEG, TEST-028-IT-POS, TEST-028-E2E-POS

---

## PHASE 3 â€” Wishlist, Cart & Checkout

**Goal**: Users can save products, manage a shopping cart, and complete a purchase.

**Requirements Covered**:
- REQ-004 - Wishlist
- REQ-008 - Shopping Cart
- REQ-025 - Payment Integration (external provider)
- REQ-027 - Out-of-Stock Purchase (Edge Case)
- REQ-029 - Duplicate Wishlist Entry (Edge Case)
- REQ-034 - Assumption: PCI handled externally

**Deliverables**:
- Wishlist add / remove (no duplicate entries)
- Cart add / update quantity / remove
- Checkout flow integrated with external payment provider
- Real-time out-of-stock block at checkout
- No card data stored in system

**Test IDs**:
- TEST-004-UT-POS, TEST-004-UT-NEG, TEST-004-IT-POS, TEST-004-E2E-POS
- TEST-008-UT-POS, TEST-008-UT-NEG, TEST-008-UT-BOUNDARY, TEST-008-IT-POS, TEST-008-E2E-POS
- TEST-025-UT-POS, TEST-025-IT-POS, TEST-025-E2E-POS
- TEST-027-UT-POS, TEST-027-UT-NEG, TEST-027-IT-POS, TEST-027-E2E-POS
- TEST-029-UT-POS, TEST-029-UT-NEG, TEST-029-IT-POS, TEST-029-E2E-POS
- TEST-034-UT-POS, TEST-034-IT-POS, TEST-034-E2E-POS

---

## PHASE 4 â€” Order Management (User-Facing)

**Goal**: Users can track and review orders after purchase.

**Requirements Covered**:
- REQ-009 - Order Management (User)
- REQ-017 - Reliability (ACID compliance, >=99.9% uptime)
- REQ-026 - Internet Outage (Edge Case)
- REQ-032 - Assumption: Reliable Internet

**Deliverables**:
- Order history list page
- Order detail / status page
- Offline error message with retry option
- ACID-compliant transaction handling

**Test IDs**:
- TEST-009-UT-POS, TEST-009-IT-POS, TEST-009-E2E-POS
- TEST-017-UT-POS, TEST-017-IT-POS, TEST-017-E2E-POS
- TEST-026-UT-POS, TEST-026-IT-POS, TEST-026-E2E-POS
- TEST-032-UT-POS, TEST-032-IT-POS, TEST-032-E2E-POS

---

## PHASE 5 â€” Personalization & Recommendations

**Goal**: Serve personalized product suggestions based on user behavior.

**Requirements Covered**:
- REQ-005 - Personalized Product Suggestions
- REQ-006 - Smart Suggestions Engine
- REQ-024 - No Multi-Vendor Marketplace (out-of-scope guard)

**Deliverables**:
- Recommendations based on browsing, wishlist, purchase history, and trending
- Smart suggestions engine combining all behavioral signals
- Explicit exclusion of marketplace seller features

**Test IDs**:
- TEST-005-UT-POS, TEST-005-IT-POS, TEST-005-E2E-POS
- TEST-006-UT-POS, TEST-006-IT-POS, TEST-006-E2E-POS
- TEST-024-UT-POS, TEST-024-IT-POS, TEST-024-E2E-POS

---

## PHASE 6 â€” Admin Panel (Products, Inventory & Orders)

**Goal**: Administrators can manage products, inventory, and orders.

**Requirements Covered**:
- REQ-010 - Product Management (Admin)
- REQ-011 - Inventory Management (Admin)
- REQ-012 - Order Management (Admin)
- REQ-030 - Concurrent Inventory Updates (Edge Case)
- REQ-033 - Assumption: Accurate Inventory Data

**Deliverables**:
- Admin product create / edit / archive / publish
- Inventory stock level tracking with low-stock alerts
- Admin order processing and status update UI
- Serialized concurrent stock update logic (no negative inventory)

**Test IDs**:
- TEST-010-UT-POS, TEST-010-UT-NEG, TEST-010-IT-POS, TEST-010-E2E-POS
- TEST-011-UT-POS, TEST-011-UT-NEG, TEST-011-IT-POS, TEST-011-E2E-POS
- TEST-012-UT-POS, TEST-012-UT-NEG, TEST-012-IT-POS, TEST-012-E2E-POS
- TEST-030-UT-POS, TEST-030-UT-NEG, TEST-030-IT-POS, TEST-030-E2E-POS
- TEST-033-UT-POS, TEST-033-IT-POS, TEST-033-E2E-POS

---

## PHASE 7 â€” Analytics, Reports & Non-Functional Hardening

**Goal**: Admin analytics dashboard and full non-functional requirement compliance.

**Requirements Covered**:
- REQ-013 - Analytics Dashboard
- REQ-014 - Usability (<=5 min learning curve)
- REQ-015 - Accessibility (WCAG 2.1 AA, >=4.5:1 contrast)
- REQ-016 - Performance (<=2s page load, <=300ms search)
- REQ-020 - Scalability (100k concurrent users, 50k products)
- REQ-021 - Maintainability (>=80% unit-test coverage, ESLint Airbnb)
- REQ-031 - Assumption: Single-Vendor Business

**Deliverables**:
- Admin analytics dashboard (sales, orders, inventory KPIs)
- Lighthouse / a11y audit passing
- Load-testing validation (k6 or Artillery)
- ESLint Airbnb config enforced in CI
- Unit-test coverage gate >=80%

**Test IDs**:
- TEST-013-UT-POS, TEST-013-IT-POS, TEST-013-E2E-POS
- TEST-014-UT-POS, TEST-014-IT-POS, TEST-014-E2E-POS
- TEST-015-UT-POS, TEST-015-IT-POS, TEST-015-E2E-POS
- TEST-016-UT-POS, TEST-016-UT-BOUNDARY, TEST-016-IT-POS, TEST-016-E2E-POS
- TEST-020-UT-POS, TEST-020-UT-BOUNDARY, TEST-020-IT-POS, TEST-020-E2E-POS
- TEST-021-UT-POS, TEST-021-IT-POS, TEST-021-E2E-POS
- TEST-031-UT-POS, TEST-031-IT-POS, TEST-031-E2E-POS

---

## Phase Summary

| Phase   | Focus                            | REQs                                   | Status      |
|---------|----------------------------------|----------------------------------------|-------------|
| PHASE 1 | Foundation & Auth                | REQ-001, 018, 019, 022, 023, 035       | Pending |
| PHASE 2 | Product Catalog & Search         | REQ-002, 003, 007, 028                 | Pending |
| PHASE 3 | Wishlist, Cart & Checkout        | REQ-004, 008, 025, 027, 029, 034       | Pending |
| PHASE 4 | Order Management (User)          | REQ-009, 017, 026, 032                 | Pending |
| PHASE 5 | Personalization & Recommendations| REQ-005, 006, 024                      | Pending |
| PHASE 6 | Admin Panel                      | REQ-010, 011, 012, 030, 033            | Pending |
| PHASE 7 | Analytics & NFR Hardening        | REQ-013, 014, 015, 016, 020, 021, 031  | Pending |
