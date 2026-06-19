## 1. Docker Setup

- [ ] 1.1 Create `docker-compose.yml` at the project root using the `dockware/dev:latest` image, mapping port 80 to host port 80
- [ ] 1.2 Add a `healthcheck` to the Shopware service in `docker-compose.yml` (e.g., curl to `http://localhost/`)
- [ ] 1.3 Verify `docker compose up -d` starts the container and the storefront is reachable at `http://localhost`

## 2. Page Object

- [ ] 2.1 Create `StorefrontPage.java` in `src/test/java/de/comsystoreply/aiqe/aiqeshopwaretests/` with Selenide element fields: `mainNavigation`, `searchInput`, `searchButton`
- [ ] 2.2 Inspect the running dockware storefront to identify correct CSS/XPath selectors for each element and set them in `StorefrontPage`

## 3. Smoke Tests

- [ ] 3.1 Create `StorefrontSmokeTest.java` in the same package with a `@BeforeAll` that reads `shopware.baseUrl` system property (defaulting to `http://localhost`) and sets `Configuration.browserSize = "1280x800"`
- [ ] 3.2 Implement `homepageLoads()` test: open base URL, assert `Selenide.title()` is not blank, assert `mainNavigation` is visible
- [ ] 3.3 Implement `categoryNavigationWorks()` test: click the first top-level nav link, assert the URL changed, assert at least one product card or listing element is visible
- [ ] 3.4 Implement `searchReturnsResults()` test: type "Shirt" into `searchInput`, click `searchButton`, assert at least one product result element is visible

## 4. Verification

- [ ] 4.1 Run `./gradlew test` with the dockware container running and confirm all three smoke tests pass
- [ ] 4.2 Confirm Serenity report is generated in `build/reports/` with test results
