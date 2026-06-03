File: /project-prompts/PHASES/PHASE-5/EXECUTION_STAGES.md

# PHASE 5 â€” Personalization & Recommendations: Execution Stages

REQs: REQ-005, REQ-006, REQ-024

---

## STAGE 1 â€” Setup

**REQ-IDs**: REQ-005
**TEST-IDs**: TEST-005-UT-POS

**Tasks**:
- Set up recommendation engine scaffold
- Create mock behavioral data for tests (mock views, wishlist items, purchases)

**TDD Flow**:
1. Write: recommendation-config.test.ts â€” verify data sources are accessible to engine
2. Run -> FAIL
3. Implement: recommendationService scaffold
4. Run -> PASS
5. Refactor: structure service for multiple algorithmic strategies

---

## STAGE 2 â€” Architecture

**REQ-IDs**: REQ-005, REQ-006, REQ-024
**TEST-IDs**: TEST-005-UT-POS, TEST-006-UT-POS, TEST-024-UT-POS

**Tasks**:
- Define API contract: GET /recommendations
- Define Recommendation Engine architecture: weighting system (e.g., Purchase = 5, Wishlist = 3, View = 1)
- Explicitly add architectural constraint check preventing multi-vendor logic

**TDD Flow**:
1. Write: engine-architecture.test.ts â€” verify engine combines scores from multiple sources
2. Run -> FAIL
3. Implement: base scoring logic
4. Run -> PASS
5. Refactor: extract scoring weights into configuration

---

## STAGE 3 â€” Database

**REQ-IDs**: REQ-005, REQ-006
**TEST-IDs**: TEST-005-IT-POS, TEST-006-IT-POS

**Tasks**:
- Ensure all necessary indexes exist for fast aggregation (Views, Wishlist, Orders)
- Create Materialized View or caching strategy for "Trending Products" (e.g., most viewed/bought in last 7 days)
- Run migration for trending view if applicable

**TDD Flow**:
1. Write: trending-query.test.ts â€” verify trending query returns top products quickly
2. Run -> FAIL
3. Implement: Sequelize raw query or Redis sorted set for trending
4. Run -> PASS
5. Refactor: schedule trending update job

---

## STAGE 4 â€” Backend

**REQ-IDs**: REQ-005, REQ-006
**TEST-IDs**: TEST-005-UT-POS, TEST-006-UT-POS

**Tasks**:
- Implement recommendationService.getRecommendations(userId)
- Combine signals: Search history, Wishlist, Recently Viewed, Purchase History
- Fallback logic: if insufficient user data, return Trending products
- GET /recommendations â€” endpoint to serve personalized list

**TDD Flow**:
1. Write: recommendations.test.ts â€” verify user with specific views gets related recommendations
2. Run -> FAIL
3. Implement: signal aggregation and product fetching
4. Run -> PASS
5. Refactor: optimize DB queries (avoid N+1)

---

## STAGE 5 â€” Frontend

**REQ-IDs**: REQ-005, REQ-006, REQ-024
**TEST-IDs**: TEST-005-E2E-POS, TEST-006-E2E-POS, TEST-024-E2E-POS

**Tasks**:
- Build ProductCarousel component
- Add "Recommended for You" section to HomePage
- Add "You Might Also Like" section to ProductDetailPage (based on current product category + user history)
- Ensure UI strictly reflects single-vendor design (no "Sold by X" labels)

**TDD Flow**:
1. Write: ProductCarousel.test.tsx â€” renders list of recommended products
2. Run -> FAIL
3. Implement: ProductCarousel UI
4. Run -> PASS
5. Refactor: make carousel responsive and touch-friendly

---

## STAGE 6 â€” State

**REQ-IDs**: REQ-005
**TEST-IDs**: TEST-005-UT-POS

**Tasks**:
- Implement useRecommendations() React Query hook
- Configure aggressive caching (staleTime: 1 hour) as recommendations don't need real-time updates

**TDD Flow**:
1. Write: useRecommendations.test.tsx â€” fetches recommendations
2. Run -> FAIL
3. Implement: hook logic
4. Run -> PASS
5. Refactor: integrate with Suspense if applicable

---

## STAGE 7 â€” Auth

**REQ-IDs**: REQ-005
**TEST-IDs**: TEST-005-IT-POS

**Tasks**:
- Verify GET /recommendations returns personalized data with JWT
- Verify GET /recommendations returns generic/trending data without JWT (graceful degradation)

**TDD Flow**:
1. Write: recommendation-auth.test.ts â€” unauthenticated request returns trending products
2. Run -> FAIL
3. Implement: auth check branch in route handler
4. Run -> PASS
5. Refactor: clarify code paths for auth vs unauth

---

## STAGE 8 â€” Integration

**REQ-IDs**: REQ-005, REQ-006
**TEST-IDs**: TEST-005-IT-POS, TEST-006-IT-POS

**Tasks**:
- End-to-end test of recommendation pipeline (add to wishlist -> fetch recommendations -> verify product is influenced)
- Verify performance of recommendation query under load

**TDD Flow**:
1. Write: recommendation-integration.test.ts â€” full flow from user action to recommendation output
2. Run -> FAIL
3. Implement: necessary wiring and performance tuning
4. Run -> PASS
5. Refactor: add caching layer (Redis) to the output

---

## STAGE 9 â€” Testing

**REQ-IDs**: All Phase 5 REQs
**TEST-IDs**: All Phase 5 TEST-IDs

**Tasks**:
- Run: pnpm test
- Run: pnpm test:integration
- Run: pnpm cypress:run --spec "recommendations.cy.ts"
- Verify coverage >= 80% for recommendationService
- Fix all failures

---

## STAGE 10 â€” Deployment

**REQ-IDs**: REQ-005
**TEST-IDs**: TEST-005-E2E-POS

**Tasks**:
- Deploy Phase 5 to staging
- Smoke-test: browse products, check home page for updated recommendations
- Verify Redis cache hit rates for recommendations
- Check logs for any slow queries generated by the recommendation engine
