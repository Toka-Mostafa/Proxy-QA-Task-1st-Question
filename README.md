# Proxy QA Assessment

📌 Project Purpose

This repository contains the QA Test Plan, Automation Plan, and pseudo-YAML CI jobs for the Team API Keys & Proxy Pools feature.


📁 Repository Structure

1. Test Plan

**Path** 
    /plans/TestPlan.md

**Covers**
- Scope & objectives
- Acceptance criteria
- UI, API, backend test cases
- Exploratory proxy scenarios
- Environment handling (dev/stage/prod)
- Risks & expected behavior under large data sets

2. Automation Plan

**Path** 
    /plans/AutomationPlan.md

**Covers**
- Full automation scope
- API lifecycle tests
- UI smoke tests
- Pagination & scaling tests
- Why these tests were selected
- Task assignment (Senior vs Junior QA)
- Automation architecture & folder structure
- CI metrics & environment strategy

3. Automation Codebase

**Tools should be used**

Selenium : UI automation
Rest-Assured : API automation
Gradle : build + dependency management
GitHub Actions : CI pipeline
Java : POM structure, models, clients, utilities

**Intended Structure**

/src/test/java/ui : UI smoke tests
/src/test/java/api : API tests
/src/main/java :  Page Objects, API clients, utilities, core setup
/resources : Environment configs and test data


🎯 Intent

**The repository provides**

A clear QA strategy (manual + automated).
Documented workflows for Support.
Scalable test architecture for API and UI validation.
CI-ready automation aligned with the Test Plan.


👤 Author

Toka Mostafa – QA Lead & Automation Engineer
