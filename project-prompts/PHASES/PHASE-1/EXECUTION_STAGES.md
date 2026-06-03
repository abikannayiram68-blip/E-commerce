File: /project-prompts/PHASES/PHASE-1/EXECUTION_STAGES.md

# PHASE 1 - Foundation & Authentication: Execution Stages

REQs: REQ-001, REQ-018, REQ-019, REQ-022, REQ-023, REQ-035

---

## STAGE 1 - Setup

**REQ-IDs**: REQ-018, REQ-022, REQ-035
**TEST-IDs**: TEST-018-UT-POS, TEST-022-UT-POS, TEST-035-UT-POS

**Tasks**:
- Initialize monorepo (pnpm workspaces)
- Scaffold `/frontend` with Vite + React 18 + TypeScript
- Scaffold `/backend` with Node.js 20 + Express + TypeScript
- Configure ESLint (Airbnb) + Prettier
- Configure environment variables (.env.example)
- Set up GitHub repository and branch protection rules

**TDD Flow**:
1. Write: env-config.test.ts - verify required env vars are present
2. Run -> FAIL
3. Implement: .env loading with `dotenv`
4. Run -> PASS
5. Refactor: extract config module

---

## STAGE 2 - Architecture

**REQ-IDs**: REQ-018, REQ-020, REQ-022
**TEST-IDs**: TEST-018-UT-POS, TEST-020-UT-POS, TEST-022-UT-POS

**Tasks**:
- Define folder structure per SYSTEM_DESIGN.md
- Configure path aliases (tsconfig.paths)
- Set up shared `/packages/types` and `/packages/utils`
- Configure Docker (Dockerfile for api + web, docker-compose.yml)
- Define CORS policy (only allow front-end origin)

**TDD Flow**:
1. Write: cors.test.ts - verify CORS rejects disallowed origins
2. Run -> FAIL
3. Implement: Express CORS middleware
4. Run -> PASS
5. Refactor: centralize CORS config

---

## STAGE 3 - Database

**REQ-IDs**: REQ-017, REQ-018, REQ-022
**TEST-IDs**: TEST-017-UT-POS, TEST-018-UT-POS, TEST-022-UT-POS

**Tasks**:
- Initialize MySQL 8 via Docker
- Set up Sequelize ORM with connection string
- Create User model (id, googleId, email, name, avatar, consentAt)
- Run initial migration: `Sequelize migrate dev --name init`
- Configure Redis 7 for session storage

**TDD Flow**:
1. Write: user-model.test.ts - verify User is created and retrieved by googleId
2. Run -> FAIL
3. Implement: Sequelize User schema + migration
4. Run -> PASS
5. Refactor: seed script for test data

---

## STAGE 4 - Backend (Auth API)

**REQ-IDs**: REQ-001, REQ-018, REQ-023, REQ-035
**TEST-IDs**: TEST-001-UT-POS, TEST-001-UT-NEG, TEST-001-IT-POS, TEST-018-UT-POS, TEST-018-UT-NEG, TEST-023-UT-POS, TEST-023-UT-NEG, TEST-035-UT-POS

**Tasks**:
- Implement Google OAuth 2.0 callback route (`GET /auth/google/callback`)
- Issue JWT (access + refresh) stored in HttpOnly cookies
- Implement `GET /auth/me` - returns current user from JWT
- Implement `POST /auth/logout` - clears cookies
- Block all non-Google login attempts (return 405)

**TDD Flow**:
1. Write: auth.test.ts - mock Google token, verify JWT issued + cookie set
2. Run -> FAIL
3. Implement: Passport.js GoogleStrategy + JWT signing
4. Run -> PASS
5. Refactor: extract authService, jwtService

---

## STAGE 5 - Frontend (Auth UI)

**REQ-IDs**: REQ-001, REQ-019, REQ-023
**TEST-IDs**: TEST-001-E2E-POS, TEST-019-UT-POS, TEST-019-E2E-POS, TEST-023-E2E-POS

**Tasks**:
- Build Login page with "Sign in with Google" button
- Redirect authenticated users to home
- Build Privacy Consent banner (record consent to API before tracking)
- Build PrivacyPolicy page (accessible from every page footer)
- Protect routes: redirect unauthenticated users to /login

**TDD Flow**:
1. Write: LoginPage.test.tsx - verify Google button renders, click triggers OAuth redirect
2. Run -> FAIL
3. Implement: LoginPage component + useAuth hook
4. Run -> PASS
5. Refactor: extract ProtectedRoute wrapper

---

## STAGE 6 - State

**REQ-IDs**: REQ-001, REQ-019
**TEST-IDs**: TEST-001-UT-POS, TEST-019-UT-POS

**Tasks**:
- Implement global AuthContext (user, loading, login, logout)
- Implement ConsentContext (consentGiven, recordConsent)
- Persist auth state across page refresh via `GET /auth/me`

**TDD Flow**:
1. Write: AuthContext.test.tsx - verify user populated after /auth/me resolves
2. Run -> FAIL
3. Implement: AuthContext with React Query
4. Run -> PASS
5. Refactor: merge into single useSession hook

---

## STAGE 7 - Auth (end-to-end wiring)

**REQ-IDs**: REQ-001, REQ-018, REQ-023
**TEST-IDs**: TEST-001-IT-POS, TEST-018-IT-POS, TEST-023-IT-POS

**Tasks**:
- Wire frontend OAuth redirect to backend callback
- Test full login/logout cycle across environments
- Validate JWT expiry and refresh logic
- Ensure all endpoints reject unauthenticated requests with 401

**TDD Flow**:
1. Write: auth-integration.test.ts - full OAuth login cycle via SuperTest mocks
2. Run -> FAIL
3. Implement: end-to-end wiring
4. Run -> PASS
5. Refactor: unify error responses

---

## STAGE 8 - Integration

**REQ-IDs**: REQ-018, REQ-019, REQ-022, REQ-035
**TEST-IDs**: TEST-018-IT-POS, TEST-019-IT-POS, TEST-022-IT-POS, TEST-035-IT-POS

**Tasks**:
- Integrate Redis session store with Express
- Validate TLS enforcement (HTTPS-only in production)
- Verify consent record is written to DB on acceptance
- Confirm privacy policy link in footer on all pages

**TDD Flow**:
1. Write: consent-integration.test.ts - verify POST /privacy/consent writes to DB
2. Run -> FAIL
3. Implement: consent endpoint + DB write
4. Run -> PASS
5. Refactor: extract privacyService

---

## STAGE 9 - Testing

**REQ-IDs**: REQ-001, REQ-018, REQ-019, REQ-021, REQ-022, REQ-023, REQ-035
**TEST-IDs**: All Phase 1 test IDs

**Tasks**:
- Run full unit test suite: `pnpm test`
- Run integration tests: `pnpm test:integration`
- Run Cypress E2E: `pnpm cypress:run`
- Confirm unit-test coverage >=80%
- Fix any failures before proceeding

---

## STAGE 10 - Deployment

**REQ-IDs**: REQ-017, REQ-018, REQ-020
**TEST-IDs**: TEST-017-E2E-POS, TEST-018-E2E-POS, TEST-020-E2E-POS

**Tasks**:
- Configure GitHub Actions CI pipeline (lint + test + build on PR)
- Build Docker images for api and web
- Deploy to Azure Web Apps (staging environment)
- Verify TLS 1.2+ enforced on Azure
- Smoke-test: login flow on staging URL
