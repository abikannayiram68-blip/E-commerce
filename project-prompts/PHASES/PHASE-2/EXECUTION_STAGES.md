File: /project-prompts/PHASES/PHASE-2/EXECUTION_STAGES.md

# PHASE 2 â€” Product Catalog & Search: Execution Stages

REQs: REQ-002, REQ-003, REQ-007, REQ-028

---

## STAGE 1 â€” Setup

**REQ-IDs**: REQ-002, REQ-003
**TEST-IDs**: TEST-002-UT-POS, TEST-003-UT-POS

**Tasks**:
- Enable FULLTEXT extension in MySQL for fuzzy search
- Add product seed data (50 sample products with categories, prices, images)
- Configure Redis for recently-viewed cache (TTL: 24h)

**TDD Flow**:
1. Write: search-config.test.ts â€” verify FULLTEXT extension is active
2. Run -> FAIL
3. Implement: migration enabling FULLTEXT
4. Run -> PASS
5. Refactor: extract dbExtensions helper

---

## STAGE 2 â€” Architecture

**REQ-IDs**: REQ-002, REQ-003, REQ-007
**TEST-IDs**: TEST-002-UT-POS, TEST-003-UT-POS, TEST-007-UT-POS

**Tasks**:
- Define API contracts: GET /products, GET /products/:slug, GET /search, GET /users/me/recently-viewed
- Define React page structure: ProductListPage, ProductDetailPage, SearchResultsPage
- Define shared Product type in /packages/types

**TDD Flow**:
1. Write: product-types.test.ts â€” verify Product shape matches schema
2. Run -> FAIL
3. Implement: Product type + Zod schema
4. Run -> PASS
5. Refactor: export from @repo/types

---

## STAGE 3 â€” Database

**REQ-IDs**: REQ-002, REQ-003, REQ-007
**TEST-IDs**: TEST-002-IT-POS, TEST-003-IT-POS, TEST-007-IT-POS

**Tasks**:
- Create Product model (id, slug, name, description, price, stock, categoryId, images[], isArchived)
- Create Category model (id, slug, name, parentId)
- Create RecentlyViewed model (userId, productId, viewedAt)
- Run migration: Sequelize migrate dev --name products-catalog
- Create indexes: Product(slug), Product(name trgm), RecentlyViewed(userId, viewedAt)

**TDD Flow**:
1. Write: product-model.test.ts â€” create product, fetch by slug, verify fields
2. Run -> FAIL
3. Implement: Sequelize Product + Category schema + migration
4. Run -> PASS
5. Refactor: add compound unique constraint on (userId, productId) for RecentlyViewed

---

## STAGE 4 â€” Backend

**REQ-IDs**: REQ-002, REQ-003, REQ-007, REQ-028
**TEST-IDs**: TEST-002-UT-POS, TEST-002-UT-NEG, TEST-002-IT-POS, TEST-003-UT-POS, TEST-003-UT-NEG, TEST-003-IT-POS, TEST-007-UT-POS, TEST-007-IT-POS, TEST-028-UT-POS, TEST-028-UT-NEG, TEST-028-IT-POS

**Tasks**:
- GET /products â€” paginated listing with filters (category, price range, sort)
- GET /products/:slug â€” single product detail
- GET /search?q=:query â€” fuzzy search via FULLTEXT similarity (<=2 char errors)
- POST /users/me/recently-viewed â€” record product view (auth required)
- GET /users/me/recently-viewed â€” return last 10 viewed products (auth required)

**TDD Flow**:
1. Write: search.test.ts â€” query "shos" must return "shoes"
2. Run -> FAIL
3. Implement: productService.search() using FULLTEXT similarity()
4. Run -> PASS
5. Refactor: extract searchService, add query sanitization

---

## STAGE 5 â€” Frontend

**REQ-IDs**: REQ-002, REQ-003, REQ-007, REQ-028
**TEST-IDs**: TEST-002-E2E-POS, TEST-003-E2E-POS, TEST-007-E2E-POS, TEST-028-E2E-POS

**Tasks**:
- Build ProductCard component (image, name, price, wishlist toggle)
- Build ProductListPage with pagination, category filter sidebar, sort dropdown
- Build ProductDetailPage (images, description, price, Add to Cart, Add to Wishlist)
- Build SearchBar component (debounced input, 300ms delay)
- Build SearchResultsPage (query display, results grid, empty state)
- Build RecentlyViewed horizontal scroll section on home + product detail pages

**TDD Flow**:
1. Write: SearchBar.test.tsx â€” verify debounce delays API call by 300ms
2. Run -> FAIL
3. Implement: SearchBar with useDebounce hook
4. Run -> PASS
5. Refactor: extract useProductSearch hook

---

## STAGE 6 â€” State

**REQ-IDs**: REQ-002, REQ-007
**TEST-IDs**: TEST-002-UT-POS, TEST-007-UT-POS

**Tasks**:
- Implement useProductSearch(query) React Query hook
- Implement useRecentlyViewed() â€” fetch + record views
- Implement useProductDetail(slug) hook
- Cache product queries (staleTime: 5 min)

**TDD Flow**:
1. Write: useRecentlyViewed.test.tsx â€” viewing a product calls POST endpoint
2. Run -> FAIL
3. Implement: useRecentlyViewed with mutation + cache invalidation
4. Run -> PASS
5. Refactor: deduplicate view tracking logic

---

## STAGE 7 â€” Auth

**REQ-IDs**: REQ-002, REQ-007
**TEST-IDs**: TEST-002-IT-POS, TEST-007-IT-POS

**Tasks**:
- Verify product listing returns 200 for unauthenticated users
- Verify recently-viewed endpoints return 401 without JWT
- Verify search works for anonymous users

**TDD Flow**:
1. Write: product-auth.test.ts â€” GET /products returns 200 without auth
2. Run -> FAIL
3. Implement: optional auth middleware for public product routes
4. Run -> PASS
5. Refactor: separate public vs protected route groups

---

## STAGE 8 â€” Integration

**REQ-IDs**: REQ-002, REQ-003, REQ-007, REQ-028
**TEST-IDs**: TEST-002-IT-POS, TEST-003-IT-POS, TEST-007-IT-POS, TEST-028-IT-POS

**Tasks**:
- Verify fuzzy search returns results for "iphon" -> iPhone
- Verify recently-viewed updates after product page visit
- Verify empty search returns 200 with empty array (not error)
- Verify product pagination (page 1, 2, 3) works correctly

**TDD Flow**:
1. Write: search-integration.test.ts â€” end-to-end fuzzy search pipeline
2. Run -> FAIL
3. Implement: full search route -> service -> DB pipeline
4. Run -> PASS
5. Refactor: add search response time logging

---

## STAGE 9 â€” Testing

**REQ-IDs**: REQ-002, REQ-003, REQ-007, REQ-028
**TEST-IDs**: All Phase 2 TEST-IDs

**Tasks**:
- Run: pnpm test (unit suite)
- Run: pnpm test:integration
- Run: pnpm cypress:run --spec "search.cy.ts,product.cy.ts"
- Verify coverage >=80% for productService, searchService
- Fix all failures before proceeding

---

## STAGE 10 â€” Deployment

**REQ-IDs**: REQ-016, REQ-020
**TEST-IDs**: TEST-016-E2E-POS, TEST-020-E2E-POS

**Tasks**:
- Deploy Phase 2 to staging
- Run Lighthouse audit: page load <=2s on simulated 3G
- Verify search response <=300ms (k6 smoke test)
- Confirm product images load via CDN/static serving
