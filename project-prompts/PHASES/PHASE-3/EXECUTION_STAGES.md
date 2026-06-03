File: /project-prompts/PHASES/PHASE-3/EXECUTION_STAGES.md

# PHASE 3 â€” Wishlist, Cart & Checkout: Execution Stages

REQs: REQ-004, REQ-008, REQ-025, REQ-027, REQ-029, REQ-034

---

## STAGE 1 â€” Setup

**REQ-IDs**: REQ-004, REQ-008, REQ-025
**TEST-IDs**: TEST-004-UT-POS, TEST-008-UT-POS, TEST-025-UT-POS

**Tasks**:
- Configure external payment provider SDK (Stripe or equivalent)
- Add environment variables: PAYMENT_SECRET_KEY, PAYMENT_WEBHOOK_SECRET
- Define cart state management approach (server-side DB + local sync)

**TDD Flow**:
1. Write: payment-config.test.ts â€” verify payment SDK initializes with valid key
2. Run -> FAIL
3. Implement: paymentService initialization
4. Run -> PASS
5. Refactor: extract paymentConfig module

---

## STAGE 2 â€” Architecture

**REQ-IDs**: REQ-004, REQ-008, REQ-025, REQ-029
**TEST-IDs**: TEST-004-UT-POS, TEST-008-UT-POS, TEST-025-UT-POS, TEST-029-UT-POS

**Tasks**:
- Define API contracts: POST /wishlist, DELETE /wishlist/:productId, GET /wishlist
- Define cart APIs: GET /cart, POST /cart/items, PATCH /cart/items/:id, DELETE /cart/items/:id
- Define checkout API: POST /checkout/session, POST /checkout/webhook
- Define shared CartItem, WishlistItem types in /packages/types

**TDD Flow**:
1. Write: cart-types.test.ts â€” verify CartItem shape (productId, quantity, price)
2. Run -> FAIL
3. Implement: CartItem Zod schema
4. Run -> PASS
5. Refactor: export all commerce types from @repo/types

---

## STAGE 3 â€” Database

**REQ-IDs**: REQ-004, REQ-008, REQ-029
**TEST-IDs**: TEST-004-IT-POS, TEST-008-IT-POS, TEST-029-IT-POS

**Tasks**:
- Create Wishlist model (userId, productId) â€” unique constraint on (userId, productId)
- Create Cart model (userId) + CartItem (cartId, productId, quantity)
- Create Order model (id, userId, status, totalAmount, createdAt)
- Create OrderItem model (orderId, productId, quantity, unitPrice)
- Run migration: Sequelize migrate dev --name wishlist-cart-orders

**TDD Flow**:
1. Write: wishlist-model.test.ts â€” add same product twice, expect unique constraint error
2. Run -> FAIL
3. Implement: Sequelize Wishlist model with @@unique([userId, productId])
4. Run -> PASS
5. Refactor: add cascade delete when user account deleted

---

## STAGE 4 â€” Backend

**REQ-IDs**: REQ-004, REQ-008, REQ-025, REQ-027, REQ-029, REQ-034
**TEST-IDs**: TEST-004-UT-POS, TEST-004-UT-NEG, TEST-004-IT-POS, TEST-008-UT-POS, TEST-008-UT-NEG, TEST-008-UT-BOUNDARY, TEST-008-IT-POS, TEST-025-UT-POS, TEST-025-IT-POS, TEST-027-UT-POS, TEST-027-UT-NEG, TEST-027-IT-POS, TEST-029-UT-POS, TEST-029-UT-NEG, TEST-029-IT-POS, TEST-034-UT-POS, TEST-034-IT-POS

**Tasks**:
- POST /wishlist â€” add product (reject duplicate with 409)
- DELETE /wishlist/:productId â€” remove product
- GET /wishlist â€” list user wishlist
- POST /cart/items â€” add product to cart (verify stock > 0, reject if out-of-stock)
- PATCH /cart/items/:id â€” update quantity (boundary: qty >= 1)
- DELETE /cart/items/:id â€” remove item
- POST /checkout/session â€” create payment provider session (no card data stored)
- POST /checkout/webhook â€” handle payment confirmation, create Order record

**TDD Flow**:
1. Write: wishlist.test.ts â€” duplicate add returns 409 Conflict
2. Run -> FAIL
3. Implement: wishlistService.add() with upsert conflict detection
4. Run -> PASS
5. Refactor: extract duplicate guard as reusable util

---

## STAGE 5 â€” Frontend

**REQ-IDs**: REQ-004, REQ-008, REQ-025, REQ-027, REQ-029
**TEST-IDs**: TEST-004-E2E-POS, TEST-008-E2E-POS, TEST-025-E2E-POS, TEST-027-E2E-POS, TEST-029-E2E-POS

**Tasks**:
- Build WishlistPage (grid of saved products, remove button)
- Add WishlistToggle button to ProductCard + ProductDetailPage
- Build CartDrawer (slide-in panel: items, quantities, subtotal, checkout button)
- Build CartPage (full cart view, quantity steppers, remove, proceed to checkout)
- Build CheckoutPage (order summary, redirect to payment provider)
- Show real-time out-of-stock badge on product cards
- Show toast notification when duplicate wishlist add is attempted

**TDD Flow**:
1. Write: CartDrawer.test.tsx â€” updating quantity calls PATCH endpoint
2. Run -> FAIL
3. Implement: CartDrawer with useCart hook
4. Run -> PASS
5. Refactor: extract CartItem component

---

## STAGE 6 â€” State

**REQ-IDs**: REQ-004, REQ-008, REQ-029
**TEST-IDs**: TEST-004-UT-POS, TEST-008-UT-POS, TEST-029-UT-POS

**Tasks**:
- Implement useWishlist() hook (add, remove, check if product is in wishlist)
- Implement useCart() hook (items, addItem, updateQty, removeItem, total)
- Implement useCheckout() hook (createSession, redirect to provider)
- Sync cart item count in header badge

**TDD Flow**:
1. Write: useWishlist.test.tsx â€” adding duplicate shows error toast, does not mutate state
2. Run -> FAIL
3. Implement: useWishlist with duplicate guard + toast notification
4. Run -> PASS
5. Refactor: centralize toast notification system

---

## STAGE 7 â€” Auth

**REQ-IDs**: REQ-004, REQ-008, REQ-025
**TEST-IDs**: TEST-004-IT-POS, TEST-008-IT-POS, TEST-025-IT-POS

**Tasks**:
- All wishlist/cart/checkout endpoints require authentication (401 if missing JWT)
- Redirect unauthenticated user to login when clicking Add to Wishlist / Add to Cart
- Verify payment webhook uses secret signature validation (not JWT)

**TDD Flow**:
1. Write: cart-auth.test.ts â€” POST /cart/items without JWT returns 401
2. Run -> FAIL
3. Implement: requireAuth middleware on all cart/wishlist routes
4. Run -> PASS
5. Refactor: apply middleware at router level

---

## STAGE 8 â€” Integration

**REQ-IDs**: REQ-004, REQ-008, REQ-025, REQ-027, REQ-029, REQ-034
**TEST-IDs**: TEST-004-IT-POS, TEST-008-IT-POS, TEST-025-IT-POS, TEST-027-IT-POS, TEST-029-IT-POS, TEST-034-IT-POS

**Tasks**:
- Verify adding out-of-stock product to cart returns 409 with real-time message
- Verify payment webhook creates Order record after successful payment
- Verify no card data in database after checkout
- Verify duplicate wishlist add shows 409 and does not create second record

**TDD Flow**:
1. Write: checkout-integration.test.ts â€” mock webhook event, verify Order created in DB
2. Run -> FAIL
3. Implement: webhookService.handlePaymentSuccess() -> orderService.create()
4. Run -> PASS
5. Refactor: extract order creation as atomic transaction

---

## STAGE 9 â€” Testing

**REQ-IDs**: REQ-004, REQ-008, REQ-025, REQ-027, REQ-029, REQ-034
**TEST-IDs**: All Phase 3 TEST-IDs

**Tasks**:
- Run: pnpm test (unit suite)
- Run: pnpm test:integration
- Run: pnpm cypress:run --spec "wishlist.cy.ts,cart.cy.ts,checkout.cy.ts"
- Verify coverage >=80% for wishlistService, cartService, paymentService
- Fix all failures before proceeding

---

## STAGE 10 â€” Deployment

**REQ-IDs**: REQ-017, REQ-025, REQ-034
**TEST-IDs**: TEST-017-E2E-POS, TEST-025-E2E-POS, TEST-034-E2E-POS

**Tasks**:
- Deploy Phase 3 to staging
- Smoke-test: add to cart, proceed to checkout, verify payment provider redirect
- Confirm no card data written to MySQL (DB audit)
- Verify payment webhook signature validation on Azure endpoint
