# Design Decisions

- **Clientâ€‘Side**: Use plain React 18 application (no SPA label) built with Vite.
- **Styling**: TailwindCSS, Framer Motion for animations, darkâ€‘mode support.
- **Backend**: Node.js 20 with Express, TypeScript, Sequelize ORM.
- **Authentication**: Google OAuth 2.0 with JWT stored in HttpOnly cookies.
- **Cache**: Redis for sessions and shortâ€‘lived query caching.
- **CI/CD**: GitHub Actions, Docker, Azure Web Apps.
- **Folder Structure**: Detailed hierarchy as documented in SYSTEM_DESIGN.md.
- **Testing Strategy**: TDD first â€“ unit, integration, and E2E tests generated per requirement.
