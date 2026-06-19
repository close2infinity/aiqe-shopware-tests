## Context

The project already has a working Selenide + Serenity BDD + JUnit 5 test framework. Sample tests target JetBrains.com. The goal is to point the framework at a real Shopware instance running locally via Docker, and add an initial smoke test suite that verifies the storefront is functional end-to-end.

Dockware (`dockware/dev`) is a community-maintained Docker image that ships a fully configured Shopware 6 instance ready to run with a single `docker compose up`. It exposes the storefront on port 80 by default.

## Goals / Non-Goals

**Goals:**
- Provide a `docker-compose.yml` that starts Shopware via dockware with no manual setup
- Implement a `StorefrontPage` page object covering the Shopware storefront homepage, navigation, and search bar
- Implement a `StorefrontSmokeTest` with three smoke scenarios: homepage loads, category navigation works, search returns results

**Non-Goals:**
- Admin/backend UI tests
- Checkout or payment flow tests
- CI pipeline integration (deferred)
- Testing against a remote/cloud Shopware instance

## Decisions

### Use dockware/dev image
**Decision:** Use `dockware/dev:latest` as the Shopware container.  
**Rationale:** dockware is purpose-built for Shopware development/testing, includes demo data, and starts without any additional configuration. Alternative would be building a custom Shopware image from scratch, which adds significant maintenance overhead.  
**Alternatives considered:** Official Shopware Docker setup (requires separate MySQL + Elasticsearch containers and manual install steps).

### Keep base URL configurable via system property
**Decision:** Read the Shopware base URL from a JVM system property `shopware.baseUrl` with a default of `http://localhost`.  
**Rationale:** Allows CI/CD to override the URL without changing code. Tests can then run against any environment.

### Page Object per page area
**Decision:** Create one `StorefrontPage` class covering homepage-level elements.  
**Rationale:** At smoke-test scale a single page object is sufficient. As the suite grows, individual page objects (e.g., `SearchResultsPage`, `CategoryPage`) should be extracted.

## Risks / Trade-offs

- **Dockware startup time** → dockware can take 30–60 seconds to be ready. Mitigation: document that tests should only run after the container is healthy; consider adding a wait script or `healthcheck` in the compose file.
- **Demo data dependency** → smoke tests rely on dockware's pre-loaded demo data (e.g., known category names). If dockware changes its demo data, selectors or assertions may break. Mitigation: use resilient selectors (e.g., "first category link") rather than hardcoded text where possible.
- **Port conflicts** → port 80 may be in use on developer machines. Mitigation: note in documentation that the port can be remapped in `docker-compose.yml`.

## Migration Plan

1. Add `docker-compose.yml` to repository root
2. Add `StorefrontPage.java` and `StorefrontSmokeTest.java` under the existing test source tree
3. Run `docker compose up -d` before executing `./gradlew test`
4. Existing `MainPageTest` (JetBrains sample) can coexist; no changes required to it

## Open Questions

- Should the existing `MainPageTest` be removed or kept as a framework reference? (Recommend keeping for now, mark as sample.)
- Should we add a Gradle task that starts the Docker container automatically before the test task?
