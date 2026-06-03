File: /project-prompts/SYSTEM_DESIGN.md
# System Design

## Architecture Overview
- **Clientâ€‘Side**: Singleâ€‘Page Application (SPA) built with **React 18** using **Vite** for fast dev builds. UI follows a componentâ€‘driven design system with darkâ€‘mode support and glassmorphism styling.
- **Serverâ€‘Side**: **Node.js 20** runtime exposing a **RESTful API** via **Express**. Business logic is separated into services and controllers, enabling easy unit testing.
- **Data Layer**: **MySQL 8** accessed through **Sequelize ORM** for typeâ€‘safe queries and migrations.
- **Authentication**: **OAuth 2.0** with **Google** provider, using **JWT** stored in HttpOnly cookies for session management.
- **Cache**: **Redis** for session store and shortâ€‘lived query caching (e.g., search suggestions).
- **CI/CD**: GitHub Actions pipeline runs lint, test suites (unit, integration, E2E) and deploys to **Azure Web Apps** on merge to `main`.

## Tech Stack
| Layer | Technology | Reason |
|-------|------------|--------|
| Frontend | React 18, Vite, TypeScript, TailwindCSS, Framer Motion | Modern, fast, componentâ€‘based UI with animation support |
| Backend | Node.js 20, Express, TypeScript, Sequelize, JWT, Passportâ€‘Google | Proven ecosystem, strong typing, rapid development |
| Database | MySQL 8 | Relational data, ACID guarantees, rich query capabilities |
| Cache | Redis 7 | Lowâ€‘latency session and query caching |
| Testing | Jest, React Testing Library, SuperTest, Cypress | Full coverage of unit, integration, and E2E tests |
| DevOps | GitHub Actions, Docker, Azure Web Apps | Automated CI/CD, containerization |

## Database Schema (Simplified)
```sql
-- Users table (OAuth linked)
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    google_sub VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255) NOT NULL,
    name VARCHAR(255),
    avatar_url TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Products table
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    sku VARCHAR(100) UNIQUE NOT NULL,
    name TEXT NOT NULL,
    description TEXT,
    price NUMERIC(10,2) NOT NULL,
    category VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Wishlist items (manyâ€‘toâ€‘many)
CREATE TABLE wishlists (
    user_id INT REFERENCES users(id) ON DELETE CASCADE,
    product_id INT REFERENCES products(id) ON DELETE CASCADE,
    PRIMARY KEY (user_id, product_id)
);

-- Search suggestions (preâ€‘computed) â€“ optional
CREATE TABLE suggestions (
    id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(id),
    product_id INT REFERENCES products(id),
    rank INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
``` 

## API Specification (REST)
| Method | Endpoint | Description | Auth | Request Body | Response |
|--------|----------|-------------|------|--------------|----------|
| POST | `/api/auth/google` | Exchange Google auth code for JWT | None | `{ code: string }` | `{ token: string, user: User }` |
| GET | `/api/products` | List products (search & pagination) | Optional JWT | Query: `q`, `page`, `limit` | `{ items: Product[], total: number }` |
| GET | `/api/products/:id` | Get product details | Optional JWT | â€“ | `Product` |
| POST | `/api/wishlist` | Add product to user's wishlist | JWT | `{ productId: number }` | `{ success: true }` |
| GET | `/api/wishlist` | Retrieve user's wishlist | JWT | â€“ | `Product[]` |
| GET | `/api/suggestions` | Personalized suggestions based on activity | JWT | â€“ | `Product[]` |

## Folder Structure
```
  frontend/
    app/
      (store)/
        page.tsx
      home/
        page.tsx
      login/
        page.tsx
      products/
        page.tsx
        [slug]/
          page.tsx
      categories/
        page.tsx
        [slug]/
          page.tsx
      search/
        page.tsx
      wishlist/
        page.tsx
      cart/
        page.tsx
      checkout/
        page.tsx
      orders/
        page.tsx
        [orderId]/
          page.tsx
      profile/
        page.tsx
      notifications/
        page.tsx
      privacy/
        page.tsx
      admin/
        dashboard/
          page.tsx
        products/
          page.tsx
        categories/
          page.tsx
        inventory/
          page.tsx
        orders/
          page.tsx
        customers/
          page.tsx
        promotions/
          page.tsx
        reviews/
          page.tsx
        analytics/
          page.tsx
        reports/
          page.tsx
    components/
      accessibility/
      admin/
      cart/
      catalog/
      checkout/
      forms/
      layout/
      notifications/
      product/
      recommendations/
      ui/
      wishlist/
    lib/
      api-client.ts
      auth-client.ts
      formatting.ts
      validation.ts
    stores/
      cart-store.ts
      filter-store.ts
      session-store.ts
      ui-store.ts
    tests/
      unit/
      integration/
      e2e/
  backend/
    src/
      main.ts
      app.module.ts
      common/
        decorators/
        filters/
        guards/
        interceptors/
        pipes/
        types/
      config/
        app.config.ts
        database.config.ts
        redis.config.ts
        oauth.config.ts
        payment.config.ts
      auth/
        auth.controller.ts
        auth.service.ts
        google-oauth.service.ts
        session.service.ts
        jwt.strategy.ts
        role.guard.ts
      users/
        users.controller.ts
        users.service.ts
        customer-profile.service.ts
      catalog/
        products.controller.ts
        products.service.ts
        categories.controller.ts
        categories.service.ts
        search.service.ts
        dto/
      wishlist/
        wishlist.controller.ts
        wishlist.service.ts
      cart/
        cart.controller.ts
        cart.service.ts
      checkout/
        checkout.controller.ts
        checkout.service.ts
        payment-provider.adapter.ts
      orders/
        orders.controller.ts
        orders.service.ts
        order-status-workflow.ts
      reviews/
        reviews.controller.ts
        reviews.service.ts
        review-moderation.service.ts
      notifications/
        notifications.controller.ts
        notifications.service.ts
        notification.producer.ts
      recommendations/
        recommendations.controller.ts
        recommendations.service.ts
        smart-suggestions.service.ts
        preference-analysis.service.ts
      behavior/
        behavior-events.controller.ts
        behavior-events.service.ts
        consent.service.ts
      admin/
        admin-dashboard.controller.ts
        admin-products.controller.ts
        admin-categories.controller.ts
        admin-inventory.controller.ts
        admin-orders.controller.ts
        admin-customers.controller.ts
        admin-promotions.controller.ts
        admin-reviews.controller.ts
        admin-analytics.controller.ts
      analytics/
        sales-report.service.ts
        conversion-report.service.ts
        customer-segmentation.service.ts
        product-performance.service.ts
        promotion-performance.service.ts
        success-metrics.service.ts
      inventory/
        inventory.service.ts
        inventory-audit.service.ts
        inventory-forecast.service.ts
      jobs/
        queues.module.ts
        notification.worker.ts
        recommendation.worker.ts
        product-alert.worker.ts
        analytics.worker.ts
      health/
        health.controller.ts
        health.service.ts
      metrics/
        metrics.controller.ts
        prometheus.service.ts
     mysql/
        mysql.module.ts
        mysql.service.ts
    test/
      unit/
      integration/
      e2e/
  packages/
    shared/
      src/
        api-contracts/
        constants/
        errors/
        types/
        validation/
    database/
      mysql/
        schema.sql
        migrations/
        seed.sql
  infra/
    docker/
      Dockerfile.api
      Dockerfile.web

    monitoring/
      prometheus.yml
      grafana/
    compose/
      docker-compose.yml
      docker-compose.test.yml
  .github/
    workflows/
      ci.yml
  project-prompts/
    REQUIREMENTS.md
    SYSTEM_DESIGN.md
    TESTS/
      REQUIREMENT_TEST_MAP.md
      UNIT_TESTS.md
      INTEGRATION_TESTS.md
      E2E_TESTS.md
```
