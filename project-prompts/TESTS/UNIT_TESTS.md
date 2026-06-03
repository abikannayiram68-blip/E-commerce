File: /project-prompts/TESTS/UNIT_TESTS.md
# Unit Tests

## REQ-001 - Google Authentication
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-001-UT-POS | REQ-001 | Valid Google OAuth token returns user profile | `token = "valid-google-token"` (mock) | `{ id: "user123", email: "user@example.com", name: "Test User" }` |
| TEST-001-UT-NEG | REQ-001 | Invalid token throws error | `token = "invalid-token"` (mock) | `Error: InvalidTokenError` |

## REQ-002 - Product Search
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-002-UT-POS | REQ-002 | Returns matching products | `query = "laptop"` | Array of products whose name/description contains "laptop |
| TEST-002-UT-NEG | REQ-002 | Empty query returns empty list | `query = ""` | `[]` |

## REQ-003 - Typo-Tolerant Search
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-003-UT-POS | REQ-003 | One-typo query returns relevant results | `query = "lapto"` | Same array as for "laptop |
| TEST-003-UT-NEG | REQ-003 | Query beyond tolerance returns none | `query = "lptp"` | `[]` |

## REQ-004 - Wishlist
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-004-UT-POS | REQ-004 | Adding new product creates entry | `userId="u1", productId="p42"` | Wishlist now contains `{ productId:"p42" }` |
| TEST-004-UT-NEG | REQ-004 | Duplicate add does not duplicate | Wishlist already has `p42`; add again | Wishlist still size = 1, unchanged |

## REQ-005 - Personalized Suggestions
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-005-UT-POS | REQ-005 | Generates suggestions from user history | `userId="u1"` (history includes "electronicsaEUR) | List of 5-10 product objects from "electronics with high relevance scores |

## REQ-006 - Smart Suggestions Engine
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-006-UT-POS | REQ-006 | Combines multiple signals for ranking | `userId="u2"` (has wishlist, recent views, purchases, trending data) | Ranked list where items appearing in more signals have higher rank |

## REQ-007 - Recently Viewed Products
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-007-UT-POS | REQ-007 | Returns up to 10 most recent | `userId="u3"` (view history of 15 items) | Array of the 10 most recent product IDs, newest first |

## REQ-008 - Shopping Cart
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-008-UT-POS | REQ-008 | Add item increases quantity | `cartId="c1", productId="p5", qty=1` (cart empty) | Cart contains `{ productId:"p5", qty:1 }` |
| TEST-008-UT-NEG | REQ-008 | Adding out-of-stock product throws error | `productId="p99"` where stock = 0 | `Error: OutOfStockError` |
| TEST-008-UT-BOUNDARY | REQ-008 | Quantity limit enforced (max 99) | `qty=150` for a product | `Error: QuantityExceedsLimit` |

## REQ-009 - Order Management (User)
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-009-UT-POS | REQ-009 | Retrieve order list for user | `userId="u4"` | Array of order objects with `status`, `date`, `total` |

## REQ-010 - Product Management (Admin)
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-010-UT-POS | REQ-010 | Create new product succeeds | `product={name:"Widget",price:9.99,stock:100}` | New product ID returned, product stored |
| TEST-010-UT-NEG | REQ-010 | Missing name triggers validation error | `product={price:9.99,stock:100}` | `Error: ValidationError` |

## REQ-011 - Inventory Management (Admin)
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-011-UT-POS | REQ-011 | Update stock reduces correctly | `productId="p10", delta=-5` (stock 20) | New stock = 15 |
| TEST-011-UT-NEG | REQ-011 | Updating to negative stock errors | `productId="p10", delta=-30` (stock 20) | `Error: NegativeStockError` |

## REQ-012 - Order Management (Admin)
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-012-UT-POS | REQ-012 | Change status from Processing a+' Shipped | `orderId="o123", newStatus="Shipped"` | Status updated, timestamp recorded |
| TEST-012-UT-NEG | REQ-012 | Invalid status transition rejected | `orderId="o123", newStatus="Created"` (already Shipped) | `Error: InvalidStatusTransition` |

## REQ-013 - Analytics Dashboard
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-013-UT-POS | REQ-013 | KPI aggregation returns correct totals | `dateRange=last30d` | `{ sales:$12345, orders:256, newUsers:78 }` |

## REQ-014 - Usability (Non-Functional)
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-014-UT-POS | REQ-014 | First-time user navigation time <= 5 min | Simulated user flow timing | `averageTime = 4.3 min` (passes) |

## REQ-015 - Accessibility (Non-Functional)
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-015-UT-POS | REQ-015 | Axe core audit finds zero violations | Run Axe on main page | `0 violations` |

## REQ-016 - Performance (Non-Functional)
| TEST-ID | REQ-ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TEST-016-UT-POS | REQ-016 | Page load <= 2 s on 3G throttling | Lighthouse on homepage (3G) | `loadTime = 1.8 s`, score >= 90 |
| TEST-016-UT-BOUNDARY | REQ-016 | Search response <= 300 ms under load | 100 concurrent search requests | 95 % <= 300 ms, max = 298 ms |

## REQ-017 - Reliability (Non-Functional)
| TEST-001-UT-POS | REQ-017 | Simulated month-long uptime >= 99.9 % | Availability logs | `uptime = 99.95 %` |

## REQ-018 - Security (Non-Functional)
| TEST-018-UT-POS | REQ-018 | TLS 1.2 connection accepted | HTTPS request with TLS 1.2 | `200 OK` |
| TEST-018-UT-NEG | REQ-018 | TLS 1.0 rejected | HTTPS request with TLS 1.0 | `Error: UnsupportedProtocol` |

## REQ-019 - Privacy (Non-Functional)
| TEST-019-UT-POS | REQ-019 | Consent flag saved before tracking | User clicks "Accept on consent banner | DB entry `{ userId, consent:true }` |

## REQ-020 - Scalability (Non-Functional)
| TEST-020-UT-POS | REQ-020 | Handles 100 k concurrent reads <= 200 ms | Load test 100 k virtual users (read-only) | `p95 latency = 180 ms`, no errors |
| TEST-020-UT-BOUNDARY | REQ-020 | Search on 50 k catalog <= 300 ms | Search query on full catalog | `responseTime = 295 ms` |

## REQ-021 - Maintainability (Non-Functional)
| TEST-021-UT-POS | REQ-021 | Unit-test coverage >= 80 % | Run `npm test -- --coverage` | `coverage = 84 %` |

## REQ-022 - Constraint: Single-Vendor Architecture
| TEST-022-UT-POS | REQ-022 | Reject third-party product | `product.vendor="external"` | `Error: SingleVendorConstraint` |

## REQ-023 - Constraint: Google-Only Authentication
| TEST-023-UT-POS | REQ-023 | Google login succeeds (reuses REQ-001) |
| TEST-023-UT-NEG | REQ-023 | Email/password login rejected | `email="a@b.com", password="pwd"` | `Error: UnsupportedAuthMethod` |

## REQ-024 - Constraint: No Multi-Vendor Marketplace
| TEST-024-UT-POS | REQ-024 | Enabling seller portal fails | `enableSellerPortal=true` | `Error: FeatureDisabled` |

## REQ-025 - Constraint: Payment Integration
| TEST-025-UT-POS | REQ-025 | Payment request forwarded to provider | `orderId="o200", amount=49.99` | Redirect URL to provider, no card data stored |

## REQ-026 - Edge: Internet Outage
| TEST-026-UT-POS | REQ-026 | Offline detection shows retry UI | Simulate network offline during API call | UI displays "You are offline - retry banner |

## REQ-027 - Edge: Out-of-Stock Purchase
| TEST-027-UT-POS | REQ-027 | Adding out-of-stock to cart blocked | `productId="p99"` with stock = 0 | `Error: OutOfStockError` |
| TEST-027-UT-NEG | REQ-027 | Checkout blocked when cart contains out-of-stock | Cart has out-of-stock item, attempt checkout | Checkout button disabled, error "Product out of stock |

## REQ-028 - Edge: Search Misspelling
| TEST-028-UT-POS | REQ-028 | Misspelled query "lapto returns "laptop | `query="lapto"` | Results include "Laptop product |
| TEST-028-UT-NEG | REQ-028 | Unrelated typo returns empty set | `query="zxq"` | `[]` |

## REQ-029 - Edge: Duplicate Wishlist Entry
| TEST-029-UT-POS | REQ-029 | Second add ignored | Wishlist already has `p33`; add again | Wishlist size still = 1, unchanged |
| TEST-029-UT-NEG | REQ-029 | (Negative case not applicable) - placeholder |

## REQ-030 - Edge: Concurrent Inventory Updates
| TEST-030-UT-POS | REQ-030 | Two admins decrement stock concurrently, final stock never negative | Stock = 5; Admin A delta-3, Admin B delta-4 concurrently | Final stock = 0; one request succeeds, other fails `Error: NegativeStockError` |
| TEST-030-UT-NEG | REQ-030 | (Negative case not applicable) - placeholder |

## REQ-031 - Assumption: Single-Vendor Business
| TEST-031-UT-POS | REQ-031 | System rejects multi-vendor product data | `product.vendor="multiple"` | `Error: SingleVendorConstraint` |

## REQ-032 - Assumption: Reliable Internet
| TEST-032-UT-POS | REQ-032 | Normal operations succeed when network available | All API calls with network up | Successful responses for each call |

## REQ-033 - Assumption: Accurate Inventory Data
| TEST-033-UT-POS | REQ-033 | Admin update reflected immediately | Admin sets stock=20; query returns 20 | Consistent result |

## REQ-034 - Assumption: PCI Compliance Handled Externally
| TEST-034-UT-POS | REQ-034 | No raw card data stored after payment | Complete checkout (mock provider) | Database contains no columns with raw card numbers |

## REQ-035 - Assumption: Google OAuth Configured
| TEST-035-UT-POS | REQ-035 | OAuth flow returns valid token | Simulated Google login | `token` present and non-null |
