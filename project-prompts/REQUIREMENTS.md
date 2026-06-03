Functional Requirements
REQ-001: Google Authentication â€“ Users must be able to register and log in using Google accounts, providing quick and secure access.
REQ-002: Product Search â€“ Users must be able to enter keyword queries and receive a list of matching products.
REQ-003: Typoâ€‘Tolerant Search â€“ The search engine must recognize misspelled keywords (up to two character errors) and return relevant products.
REQ-004: Wishlist â€“ Users must be able to add products to a personal wishlist for later purchase and must not create duplicate entries.
REQ-005: Personalized Product Suggestions â€“ The system must generate product recommendations based on user behavior, including browsing, wishlist, and purchase history.
REQ-006: Smart Suggestions Engine â€“ Combine wishlist activity, search history, recently viewed items, purchase history, and trending products to produce highly personalized suggestions.
REQ-007: Recently Viewed Products â€“ The UI must display a list of products the user has recently viewed.
REQ-008: Shopping Cart â€“ Users must be able to add, review, modify quantities, and proceed to checkout with selected products.
REQ-009: Order Management (User) â€“ Users must be able to view order status and order history.
REQ-010: Product Management (Admin) â€“ Administrators must be able to create, edit, archive, and publish products.
REQ-011: Inventory Management (Admin) â€“ Administrators must be able to track stock levels and receive lowâ€‘stock alerts.
REQ-012: Order Management (Admin) â€“ Administrators must be able to process orders, update statuses, and manage fulfillment.
REQ-013: Analytics Dashboard â€“ Administrators must be able to view key business metrics such as sales, orders, and inventory.

Nonâ€‘Functional Requirements
REQ-014: Usability â€“ Navigation must be intuitive with a learning curve of â‰¤5â€¯minutes for new users on desktop and mobile.
REQ-015: Accessibility â€“ All interactive elements must be operable via keyboard and compatible with screen readers; color contrast ratio must be â‰¥4.5:1.
REQ-016: Performance â€“ Page load time must be â‰¤2â€¯seconds on a 3G connection; search responses must be returned withinâ€¯300â€¯ms.
REQ-017: Reliability â€“ System uptime must be â‰¥99.9â€¯% monthly; transaction processing must be ACIDâ€‘compliant.
REQ-018: Security â€“ Authentication must use OAuthâ€¯2.0 with Google; all data in transit must use TLSâ€¯1.2+; no passwords are stored.
REQ-019: Privacy â€“ User consent must be recorded before any behavioral tracking; privacy policy must be accessible from every page.
REQ-020: Scalability â€“ Architecture must support up to 100â€¯k concurrent users and a product catalog of 50â€¯k items without performance degradation.
REQ-021: Maintainability â€“ Codebase must achieve â‰¥80â€¯% unitâ€‘test coverage and follow ESLint Airbnb style guidelines.

Constraints
REQ-022: Singleâ€‘Vendor Architecture â€“ The system will sell only products owned by the business; no thirdâ€‘party marketplace integration.
REQ-023: Googleâ€‘Only Authentication â€“ No alternative login methods are permitted.
REQ-024: No Multiâ€‘Vendor Marketplace â€“ Features for marketplace seller onboarding are out of scope.
REQ-025: Payment Integration â€“ Payments are handled exclusively by approved external providers; the system does not store card data.

Edge Cases
REQ-026: Internet Outage â€“ If a user attempts any operation without connectivity, a clear offline message must be shown with a retry option.
REQ-027: Outâ€‘ofâ€‘Stock Purchase â€“ Attempting to purchase an outâ€‘ofâ€‘stock product must be blocked with a realâ€‘time notification.
REQ-028: Search Misspelling â€“ Queries with up to two character errors must still return relevant results via typoâ€‘tolerant logic.
REQ-029: Duplicate Wishlist Entry â€“ Adding the same product to the wishlist multiple times must not create duplicate entries.
REQ-030: Concurrent Inventory Updates â€“ Simultaneous admin updates to stock must be serialized to prevent negative inventory values.

Assumptions
REQ-031: The platform operates as a singleâ€‘vendor business.
REQ-032: Users have reliable internet connectivity during normal usage.
REQ-033: Administrators will maintain accurate inventory data.
REQ-034: Payment providers will handle all PCI compliance; the application does not store sensitive payment details.
REQ-035: Google OAuth credentials are preâ€‘configured and functional
