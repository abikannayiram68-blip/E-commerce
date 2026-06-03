File: /project-prompts/TESTS/INTEGRATION_TESTS.md
# Integration Tests

## REQ-001 - Google Authentication
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-001-IT-POS | REQ-001 | Front-end login triggers OAuth flow and creates session | 1. Click "Sign in with Google 2. Mock Google consent 3. Backend exchanges code for token 4. Session cookie set | User redirected to dashboard, `auth_token` cookie present |

## REQ-002 - Product Search
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-002-IT-POS | REQ-002 | API returns correct results for query | 1. GET `/api/search?q=phone` 2. Service queries DB 3. Returns JSON | Status 200, body contains array of products with "phone in name/description |

## REQ-003 - Typo-Tolerant Search
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-003-IT-POS | REQ-003 | Fuzzy matching returns relevant product despite typo | 1. GET `/api/search?q=iphnoe` 2. Service uses fuzzy search 3. Returns matches | Status 200, results include "iPhone |

## REQ-004 - Wishlist
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-004-IT-POS | REQ-004 | Adding to wishlist persists and is idempotent | 1. POST `/api/users/u1/wishlist` `{productId:"p5"}` a+' 201 2. Repeat same POST a+' 200 with "already in wishlist | DB contains single entry for `p5` |

## REQ-005 - Personalized Suggestions
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-005-IT-POS | REQ-005 | Suggestion service aggregates data from search history, wishlist, purchases | 1. Seed mock user data a+' 2. GET `/api/users/u1/suggestions` | JSON array of 5-10 products, higher rank for wishlist items |

## REQ-006 - Smart Suggestions Engine
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-006-IT-POS | REQ-006 | Engine combines five signals for ranking | 1. Populate DB with wishlist, search logs, recent views, purchases, trending 2. GET `/api/users/u2/smart-suggestions` | Ranked list where items appearing in multiple signals are higher |

## REQ-007 - Recently Viewed Products
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-007-IT-POS | REQ-007 | Recent-view endpoint returns correct ordering | 1. Simulate user viewing p1, p2, p3 2. GET `/api/users/u3/recent` | `[p3, p2, p1]` |

## REQ-008 - Shopping Cart
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-008-IT-POS | REQ-008 | Add item updates cart session and respects stock | 1. GET product stock=10 2. POST `/api/cart` `{productId:"p7", qty:3}` 3. Verify cart qty=3 and stock reduced to 7 | Status 200, response `{cartQty:3}` |

## REQ-009 - Order Management (User)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-009-IT-POS | REQ-009 | User fetches order history, data includes status and totals | 1. GET `/api/users/u4/orders` 2. DB returns order rows 3. Verify JSON fields | 200, array with `{orderId, status, total, date}` |

## REQ-010 - Product Management (Admin)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-010-IT-POS | REQ-010 | Admin creates product, image upload stored, DB record created | 1. POST `/api/admin/products` multipart (data + image) a+' 201 2. Verify DB entry and image saved | 201, response contains new `productId` |

## REQ-011 - Inventory Management (Admin)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-011-IT-POS | REQ-011 | Stock update endpoint correctly persists change | 1. PATCH `/api/admin/products/p10/stock` `{delta:-5}` a+' 200 2. Verify DB stock reduced | 200, new stock value returned |

## REQ-012 - Order Management (Admin)
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-012-IT-POS | REQ-012 | Admin changes order status, notification sent | 1. PUT `/api/admin/orders/o123/status` `{status:"Shipped"}` 2. DB updates 3. Email service invoked | 200, email queue receives "order shipped |

## REQ-013 - Analytics Dashboard
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-013-IT-POS | REQ-013 | Dashboard aggregates sales, orders, users for given period | 1. Seed DB with transactions for last month 2. GET `/api/admin/analytics?range=30d` | 200, `{sales:..., orders:..., newUsers:...}` |

## REQ-016 - Performance
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-016-IT-POS | REQ-016 | Search API latency under load <= 300 ms | 1. Run JMeter: 200 concurrent GET `/api/search?q=phone` | Avg = 254 ms, max <= 300 ms |

## REQ-018 - Security
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-018-IT-POS | REQ-018 | All endpoints enforce HTTPS | 1. Send HTTP request to any endpoint a+' 301 redirect to HTTPS | Redirect to HTTPS URL |

## REQ-020 - Scalability
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-020-IT-POS | REQ-020 | System serves 100 k concurrent read requests <= 200 ms | 1. Locust simulation: 100 k users GET `/api/products` | p95 latency = 180 ms, no errors |

## REQ-026 - Edge: Internet Outage
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-026-IT-POS | REQ-026 | Front-end detects offline and shows retry UI | 1. Disable network in browser 2. Trigger API call 3. Observe UI | "You are offline - retry banner displayed |

## REQ-027 - Edge: Out-of-Stock Purchase
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-027-IT-POS | REQ-027 | Checkout blocked when cart contains out-of-stock SKU | 1. Add product with stock = 0 to cart 2. Attempt checkout | 400 error, message "Product out of stock |

## REQ-028 - Edge: Search Misspelling
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-028-IT-POS | REQ-028 | Fuzzy search returns correct product despite typo | 1. GET `/api/search?q=lapto` 2. Verify results include "Laptop | 200, array contains "Laptop |

## REQ-029 - Edge: Duplicate Wishlist Entry
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-029-IT-POS | REQ-029 | Wishlist add idempotent response | 1. POST `/api/users/u1/wishlist` `{productId:"p33"}` a+' 201 2. Repeat POST a+' 200 "already in wishlist | Single DB entry for `p33` |

## REQ-030 - Edge: Concurrent Inventory Updates
| TEST-ID | REQ-ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-030-IT-POS | REQ-030 | Two admin requests decrement stock concurrently, final stock never negative | 1. Stock=5 2. Send two PATCH `/api/admin/products/p10/stock` `{delta:-3}` and `{delta:-4}` simultaneously 3. Verify outcome | One succeeds (stock = 2), other fails with `Error: NegativeStockError` |
