File: /project-prompts/TESTS/E2E_TESTS.md
# End-to-End Tests

## REQ-001 - Google Authentication (User Login Flow)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-001-E2E-POS | REQ-001 | Complete login via Google and landing on dashboard | 1. Navigate to `/login` 2. Click **Sign in with Google** 3. Mock Google consent screen 4. Backend returns session cookie 5. Dashboard loads | URL changes to `/dashboard`; auth cookie set; user greeting displayed |

## REQ-002 - Product Search (Standard)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-002-E2E-POS | REQ-002 | User searches for a product and sees results list | 1. From dashboard, type `laptop` into search bar 2. Press Enter 3. Results page loads | List of products containing "laptopaEUR; first result clickable |

## REQ-003 - Typo-Tolerant Search (Fuzzy)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-003-E2E-POS | REQ-003 | Miss-spelled query still returns relevant product | 1. Search bar entry `lapto` 2. Submit 3. Observe results | Results include the "Laptop product despite typo |

## REQ-004 - Wishlist Persistence
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-004-E2E-POS | REQ-004 | Add product to wishlist, reload page, still present | 1. Open product `p5` page 2. Click **Add to Wishlist** 3. Refresh browser 4. Open wishlist page | Wishlist shows `p5`; no duplicate entries |

## REQ-005 - Personalized Suggestions Flow
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-005-E2E-POS | REQ-005 | Suggestions adapt to recent activity | 1. View several products in category "electronics" 2. Return to homepage 3. Observe suggestions carousel | Carousel contains at least three of the recently viewed items, ranked higher than unrelated items |

## REQ-006 - Smart Suggestions Engine (Multi-Signal)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-006-E2E-POS | REQ-006 | Smart suggestions reflect wishlist, search, purchase signals | 1. Add `p10` to wishlist 2. Search for `headphones` 3. Simulate purchase of `p15` 4. Open **Smart Suggestions** page | `p10` appears near top of list, followed by items matching search term, then recent purchases |

## REQ-007 - Recently Viewed Products
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-007-E2E-POS | REQ-007 | Recent view list shows correct ordering after navigation
| 1. Visit product `p1` 2. Visit `p2` 3. Visit `p3` 4. Open **Recently Viewed** page | List shows `[p3, p2, p1]` in that order |

## REQ-008 - Shopping Cart - Add & Update Quantity
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-008-E2E-POS | REQ-008 | Add item, increase quantity, verify totals | 1. Open product `p7` 2. Click **Add to Cart** (qty 1) 3. Open cart, change qty to 3 4. Verify subtotal updates | Cart shows quantity 3, price multiplied accordingly; stock reduced by 3 |

## REQ-009 - Shopping Cart - Out-of-Stock Protection
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-009-E2E-NEG | REQ-008 | Attempt to add out-of-stock product blocks action | 1. Open product `p99` (stock 0) 2. Click **Add to Cart** | UI shows error "Product out of stockaEUR, cart unchanged |

## REQ-010 - Order Management - Checkout Flow
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-010-E2E-POS | REQ-009 | Complete checkout creates order and redirects to confirmation | 1. Ensure cart has items 2. Click **Checkout** 3. Fill mock payment details (payment provider stub) 4. Submit 5. Observe redirect | Order record created, order confirmation page shows order ID, email receipt sent (stub) |

## REQ-011 - Order Management - Cancel Order (User)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-011-E2E-POS | REQ-009 | User cancels a pending order before shipment | 1. Open **My Orders** 2. Select order status **Pending** 3. Click **Cancel** 4. Confirm 5. Verify status changes to **Cancelled** | Order status updated; inventory restored; cancellation email sent |

## REQ-012 - Product Management (Admin) - Create Product
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-012-E2E-POS | REQ-010 | Admin creates new product with image upload
| 1. Login as admin 2. Navigate to **Admin a+' Products** 3. Click **Add Product**, fill fields, attach image, submit | New product appears in product list with generated ID; image stored; API returns 201 |

## REQ-013 - Inventory Management (Admin) - Bulk Stock Update
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-013-E2E-POS | REQ-011 | Admin uploads CSV to update stock levels for multiple SKUs | 1. Admin page **Inventory** 2. Upload CSV with rows `p10,5` `p11,-3` 3. Submit 4. Verify stock values in DB | Stock for `p10` increased by 5, `p11` decreased (error if would go negative) and UI shows success message |

## REQ-014 - Analytics Dashboard (Admin) - View KPIs
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-014-E2E-POS | REQ-013 | Admin accesses analytics dashboard for last 30 days
| 1. Login as admin 2. Navigate to **Analytics** 3. Select date range **30d** 4. View KPI cards | Cards display total sales, order count, new user count matching seeded data |

## REQ-016 - Performance - Search Latency Under Load (E2E Simulated)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-016-E2E-POS | REQ-016 | Run load test against search endpoint and verify response time <= 300 ms
| 1. Use Locust script to fire 200 concurrent GET `/api/search?q=phone` 2. Collect response times | 95 % of responses <= 300 ms; no HTTP 500 errors |

## REQ-018 - Security - HTTPS Enforcement
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-018-E2E-POS | REQ-018 | Access site over HTTP and confirm redirect to HTTPS
| 1. Open `http://d:/website` in browser 2. Observe network response | 301 redirect to `https://...`; final page loads over TLS 1.2 |

## REQ-020 - Scalability - High Concurrency Read (E2E)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-020-E2E-POS | REQ-020 | Simulate 100 k concurrent reads of product catalog and verify latency
| 1. Run k6 script targeting `/api/products` with 100 k virtual users 2. Record p95 latency | p95 <= 200 ms; error rate 0 % |

## REQ-026 - Edge - Offline Mode UI
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-026-E2E-POS | REQ-026 | Browser offline, trigger API call, UI shows retry banner
| 1. Open app, enable "Offline in devtools Network 2. Click a navigation that requires API 3. Observe UI | Banner "You are offline - retry appears; retry button re-issues request when back online |

## REQ-027 - Edge - Checkout Blocked When Cart Contains Out-of-Stock SKU
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-027-E2E-NEG | REQ-027 | Add out-of-stock item, attempt checkout, verify block
| 1. Add product `p99` (stock 0) to cart 2. Click **Checkout** 3. Observe error | Checkout aborts, UI shows "Product out of stock message; cart unchanged |

## REQ-028 - Edge - Misspelling Search Recovery
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-028-E2E-POS | REQ-028 | User types typo, system suggests correct product
| 1. Search `lapto` 2. Verify results include "Laptop | Results contain the correct product despite typo |

## REQ-029 - Edge - Duplicate Wishlist Idempotency
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-029-E2E-POS | REQ-029 | Adding same product twice does not duplicate entry
| 1. Add `p33` to wishlist 2. Add `p33` again 3. Open wishlist | Wishlist shows a single entry for `p33` |

## REQ-030 - Edge - Concurrent Inventory Updates
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-030-E2E-POS | REQ-030 | Two admins decrement stock concurrently, final stock never negative
| 1. Set product `p10` stock = 5 2. Admin A sends `delta=-3` 3. Admin B sends `delta=-4` simultaneously 4. Verify final stock and error responses | One request succeeds (stock becomes 2); other fails with `Error: NegativeStockError`; stock never below 0 |
