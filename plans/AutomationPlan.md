## Full Automation List

These are directly tied to the Team API Keys & Proxy Pools feature and can be automated in 1 day.

### A. API Key Lifecycle Tests (Core Feature Logic)

**1. Create API Key**
- Validate label, pool, and default status.
- Ensures that new keys are created correctly.

**2. Update Key Label**
- Confirms that users can rename keys.
- Ensures UI, API, DB consistency for metadata.

**3. Update Key Pool Assignment**
- Verifies that keys can be linked to Residential /Premium /Dedicated /Datacenter pools.
- This is a central requirement of the feature.

**4. Revoke Key**
- Revoked keys must instantly become inactive.
- Critical for security.

**5. Unrevoke Key**
- Restores previously revoked key.
- Ensures status transitions work correctly.


### B. Key Retrieval /Pagination Tests (Scaling Behavior)

**6. List Keys (limit/skip pagination)**
- Supports large datasets (10k–150k keys).
- Ensures the API returns stable, predictable sets.


### C. Activity Log Tests (Audit Trail Feature)

**7. Activity Log Includes Lifecycle Events**
- Validates that logs contain: created, updated, revoked
- Ensures user actions are visible in UI and auditable.


### D. UI Smoke Tests

**8. Create Key from UI**
- Verify modal opens
- Fill label + pool
- Key appears in list instantly

**9. Update Key Label from UI**
- Open key details
- Edit label
- Updated label appears in list

**10. Update Pool Assignment from UI**
- Select new pool
- Ensure pool tag updates visually

**11. Revoke / Unrevoke Key via UI**
- The status badge changes
- The key icon or color updates

**12. Basic Search & Filter Test**
- Search by label
- Filter by pool (Residential, Premium, Dedicated)

**13. First-page Pagination Test**
- Validate next/previous buttons
- Works without errors
- No UI freeze


## Why These Tests Are Automated.

These tests were selected because they cover the core workflows, highest risks, and most frequently used actions in the new “Team API Keys & Proxy Pools” feature. They ensure that:

Key lifecycle actions (create, update, revoke) always work, these are critical for security and daily operations.

Proxy pool assignment is correct, this is a central requirement of the feature.

Label updates and metadata changes remain consistent, prevents UI/API mismatch.

Pagination and list endpoints work reliably, especially important for large accounts with thousands of keys.

Activity logs correctly track actions, essential for auditing, debugging, and user trust.

UI reflects backend changes, ensuring the user sees accurate, real-time information.

The suite provides fast, stable, high-ROI coverage. API tests run quickly and catch regressions early, while UI smoke tests validate key visual flows.



## Task Assignment

API automation will be handled by the Senior QA because these tests involve backend logic, security-critical flows, and require stronger technical expertise.

UI smoke tests will be assigned to the Junior QA since they are simpler, visual checks that help them learn the feature without affecting core system stability.

This division ensures fast delivery, high accuracy for critical areas, and effective use of each QA’s skill level.



## Automation Architecture & Folder Structure
Project/
   api/
      clients/
            KeyManagementClient.java     <-- create, update label, update pool, revoke, unrevoke
            KeyQueryClient.java          <-- list keys, get details
            KeyActivityClient.java       <-- logs only
            AuthClient.java              <-- If auth is needed
         
      models/
            ApiKey.java             <-- POJO for key response
            ApiKeyList.java         <-- POJO for pagination
            ActivityLogEntry.java   <-- POJO for logs

      tests/
            KeyCreationTests.java        <-- create key + initial assertions
            KeyUpdateTests.java          <-- update label + update pool
            KeyRevocationTests.java      <-- revoke + unrevoke
            KeyPaginationTests.java      <-- list keys (skip/limit)
            KeyActivityLogTests.java     <-- verify logs appear


   ui/
      pages/
            BasePage.java                <-- shared methods (click, type, wait)
            HomePage.java                <-- navigation entry point
            LoginPage.java
            KeysPage.java
            KeyDetailsPage.java

      tests/
            CreateKeyUITest.java
            UpdateKeyUITest.java
            RevokeKeyUITest.java
            PaginationUITest.java
            SearchFilterUITest.java


   core/
        BaseApiTest.java           <-- Test setup, RestAssured config, base URL, token
        BaseUITest.java            <-- Selenium driver setup
        DriverFactory.java         <-- creates ChromeDriver/FirefoxDriver
        ConfigLoader.java          <-- loads environment configs


   config/
        dev.properties
        stage.properties
        prod.properties

    .github/
        workflows/
            api-tests.yml      <-- Runs API tests on every PR
            ui-tests.yml       <-- Runs UI smoke tests nightly or manually



## QA Metrics Tracked in CI

1. Key Flow Pass Rate
Tracks success of API key lifecycle tests (create, update, revoke).
A drop signals core feature or security regression.

2. UI Regression Rate
Measures stability of UI smoke flows (create/edit/revoke/search/pagination).
A rise indicates UI breakage or API–UI desync.

3. Flaky Test Rate
Measures how often the same test produces inconsistent results across runs.
High flakiness reduces trust in CI,hides real bugs, and slows teams down. Flaky tests should be quarantined until fixed.



## Environment Handling

### Test Data Strategy

**Dev** Automation generates and deletes API keys during runtime. Ideal for rapid CI feedback.

**Stage** Automation uses predefined test keys and proxy pools stored in per-environment config files. Lifecycle tests create temporary keys when needed.

**Prod** No automated writes. CI pipelines for UI/API do not run against production to avoid impacting real users.


### Config & Secrets

**Environment configs stored in**
config/dev.properties
config/stage.properties
config/prod.properties

**Secrets (tokens, credentials) stored in** 
GitHub Actions Secrets or local .env/.properties files ignored by Git.

CI switches environments via: ./gradlew test -Denvironment=stage


### Automation-Specific Risks

Running write operations on prod can break customer access; automation is disabled for prod.

Stage rate limits and IP whitelisting may cause false negatives if configs are incorrect.

Dev instability may lead to intermittent failures; API tests include retry logic when appropriate.
