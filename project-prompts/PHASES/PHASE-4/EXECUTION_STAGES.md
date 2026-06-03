File: /project-prompts/PHASES/PHASE-4/EXECUTION_STAGES.md

# PHASE 4 â€” Order Management (User-Facing): Execution Stages

REQs: REQ-009, REQ-017, REQ-026, REQ-032

---

## STAGE 1 â€” Setup

**REQ-IDs**: REQ-009, REQ-032
**TEST-IDs**: TEST-009-UT-POS, TEST-032-UT-POS

**Tasks**:
- Set up mock order data for testing UI offline behaviors
- Install offline-detection library or implement custom hook

**TDD Flow**:
1. Write: useNetworkStatus.test.tsx â€” verify hook returns false when navigator.onLine is false
2. Run -> FAIL
3. Implement: useNetworkStatus hook
4. Run -> PASS
5. Refactor: add event listeners for online/offline events

---

## STAGE 2 â€” Architecture

**REQ-IDs**: REQ-009, REQ-017, REQ-026, REQ-032
**TEST-IDs**: TEST-009-UT-POS, TEST-017-UT-POS, TEST-026-UT-POS, TEST-032-UT-POS

**Tasks**:
- Define API contracts: GET /orders, GET /orders/:id
- Define React page structure: OrderHistoryPage, OrderDetailPage
- Design offline state component structure
- Define transaction strategy for database (Sequelize $transaction)

**TDD Flow**:
1. Write: order-types.test.ts â€” verify Order and OrderItem types include status strings
2. Run -> FAIL
3. Implement: Order types and Enums (PENDING, PROCESSING, SHIPPED, DELIVERED, CANCELLED)
4. Run -> PASS
5. Refactor: align with Sequelize schema

---

## STAGE 3 â€” Database

**REQ-IDs**: REQ-017
**TEST-IDs**: TEST-017-IT-POS

**Tasks**:
- Ensure Order and OrderItem tables are correctly related and indexed by userId
- Add index on Order(userId, createdAt) for sorting order history
- Verify ACID compliance of order creation (already created in Phase 3 webhook, but need to verify transaction wrapper)

**TDD Flow**:
1. Write: order-transaction.test.ts â€” verify order creation fails safely if order items fail to insert
2. Run -> FAIL
3. Implement: Sequelize $transaction block in order creation service
4. Run -> PASS
5. Refactor: extract common transaction wrapper

---

## STAGE 4 â€” Backend

**REQ-IDs**: REQ-009
**TEST-IDs**: TEST-009-UT-POS, TEST-009-IT-POS

**Tasks**:
- GET /orders â€” fetch user's orders, paginated, sorted by date descending
- GET /orders/:id â€” fetch specific order details including items and product snapshot data

**TDD Flow**:
1. Write: order-api.test.ts â€” GET /orders returns user orders
2. Run -> FAIL
3. Implement: orderService.getUserOrders() and route handler
4. Run -> PASS
5. Refactor: add pagination metadata to response

---

## STAGE 5 â€” Frontend

**REQ-IDs**: REQ-009, REQ-026
**TEST-IDs**: TEST-009-E2E-POS, TEST-026-E2E-POS

**Tasks**:
- Build OrderHistoryPage (list of past orders with status badges and totals)
- Build OrderDetailPage (shipping details, item list, tracking info)
- Build OfflineIndicator component (shows when network is down)
- Build ErrorState component (shows "Internet Outage" with "Retry" button when API calls fail due to network)

**TDD Flow**:
1. Write: OfflineIndicator.test.tsx â€” renders when network is offline
2. Run -> FAIL
3. Implement: OfflineIndicator using useNetworkStatus
4. Run -> PASS
5. Refactor: add CSS animations for smooth appearance

---

## STAGE 6 â€” State

**REQ-IDs**: REQ-009
**TEST-IDs**: TEST-009-UT-POS

**Tasks**:
- Implement useOrders() React Query hook
- Implement useOrderDetail(id) React Query hook
- Configure React Query retry logic for network failures

**TDD Flow**:
1. Write: useOrders.test.tsx â€” fetches and caches user orders
2. Run -> FAIL
3. Implement: useOrders hook
4. Run -> PASS
5. Refactor: configure staleTime for order history

---

## STAGE 7 â€” Auth

**REQ-IDs**: REQ-009
**TEST-IDs**: TEST-009-IT-POS

**Tasks**:
- Ensure GET /orders and GET /orders/:id require authentication
- Ensure user can only fetch their own orders (authorization check on :id)

**TDD Flow**:
1. Write: order-auth.test.ts â€” GET /orders/:id for someone else's order returns 403 or 404
2. Run -> FAIL
3. Implement: authorization check in orderService.getOrderById()
4. Run -> PASS
5. Refactor: move ownership check to reusable middleware or service layer

---

## STAGE 8 â€” Integration

**REQ-IDs**: REQ-009, REQ-017, REQ-026, REQ-032
**TEST-IDs**: TEST-009-IT-POS, TEST-017-IT-POS, TEST-026-IT-POS, TEST-032-IT-POS

**Tasks**:
- Verify order history correctly reflects completed checkouts
- Simulate offline condition during fetch, verify offline message and retry functionality
- Verify database transaction rollbacks on simulated failures

**TDD Flow**:
1. Write: order-integration.test.ts â€” full fetch flow with mock offline failure and retry
2. Run -> FAIL
3. Implement: integration wiring with React Query retry
4. Run -> PASS
5. Refactor: verify UI handles the error boundary

---

## STAGE 9 â€” Testing

**REQ-IDs**: All Phase 4 REQs
**TEST-IDs**: All Phase 4 TEST-IDs

**Tasks**:
- Run: pnpm test
- Run: pnpm test:integration
- Run: pnpm cypress:run --spec "orders.cy.ts,offline.cy.ts"
- Verify coverage >= 80%
- Fix all failures

---

## STAGE 10 â€” Deployment

**REQ-IDs**: REQ-017
**TEST-IDs**: TEST-017-E2E-POS

**Tasks**:
- Deploy Phase 4 to staging
- Smoke-test: complete a purchase, check order history, check order details
- Simulate offline mode using browser dev tools, verify UI response
- Check database metrics for query performance on orders table
