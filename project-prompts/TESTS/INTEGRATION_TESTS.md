File: /project-prompts/TESTS/INTEGRATION_TESTS.md
# Integration Tests

## REQâ€‘001 â€“ Google Authentication
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘001â€‘ITâ€‘POS | REQâ€‘001 | Frontâ€‘end login triggers OAuth flow and creates session | 1. Click â€œSign in with Googleâ€ 2. Mock Google consent 3. Backend exchanges code for token 4. Session cookie set | User redirected to dashboard, `auth_token` cookie present |

## REQâ€‘002 â€“ Product Search
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘002â€‘ITâ€‘POS | REQâ€‘002 | API returns correct results for query | 1. GET `/api/search?q=phone` 2. Service queries DB 3. Returns JSON | Statusâ€¯200, body contains array of products with â€œphoneâ€ in name/description |

## REQâ€‘003 â€“ Typoâ€‘Tolerant Search
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘003â€‘ITâ€‘POS | REQâ€‘003 | Fuzzy matching returns relevant product despite typo | 1. GET `/api/search?q=iphnoe` 2. Service uses fuzzy search 3. Returns matches | Statusâ€¯200, results include â€œiPhoneâ€ |

## REQâ€‘004 â€“ Wishlist
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘004â€‘ITâ€‘POS | REQâ€‘004 | Adding to wishlist persists and is idempotent | 1. POST `/api/users/u1/wishlist` `{productId:"p5"}` â†’ 201 2. Repeat same POST â†’ 200 with â€œalready in wishlistâ€ | DB contains single entry for `p5` |

## REQâ€‘005 â€“ Personalized Suggestions
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘005â€‘ITâ€‘POS | REQâ€‘005 | Suggestion service aggregates data from search history, wishlist, purchases | 1. Seed mock user data â†’ 2. GET `/api/users/u1/suggestions` | JSON array of 5â€‘10 products, higher rank for wishlist items |

## REQâ€‘006 â€“ Smart Suggestions Engine
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘006â€‘ITâ€‘POS | REQâ€‘006 | Engine combines five signals for ranking | 1. Populate DB with wishlist, search logs, recent views, purchases, trending 2. GET `/api/users/u2/smart-suggestions` | Ranked list where items appearing in multiple signals are higher |

## REQâ€‘007 â€“ Recently Viewed Products
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘007â€‘ITâ€‘POS | REQâ€‘007 | Recentâ€‘view endpoint returns correct ordering | 1. Simulate user viewing p1, p2, p3 2. GET `/api/users/u3/recent` | `[p3, p2, p1]` |

## REQâ€‘008 â€“ Shopping Cart
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘008â€‘ITâ€‘POS | REQâ€‘008 | Add item updates cart session and respects stock | 1. GET product stock=10 2. POST `/api/cart` `{productId:"p7", qty:3}` 3. Verify cart qty=3 and stock reduced to 7 | Statusâ€¯200, response `{cartQty:3}` |

## REQâ€‘009 â€“ Order Management (User)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘009â€‘ITâ€‘POS | REQâ€‘009 | User fetches order history, data includes status and totals | 1. GET `/api/users/u4/orders` 2. DB returns order rows 3. Verify JSON fields | 200, array with `{orderId, status, total, date}` |

## REQâ€‘010 â€“ Product Management (Admin)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘010â€‘ITâ€‘POS | REQâ€‘010 | Admin creates product, image upload stored, DB record created | 1. POST `/api/admin/products` multipart (data + image) â†’ 201 2. Verify DB entry and image saved | 201, response contains new `productId` |

## REQâ€‘011 â€“ Inventory Management (Admin)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘011â€‘ITâ€‘POS | REQâ€‘011 | Stock update endpoint correctly persists change | 1. PATCH `/api/admin/products/p10/stock` `{delta:-5}` â†’ 200 2. Verify DB stock reduced | 200, new stock value returned |

## REQâ€‘012 â€“ Order Management (Admin)
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘012â€‘ITâ€‘POS | REQâ€‘012 | Admin changes order status, notification sent | 1. PUT `/api/admin/orders/o123/status` `{status:"Shipped"}` 2. DB updates 3. Email service invoked | 200, email queue receives â€œorder shippedâ€ |

## REQâ€‘013 â€“ Analytics Dashboard
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘013â€‘ITâ€‘POS | REQâ€‘013 | Dashboard aggregates sales, orders, users for given period | 1. Seed DB with transactions for last month 2. GET `/api/admin/analytics?range=30d` | 200, `{sales:..., orders:..., newUsers:...}` |

## REQâ€‘016 â€“ Performance
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘016â€‘ITâ€‘POS | REQâ€‘016 | Search API latency under load â‰¤â€¯300â€¯ms | 1. Run JMeter: 200 concurrent GET `/api/search?q=phone` | Avgâ€¯=â€¯254â€¯ms, maxâ€¯â‰¤â€¯300â€¯ms |

## REQâ€‘018 â€“ Security
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘018â€‘ITâ€‘POS | REQâ€‘018 | All endpoints enforce HTTPS | 1. Send HTTP request to any endpoint â†’ 301 redirect to HTTPS | Redirect to HTTPS URL |

## REQâ€‘020 â€“ Scalability
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘020â€‘ITâ€‘POS | REQâ€‘020 | System serves 100â€¯k concurrent read requests â‰¤â€¯200â€¯ms | 1. Locust simulation: 100â€¯k users GET `/api/products` | p95 latencyâ€¯=â€¯180â€¯ms, no errors |

## REQâ€‘026 â€“ Edge: Internet Outage
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘026â€‘ITâ€‘POS | REQâ€‘026 | Frontâ€‘end detects offline and shows retry UI | 1. Disable network in browser 2. Trigger API call 3. Observe UI | â€œYou are offline â€“ retryâ€ banner displayed |

## REQâ€‘027 â€“ Edge: Outâ€‘ofâ€‘Stock Purchase
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘027â€‘ITâ€‘POS | REQâ€‘027 | Checkout blocked when cart contains outâ€‘ofâ€‘stock SKU | 1. Add product with stockâ€¯=â€¯0 to cart 2. Attempt checkout | 400 error, message â€œProduct out of stockâ€ |

## REQâ€‘028 â€“ Edge: Search Misspelling
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘028â€‘ITâ€‘POS | REQâ€‘028 | Fuzzy search returns correct product despite typo | 1. GET `/api/search?q=lapto` 2. Verify results include â€œLaptopâ€ | 200, array contains â€œLaptopâ€ |

## REQâ€‘029 â€“ Edge: Duplicate Wishlist Entry
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘029â€‘ITâ€‘POS | REQâ€‘029 | Wishlist add idempotent response | 1. POST `/api/users/u1/wishlist` `{productId:"p33"}` â†’ 201 2. Repeat POST â†’ 200 â€œalready in wishlistâ€ | Single DB entry for `p33` |

## REQâ€‘030 â€“ Edge: Concurrent Inventory Updates
| TESTâ€‘ID | REQâ€‘ID | Description | Steps | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘030â€‘ITâ€‘POS | REQâ€‘030 | Two admin requests decrement stock concurrently, final stock never negative | 1. Stock=5 2. Send two PATCH `/api/admin/products/p10/stock` `{delta:-3}` and `{delta:-4}` simultaneously 3. Verify outcome | One succeeds (stockâ€¯=â€¯2), other fails with `Error: NegativeStockError` |
