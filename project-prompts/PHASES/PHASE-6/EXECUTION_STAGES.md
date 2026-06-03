File: /project-prompts/PHASES/PHASE-6/EXECUTION_STAGES.md

# PHASE 6 â€” Admin Panel (Products, Inventory & Orders): Execution Stages

REQs: REQ-010, REQ-011, REQ-012, REQ-030, REQ-033

---

## STAGE 1 â€” Setup

**REQ-IDs**: REQ-010
**TEST-IDs**: TEST-010-UT-POS

**Tasks**:
- Set up Role-Based Access Control (RBAC) foundation
- Create admin user seed script
- Configure image upload service (e.g., Azure Blob Storage or AWS S3 mock)

**TDD Flow**:
1. Write: rbac-config.test.ts â€” verify Admin role definition exists
2. Run -> FAIL
3. Implement: User role enum and RBAC constants
4. Run -> PASS
5. Refactor: extract to shared types

---

## STAGE 2 â€” Architecture

**REQ-IDs**: REQ-010, REQ-011, REQ-012
**TEST-IDs**: TEST-010-UT-POS, TEST-011-UT-POS, TEST-012-UT-POS

**Tasks**:
- Define Admin API contracts: under `/api/admin/*`
- Define React Admin layout (sidebar, header, content area)
- Establish concurrent update strategy for inventory (optimistic locking vs pessimistic locking vs atomic increments)

**TDD Flow**:
1. Write: admin-layout.test.tsx â€” verify layout renders sidebar and children
2. Run -> FAIL
3. Implement: AdminLayout component
4. Run -> PASS
5. Refactor: setup nested routing for admin area

---

## STAGE 3 â€” Database

**REQ-IDs**: REQ-010, REQ-011, REQ-030
**TEST-IDs**: TEST-010-IT-POS, TEST-011-IT-POS, TEST-030-IT-POS

**Tasks**:
- Update User schema to include `role` (USER, ADMIN)
- Ensure Product schema has `stock` (Int) and `isArchived` (Boolean)
- Run migration: Sequelize migrate dev --name add-user-roles
- Implement atomic update logic for inventory using Sequelize `update` with `decrement`/`increment`

**TDD Flow**:
1. Write: inventory-atomic.test.ts â€” simulate concurrent purchases, verify stock doesn't go below 0
2. Run -> FAIL
3. Implement: atomic decrement in Sequelize and database-level CHECK constraint (stock >= 0)
4. Run -> PASS
5. Refactor: wrap in robust error handling for ConstraintViolation

---

## STAGE 4 â€” Backend

**REQ-IDs**: REQ-010, REQ-011, REQ-012, REQ-030
**TEST-IDs**: TEST-010-UT-POS, TEST-010-UT-NEG, TEST-010-IT-POS, TEST-011-UT-POS, TEST-011-UT-NEG, TEST-011-IT-POS, TEST-012-UT-POS, TEST-012-UT-NEG, TEST-012-IT-POS, TEST-030-UT-POS, TEST-030-UT-NEG, TEST-030-IT-POS

**Tasks**:
- POST /admin/products, PUT /admin/products/:id, DELETE /admin/products/:id (Archive)
- GET /admin/inventory â€” list products with stock < threshold (low-stock alerts)
- PUT /admin/inventory/:id â€” manually update stock level (serialized)
- GET /admin/orders â€” list all orders
- PUT /admin/orders/:id/status â€” update order status (e.g., Processing -> Shipped)

**TDD Flow**:
1. Write: admin-product.test.ts â€” create product API requires admin role
2. Run -> FAIL
3. Implement: POST /admin/products with admin middleware
4. Run -> PASS
5. Refactor: extract validation logic to Zod schemas

---

## STAGE 5 â€” Frontend

**REQ-IDs**: REQ-010, REQ-011, REQ-012, REQ-033
**TEST-IDs**: TEST-010-E2E-POS, TEST-011-E2E-POS, TEST-012-E2E-POS, TEST-033-E2E-POS

**Tasks**:
- Build Admin Products Page (data table, add/edit modal)
- Build Admin Inventory Page (low-stock highlighting, quick-edit stock input)
- Build Admin Orders Page (status dropdowns, filter by status)
- Integrate image upload component in Product form

**TDD Flow**:
1. Write: InventoryTable.test.tsx â€” rows with stock < 10 have alert styling
2. Run -> FAIL
3. Implement: InventoryTable with conditional styling
4. Run -> PASS
5. Refactor: extract DataTable component for reuse

---

## STAGE 6 â€” State

**REQ-IDs**: REQ-010, REQ-011, REQ-012
**TEST-IDs**: TEST-010-UT-POS, TEST-011-UT-POS, TEST-012-UT-POS

**Tasks**:
- Implement useAdminProducts(), useAdminInventory(), useAdminOrders() hooks
- Implement mutations for create/update/delete with cache invalidation
- Configure polling or manual refresh for inventory and orders (real-time not strictly required, but freshness is)

**TDD Flow**:
1. Write: useAdminMutations.test.tsx â€” updating product invalidates product list cache
2. Run -> FAIL
3. Implement: React Query mutation with onSettled invalidation
4. Run -> PASS
5. Refactor: generic mutation wrappers

---

## STAGE 7 â€” Auth

**REQ-IDs**: REQ-010, REQ-011, REQ-012
**TEST-IDs**: TEST-010-IT-POS, TEST-011-IT-POS, TEST-012-IT-POS

**Tasks**:
- Create requireAdmin middleware
- Apply requireAdmin to all `/api/admin/*` routes
- Protect frontend `/admin/*` routes (redirect non-admins to home)

**TDD Flow**:
1. Write: admin-auth.test.ts â€” non-admin user accessing /api/admin/orders gets 403
2. Run -> FAIL
3. Implement: requireAdmin middleware
4. Run -> PASS
5. Refactor: unified error responses for 401 vs 403

---

## STAGE 8 â€” Integration

**REQ-IDs**: REQ-010, REQ-011, REQ-012, REQ-030
**TEST-IDs**: TEST-010-IT-POS, TEST-011-IT-POS, TEST-012-IT-POS, TEST-030-IT-POS

**Tasks**:
- Verify full product lifecycle: Admin creates -> User sees -> Admin archives -> User doesn't see
- Verify inventory management: Admin updates stock -> User checkout respects new stock
- Verify concurrent inventory updates: 2 admins update simultaneously, result is correct

**TDD Flow**:
1. Write: product-lifecycle.test.ts â€” full e2e flow via API
2. Run -> FAIL
3. Implement: correct archiving logic (filter archived from public API)
4. Run -> PASS
5. Refactor: centralize public vs admin query filters

---

## STAGE 9 â€” Testing

**REQ-IDs**: All Phase 6 REQs
**TEST-IDs**: All Phase 6 TEST-IDs

**Tasks**:
- Run: pnpm test
- Run: pnpm test:integration
- Run: pnpm cypress:run --spec "admin-products.cy.ts,admin-orders.cy.ts,admin-inventory.cy.ts"
- Verify coverage >= 80% for admin controllers and services
- Fix all failures

---

## STAGE 10 â€” Deployment

**REQ-IDs**: REQ-010, REQ-011, REQ-012
**TEST-IDs**: TEST-010-E2E-POS, TEST-011-E2E-POS, TEST-012-E2E-POS

**Tasks**:
- Deploy Phase 6 to staging
- Grant Admin role to test account via database script
- Smoke-test: log in as admin, create product, view in storefront, update order status
- Verify Admin routes are inaccessible to normal users in staging
