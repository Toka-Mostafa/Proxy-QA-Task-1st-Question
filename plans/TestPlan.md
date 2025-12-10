# Test Plan — Team API Keys & Proxy Pools

## 1. Scope & Objective

This test plan covers the new "Team API Keys & Proxy Pools" area.
The goal is to verify that users can manage their API keys reliably (create, label, revoke, assign pools) and that these keys behave correctly when used with different proxy types.

The plan also focuses on:

- Proper enforcement of revocation and IP whitelisting
- Correct routing to each proxy product
- Accurate usage logs
- Stable performance for large accounts (10k+ keys)
- Consistent behavior across regions

## 2. Key Acceptance Criteria

- API keys should be created, edited, viewed, and revoked without errors.
- Keys should be linked to the correct proxy pool (Datacenter, Residential, Premium Shared, Dedicated).
- A revoked or inactive key should fail authentication everywhere it can be used.
- Authentication should respect IP whitelisting and environment rules.
- Valid keys should route through the correct proxy product and return expected responses.
- Rate limits should work as expected and return appropriate status codes.
- Usage logs should capture successful and failed requests (IP, country, timestamps, error).
- The API Keys page should load efficiently even for very large key sets and should support filtering and search.
- UI, API, and backend data should stay in sync.

## 3. Core Test Cases

### A. UI Test Scenarios

- Creating a new key should display it in the list immediately.
- Updating a key label or pool assignment should reflect the change instantly on the UI and API.
- Revoking or un-revoking a key should update the key's status across the UI without delay.
- Loading the key list with large datasets (10k–150k keys) should not freeze or show infinite loading.
- Filtering, searching, and pagination should work correctly and return accurate results.
- The key details page should correctly show assigned pool, whitelist, rate limits, and activity logs.
- Invalid inputs (wrong IP format, empty label, duplicates) should show proper error messages.
- All UI text and labels should be consistent across different user roles, browsers, and sessions.

### B. API Test Scenarios

- Creating a key via API should return the correct response and store all fields accurately.
- Updating or revoking a key should reflect immediately in subsequent API responses.
- Paginated key retrieval should consider skip/ limit and return stable results even on large datasets.
- A valid key should authenticate proxy requests and return a 200 response.
- A revoked key should reject proxy requests with 401/403.
- A request from an unwhitelisted IP should return 403.
- Keys exceeding rate limits should return 429.
- Proxy requests should be routed through the correct proxy pool based on the key's assignment.
- Activity logs should update after both successful and failed proxy attempts.
- Requests made from different countries/endpoints should behave consistently and respect key status.

### C. Data & Backend Scenarios

- Creating or updating a key should write the correct status, timestamps, pool, and workspace ID into the DB.
- Revocation should propagate across all proxy servers so no node accepts the key afterward.
- Activity logs in the database should match the logs shown in the UI and returned by the API.
- Failed proxy requests (revoked key, bad IP, rate-limited) should still produce log entries.

### D. Environment-Specific Behavior
- Using the same key in dev, stage, and prod should trigger each environment’s own rate limit rules.
- Keys should only work in the environments they are intended for, if an environment restriction exists, accessing another environment should fail.
- IP whitelisting should be enforced consistently in stage/prod, dev may allow more relaxed rules (depending on configuration).
- Activity logs should correctly show which environment the request was made from.
- Revoking a key in one environment should not leave stale sessions in another environment.
- Environment-specific base URLs or proxy endpoints should route to the correct proxy network and respect key permissions.

## 4. Exploratory Scenarios (Proxy-Focused)

### Rate Limits & Traffic Patterns

- Sending rapid burst traffic should hit rate limits consistently with 429 responses.
- Steady traffic should remain below limits without unexpected blocks.
- Running multiple parallel requests (similar to CI load) should not cause inconsistent responses.
- Slow or unstable proxy endpoints should trigger timeouts or errors without false success.

### Geo-Distributed Behavior

- Using the same key from multiple regions should always respect its current status (active or revoked).
- Logs should correctly capture last-used IP and country across different proxy locations.
- No region should behave differently in terms of authentication or revocation.

### Large Account Behavior

- Loading the API Keys page with 50k–150k keys should remain responsive and not time out.
- Large activity logs should load, filter, and scroll smoothly.
- API responses should remain within acceptable time under high load.

### Failure & Edge Conditions

- Proxy gateway timeouts or DNS failures should appear correctly in the activity logs.
- Incorrect proxy parameters (wrong protocol, country mismatch) should return clear errors.
- Slow proxies should result in proper timeout errors rather than false "connected" states.
- The UI should stay usable even if the backend is slow or returns partial data.



## Environment Handling (dev / stage / prod)

### Test Data Strategy

**Dev** Create temporary API keys and proxy pools freely for testing.
Use this environment for early testing and exploratory work.

**Stage** Use a dedicated QA workspace with controlled test keys. 
Validate key lifecycle, pool assignment, logs, rate limits, and whitelisting under production-like rules.

**Prod** Only limited manual, non-destructive smoke tests. 
No creation or revocation of real customer keys.


### Environment Differences & Risks

**Dev** Frequent deployments and unstable services may cause inconsistent results. Rate limits and whitelisting may be not fully enforced.

**Stage** Must reflect real behavior, stricter IP whitelisting, rate limiting, propagation delays. 
Issues found here are blocking for release.

**Prod** Any test action risks affecting customers. Must avoid modifying API keys or generating excessive traffic. 
Monitoring and logging need extra caution.



## Automation strategy and CI implementation 
Details are documented separately in the Automation Plan of this repository.


