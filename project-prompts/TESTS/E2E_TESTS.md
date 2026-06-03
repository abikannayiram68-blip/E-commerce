File: /project-prompts/TESTS/E2E_TESTS.md
# Endâ€‘toâ€‘End Tests

## REQâ€‘001 â€“ Google Authentication (User Login Flow)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘001â€‘E2Eâ€‘POS | REQâ€‘001 | Complete login via Google and landing on dashboard | 1. Navigate to `/login` 2. Click **Sign in with Google** 3. Mock Google consent screen 4. Backend returns session cookie 5. Dashboard loads | URL changes to `/dashboard`; auth cookie set; user greeting displayed |

## REQâ€‘002 â€“ Product Search (Standard)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘002â€‘E2Eâ€‘POS | REQâ€‘002 | User searches for a product and sees results list | 1. From dashboard, type `laptop` into search bar 2. Press Enter 3. Results page loads | List of products containing â€œlaptopâ€; first result clickable |

## REQâ€‘003 â€“ Typoâ€‘Tolerant Search (Fuzzy)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘003â€‘E2Eâ€‘POS | REQâ€‘003 | Missâ€‘spelled query still returns relevant product | 1. Search bar entry `lapto` 2. Submit 3. Observe results | Results include the â€œLaptopâ€ product despite typo |

## REQâ€‘004 â€“ Wishlist Persistence
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘004â€‘E2Eâ€‘POS | REQâ€‘004 | Add product to wishlist, reload page, still present | 1. Open product `p5` page 2. Click **Add to Wishlist** 3. Refresh browser 4. Open wishlist page | Wishlist shows `p5`; no duplicate entries |

## REQâ€‘005 â€“ Personalized Suggestions Flow
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘005â€‘E2Eâ€‘POS | REQâ€‘005 | Suggestions adapt to recent activity | 1. View several products in category â€œelectronics" 2. Return to homepage 3. Observe suggestions carousel | Carousel contains at least three of the recently viewed items, ranked higher than unrelated items |

## REQâ€‘006 â€“ Smart Suggestions Engine (Multiâ€‘Signal)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘006â€‘E2Eâ€‘POS | REQâ€‘006 | Smart suggestions reflect wishlist, search, purchase signals | 1. Add `p10` to wishlist 2. Search for `headphones` 3. Simulate purchase of `p15` 4. Open **Smart Suggestions** page | `p10` appears near top of list, followed by items matching search term, then recent purchases |

## REQâ€‘007 â€“ Recently Viewed Products
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘007â€‘E2Eâ€‘POS | REQâ€‘007 | Recent view list shows correct ordering after navigation
| 1. Visit product `p1` 2. Visit `p2` 3. Visit `p3` 4. Open **Recently Viewed** page | List shows `[p3, p2, p1]` in that order |

## REQâ€‘008 â€“ Shopping Cart â€“ Add & Update Quantity
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘008â€‘E2Eâ€‘POS | REQâ€‘008 | Add item, increase quantity, verify totals | 1. Open product `p7` 2. Click **Add to Cart** (qtyâ€¯1) 3. Open cart, change qty toâ€¯3 4. Verify subtotal updates | Cart shows quantityâ€¯3, price multiplied accordingly; stock reduced byâ€¯3 |

## REQâ€‘009 â€“ Shopping Cart â€“ Outâ€‘ofâ€‘Stock Protection
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘009â€‘E2Eâ€‘NEG | REQâ€‘008 | Attempt to add outâ€‘ofâ€‘stock product blocks action | 1. Open product `p99` (stockâ€¯0) 2. Click **Add to Cart** | UI shows error â€œProduct out of stockâ€, cart unchanged |

## REQâ€‘010 â€“ Order Management â€“ Checkout Flow
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘010â€‘E2Eâ€‘POS | REQâ€‘009 | Complete checkout creates order and redirects to confirmation | 1. Ensure cart has items 2. Click **Checkout** 3. Fill mock payment details (payment provider stub) 4. Submit 5. Observe redirect | Order record created, order confirmation page shows order ID, email receipt sent (stub) |

## REQâ€‘011 â€“ Order Management â€“ Cancel Order (User)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘011â€‘E2Eâ€‘POS | REQâ€‘009 | User cancels a pending order before shipment | 1. Open **My Orders** 2. Select order status **Pending** 3. Click **Cancel** 4. Confirm 5. Verify status changes to **Cancelled** | Order status updated; inventory restored; cancellation email sent |

## REQâ€‘012 â€“ Product Management (Admin) â€“ Create Product
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘012â€‘E2Eâ€‘POS | REQâ€‘010 | Admin creates new product with image upload
| 1. Login as admin 2. Navigate to **Admin â†’ Products** 3. Click **Add Product**, fill fields, attach image, submit | New product appears in product list with generated ID; image stored; API returns 201 |

## REQâ€‘013 â€“ Inventory Management (Admin) â€“ Bulk Stock Update
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘013â€‘E2Eâ€‘POS | REQâ€‘011 | Admin uploads CSV to update stock levels for multiple SKUs | 1. Admin page **Inventory** 2. Upload CSV with rows `p10,5` `p11,-3` 3. Submit 4. Verify stock values in DB | Stock for `p10` increased byâ€¯5, `p11` decreased (error if would go negative) and UI shows success message |

## REQâ€‘014 â€“ Analytics Dashboard (Admin) â€“ View KPIs
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘014â€‘E2Eâ€‘POS | REQâ€‘013 | Admin accesses analytics dashboard for last 30â€¯days
| 1. Login as admin 2. Navigate to **Analytics** 3. Select date range **30d** 4. View KPI cards | Cards display total sales, order count, new user count matching seeded data |

## REQâ€‘016 â€“ Performance â€“ Search Latency Under Load (E2E Simulated)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘016â€‘E2Eâ€‘POS | REQâ€‘016 | Run load test against search endpoint and verify response time â‰¤â€¯300â€¯ms
| 1. Use Locust script to fire 200 concurrent GET `/api/search?q=phone` 2. Collect response times | 95â€¯% of responses â‰¤â€¯300â€¯ms; no HTTPâ€¯500 errors |

## REQâ€‘018 â€“ Security â€“ HTTPS Enforcement
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘018â€‘E2Eâ€‘POS | REQâ€‘018 | Access site over HTTP and confirm redirect to HTTPS
| 1. Open `http://d:/website` in browser 2. Observe network response | 301 redirect to `https://...`; final page loads over TLSâ€¯1.2 |

## REQâ€‘020 â€“ Scalability â€“ High Concurrency Read (E2E)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘020â€‘E2Eâ€‘POS | REQâ€‘020 | Simulate 100â€¯k concurrent reads of product catalog and verify latency
| 1. Run k6 script targeting `/api/products` with 100â€¯k virtual users 2. Record p95 latency | p95 â‰¤â€¯200â€¯ms; error rate 0â€¯% |

## REQâ€‘026 â€“ Edge â€“ Offline Mode UI
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘026â€‘E2Eâ€‘POS | REQâ€‘026 | Browser offline, trigger API call, UI shows retry banner
| 1. Open app, enable â€œOfflineâ€ in devtools Network 2. Click a navigation that requires API 3. Observe UI | Banner â€œYou are offline â€“ retryâ€ appears; retry button reâ€‘issues request when back online |

## REQâ€‘027 â€“ Edge â€“ Checkout Blocked When Cart Contains Outâ€‘ofâ€‘Stock SKU
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘027â€‘E2Eâ€‘NEG | REQâ€‘027 | Add outâ€‘ofâ€‘stock item, attempt checkout, verify block
| 1. Add product `p99` (stockâ€¯0) to cart 2. Click **Checkout** 3. Observe error | Checkout aborts, UI shows â€œProduct out of stockâ€ message; cart unchanged |

## REQâ€‘028 â€“ Edge â€“ Misspelling Search Recovery
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘028â€‘E2Eâ€‘POS | REQâ€‘028 | User types typo, system suggests correct product
| 1. Search `lapto` 2. Verify results include â€œLaptopâ€ | Results contain the correct product despite typo |

## REQâ€‘029 â€“ Edge â€“ Duplicate Wishlist Idempotency
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘029â€‘E2Eâ€‘POS | REQâ€‘029 | Adding same product twice does not duplicate entry
| 1. Add `p33` to wishlist 2. Add `p33` again 3. Open wishlist | Wishlist shows a single entry for `p33` |

## REQâ€‘030 â€“ Edge â€“ Concurrent Inventory Updates
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘030â€‘E2Eâ€‘POS | REQâ€‘030 | Two admins decrement stock concurrently, final stock never negative
| 1. Set product `p10` stockâ€¯=â€¯5 2. Admin A sends `delta=-3` 3. Admin B sends `delta=-4` simultaneously 4. Verify final stock and error responses | One request succeeds (stock becomesâ€¯2); other fails with `Error: NegativeStockError`; stock never belowâ€¯0 |
