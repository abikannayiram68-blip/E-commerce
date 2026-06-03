File: /project-prompts/TESTS/UNIT_TESTS.md
# Unit Tests

## REQâ€‘001 â€“ Google Authentication
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘001â€‘UTâ€‘POS | REQâ€‘001 | Valid Google OAuth token returns user profile | `token = "valid-google-token"` (mock) | `{ id: "user123", email: "user@example.com", name: "Test User" }` |
| TESTâ€‘001â€‘UTâ€‘NEG | REQâ€‘001 | Invalid token throws error | `token = "invalid-token"` (mock) | `Error: InvalidTokenError` |

## REQâ€‘002 â€“ Product Search
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘002â€‘UTâ€‘POS | REQâ€‘002 | Returns matching products | `query = "laptop"` | Array of products whose name/description contains â€œlaptopâ€ |
| TESTâ€‘002â€‘UTâ€‘NEG | REQâ€‘002 | Empty query returns empty list | `query = ""` | `[]` |

## REQâ€‘003 â€“ Typoâ€‘Tolerant Search
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘003â€‘UTâ€‘POS | REQâ€‘003 | Oneâ€‘typo query returns relevant results | `query = "lapto"` | Same array as for â€œlaptopâ€ |
| TESTâ€‘003â€‘UTâ€‘NEG | REQâ€‘003 | Query beyond tolerance returns none | `query = "lptp"` | `[]` |

## REQâ€‘004 â€“ Wishlist
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘004â€‘UTâ€‘POS | REQâ€‘004 | Adding new product creates entry | `userId="u1", productId="p42"` | Wishlist now contains `{ productId:"p42" }` |
| TESTâ€‘004â€‘UTâ€‘NEG | REQâ€‘004 | Duplicate add does not duplicate | Wishlist already has `p42`; add again | Wishlist still sizeâ€¯=â€¯1, unchanged |

## REQâ€‘005 â€“ Personalized Suggestions
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘005â€‘UTâ€‘POS | REQâ€‘005 | Generates suggestions from user history | `userId="u1"` (history includes â€œelectronicsâ€) | List of 5â€‘10 product objects from â€œelectronicsâ€ with high relevance scores |

## REQâ€‘006 â€“ Smart Suggestions Engine
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘006â€‘UTâ€‘POS | REQâ€‘006 | Combines multiple signals for ranking | `userId="u2"` (has wishlist, recent views, purchases, trending data) | Ranked list where items appearing in more signals have higher rank |

## REQâ€‘007 â€“ Recently Viewed Products
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘007â€‘UTâ€‘POS | REQâ€‘007 | Returns up to 10 most recent | `userId="u3"` (view history of 15 items) | Array of the 10 most recent product IDs, newest first |

## REQâ€‘008 â€“ Shopping Cart
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘008â€‘UTâ€‘POS | REQâ€‘008 | Add item increases quantity | `cartId="c1", productId="p5", qty=1` (cart empty) | Cart contains `{ productId:"p5", qty:1 }` |
| TESTâ€‘008â€‘UTâ€‘NEG | REQâ€‘008 | Adding outâ€‘ofâ€‘stock product throws error | `productId="p99"` where stockâ€¯=â€¯0 | `Error: OutOfStockError` |
| TESTâ€‘008â€‘UTâ€‘BOUNDARY | REQâ€‘008 | Quantity limit enforced (maxâ€¯99) | `qty=150` for a product | `Error: QuantityExceedsLimit` |

## REQâ€‘009 â€“ Order Management (User)
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘009â€‘UTâ€‘POS | REQâ€‘009 | Retrieve order list for user | `userId="u4"` | Array of order objects with `status`, `date`, `total` |

## REQâ€‘010 â€“ Product Management (Admin)
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘010â€‘UTâ€‘POS | REQâ€‘010 | Create new product succeeds | `product={name:"Widget",price:9.99,stock:100}` | New product ID returned, product stored |
| TESTâ€‘010â€‘UTâ€‘NEG | REQâ€‘010 | Missing name triggers validation error | `product={price:9.99,stock:100}` | `Error: ValidationError` |

## REQâ€‘011 â€“ Inventory Management (Admin)
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘011â€‘UTâ€‘POS | REQâ€‘011 | Update stock reduces correctly | `productId="p10", delta=-5` (stock 20) | New stock = 15 |
| TESTâ€‘011â€‘UTâ€‘NEG | REQâ€‘011 | Updating to negative stock errors | `productId="p10", delta=-30` (stock 20) | `Error: NegativeStockError` |

## REQâ€‘012 â€“ Order Management (Admin)
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘012â€‘UTâ€‘POS | REQâ€‘012 | Change status from Processing â†’ Shipped | `orderId="o123", newStatus="Shipped"` | Status updated, timestamp recorded |
| TESTâ€‘012â€‘UTâ€‘NEG | REQâ€‘012 | Invalid status transition rejected | `orderId="o123", newStatus="Created"` (already Shipped) | `Error: InvalidStatusTransition` |

## REQâ€‘013 â€“ Analytics Dashboard
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘013â€‘UTâ€‘POS | REQâ€‘013 | KPI aggregation returns correct totals | `dateRange=last30d` | `{ sales:$12345, orders:256, newUsers:78 }` |

## REQâ€‘014 â€“ Usability (Nonâ€‘Functional)
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘014â€‘UTâ€‘POS | REQâ€‘014 | Firstâ€‘time user navigation time â‰¤â€¯5â€¯min | Simulated user flow timing | `averageTime = 4.3â€¯min` (passes) |

## REQâ€‘015 â€“ Accessibility (Nonâ€‘Functional)
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘015â€‘UTâ€‘POS | REQâ€‘015 | Axe core audit finds zero violations | Run Axe on main page | `0 violations` |

## REQâ€‘016 â€“ Performance (Nonâ€‘Functional)
| TESTâ€‘ID | REQâ€‘ID | Description | Input | Expected Output |
|--------|--------|-------------|-------|-----------------|
| TESTâ€‘016â€‘UTâ€‘POS | REQâ€‘016 | Page load â‰¤â€¯2â€¯s on 3G throttling | Lighthouse on homepage (3G) | `loadTime = 1.8â€¯s`, scoreâ€¯â‰¥â€¯90 |
| TESTâ€‘016â€‘UTâ€‘BOUNDARY | REQâ€‘016 | Search response â‰¤â€¯300â€¯ms under load | 100 concurrent search requests | 95â€¯% â‰¤â€¯300â€¯ms, maxâ€¯=â€¯298â€¯ms |

## REQâ€‘017 â€“ Reliability (Nonâ€‘Functional)
| TESTâ€‘001â€‘UTâ€‘POS | REQâ€‘017 | Simulated monthâ€‘long uptime â‰¥â€¯99.9â€¯% | Availability logs | `uptime = 99.95â€¯%` |

## REQâ€‘018 â€“ Security (Nonâ€‘Functional)
| TESTâ€‘018â€‘UTâ€‘POS | REQâ€‘018 | TLSâ€¯1.2 connection accepted | HTTPS request with TLSâ€¯1.2 | `200 OK` |
| TESTâ€‘018â€‘UTâ€‘NEG | REQâ€‘018 | TLSâ€¯1.0 rejected | HTTPS request with TLSâ€¯1.0 | `Error: UnsupportedProtocol` |

## REQâ€‘019 â€“ Privacy (Nonâ€‘Functional)
| TESTâ€‘019â€‘UTâ€‘POS | REQâ€‘019 | Consent flag saved before tracking | User clicks â€œAcceptâ€ on consent banner | DB entry `{ userId, consent:true }` |

## REQâ€‘020 â€“ Scalability (Nonâ€‘Functional)
| TESTâ€‘020â€‘UTâ€‘POS | REQâ€‘020 | Handles 100â€¯k concurrent reads â‰¤â€¯200â€¯ms | Load test 100â€¯k virtual users (readâ€‘only) | `p95 latency = 180â€¯ms`, no errors |
| TESTâ€‘020â€‘UTâ€‘BOUNDARY | REQâ€‘020 | Search on 50â€¯k catalog â‰¤â€¯300â€¯ms | Search query on full catalog | `responseTime = 295â€¯ms` |

## REQâ€‘021 â€“ Maintainability (Nonâ€‘Functional)
| TESTâ€‘021â€‘UTâ€‘POS | REQâ€‘021 | Unitâ€‘test coverage â‰¥â€¯80â€¯% | Run `npm test -- --coverage` | `coverage = 84â€¯%` |

## REQâ€‘022 â€“ Constraint: Singleâ€‘Vendor Architecture
| TESTâ€‘022â€‘UTâ€‘POS | REQâ€‘022 | Reject thirdâ€‘party product | `product.vendor="external"` | `Error: SingleVendorConstraint` |

## REQâ€‘023 â€“ Constraint: Googleâ€‘Only Authentication
| TESTâ€‘023â€‘UTâ€‘POS | REQâ€‘023 | Google login succeeds (reuses REQâ€‘001) |
| TESTâ€‘023â€‘UTâ€‘NEG | REQâ€‘023 | Email/password login rejected | `email="a@b.com", password="pwd"` | `Error: UnsupportedAuthMethod` |

## REQâ€‘024 â€“ Constraint: No Multiâ€‘Vendor Marketplace
| TESTâ€‘024â€‘UTâ€‘POS | REQâ€‘024 | Enabling seller portal fails | `enableSellerPortal=true` | `Error: FeatureDisabled` |

## REQâ€‘025 â€“ Constraint: Payment Integration
| TESTâ€‘025â€‘UTâ€‘POS | REQâ€‘025 | Payment request forwarded to provider | `orderId="o200", amount=49.99` | Redirect URL to provider, no card data stored |

## REQâ€‘026 â€“ Edge: Internet Outage
| TESTâ€‘026â€‘UTâ€‘POS | REQâ€‘026 | Offline detection shows retry UI | Simulate network offline during API call | UI displays â€œYou are offline â€“ retryâ€ banner |

## REQâ€‘027 â€“ Edge: Outâ€‘ofâ€‘Stock Purchase
| TESTâ€‘027â€‘UTâ€‘POS | REQâ€‘027 | Adding outâ€‘ofâ€‘stock to cart blocked | `productId="p99"` with stockâ€¯=â€¯0 | `Error: OutOfStockError` |
| TESTâ€‘027â€‘UTâ€‘NEG | REQâ€‘027 | Checkout blocked when cart contains outâ€‘ofâ€‘stock | Cart has outâ€‘ofâ€‘stock item, attempt checkout | Checkout button disabled, error â€œProduct out of stockâ€ |

## REQâ€‘028 â€“ Edge: Search Misspelling
| TESTâ€‘028â€‘UTâ€‘POS | REQâ€‘028 | Misspelled query â€œlaptoâ€ returns â€œlaptopâ€ | `query="lapto"` | Results include â€œLaptopâ€ product |
| TESTâ€‘028â€‘UTâ€‘NEG | REQâ€‘028 | Unrelated typo returns empty set | `query="zxq"` | `[]` |

## REQâ€‘029 â€“ Edge: Duplicate Wishlist Entry
| TESTâ€‘029â€‘UTâ€‘POS | REQâ€‘029 | Second add ignored | Wishlist already has `p33`; add again | Wishlist size stillâ€¯=â€¯1, unchanged |
| TESTâ€‘029â€‘UTâ€‘NEG | REQâ€‘029 | (Negative case not applicable) â€“ placeholder |

## REQâ€‘030 â€“ Edge: Concurrent Inventory Updates
| TESTâ€‘030â€‘UTâ€‘POS | REQâ€‘030 | Two admins decrement stock concurrently, final stock never negative | Stockâ€¯=â€¯5; Admin A deltaâ€‘3, Admin B deltaâ€‘4 concurrently | Final stockâ€¯=â€¯0; one request succeeds, other fails `Error: NegativeStockError` |
| TESTâ€‘030â€‘UTâ€‘NEG | REQâ€‘030 | (Negative case not applicable) â€“ placeholder |

## REQâ€‘031 â€“ Assumption: Singleâ€‘Vendor Business
| TESTâ€‘031â€‘UTâ€‘POS | REQâ€‘031 | System rejects multiâ€‘vendor product data | `product.vendor="multiple"` | `Error: SingleVendorConstraint` |

## REQâ€‘032 â€“ Assumption: Reliable Internet
| TESTâ€‘032â€‘UTâ€‘POS | REQâ€‘032 | Normal operations succeed when network available | All API calls with network up | Successful responses for each call |

## REQâ€‘033 â€“ Assumption: Accurate Inventory Data
| TESTâ€‘033â€‘UTâ€‘POS | REQâ€‘033 | Admin update reflected immediately | Admin sets stock=20; query returns 20 | Consistent result |

## REQâ€‘034 â€“ Assumption: PCI Compliance Handled Externally
| TESTâ€‘034â€‘UTâ€‘POS | REQâ€‘034 | No raw card data stored after payment | Complete checkout (mock provider) | Database contains no columns with raw card numbers |

## REQâ€‘035 â€“ Assumption: Google OAuth Configured
| TESTâ€‘035â€‘UTâ€‘POS | REQâ€‘035 | OAuth flow returns valid token | Simulated Google login | `token` present and nonâ€‘null |
