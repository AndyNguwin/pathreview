## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/88

**Issue title:** ``POST /reviews`` endpoint has no test for when the profile has no ingested documents


**Tier:** Tier 1

**Problem summary:**
There are currently many unit tests (at ``tests/unit``) in this project, but there aren't any that covers testing the ``/reviews`` endpoint (there is one for the review service layer). The specific issue is that there isn't a test for the edge case of ``POST /reviews`` when a profile exists without any associated/ingested documents. This endpoint is meant to create a new review for the profile, triggering the ingestion pipeline with a profile's documents. There needs to be a test to ensure the endpoint doesn't break and returns an appropriate error instead of crashing when hitting the endpoint with a profile that has no ingested documents.

I chose this issue because this is my first time committing to a large codebase that I'm unfamiliar with. I think a tier 1 problem would be a good fit for me to familiarize myself to starting open source contributions. I was also very interested in writing test cases in the previous projects so I decided on an issue where I would test functionality. This also gives me a different view on what could be considered a "contribution", which can be anything that is asked for by a maintainer rather than always being a new feature to add or bug to fix.

**Branch name:** 
- https://github.com/AndyNguwin/pathreview/tree/test/88-reviews-no-documents-test
- test/88-reviews-no-documents-test

**Setup confirmation:** [X] App runs locally at localhost:5173

**Cohort ledger:** [X] Issue added to cohort ledger

## Week 8 — Reproduction & solution planning

**Reproduction commit link:**
- [Link specifically for issue reproduction](https://github.com/AndyNguwin/pathreview/commit/3dabbfb311adb973316a183374d59544c3ae23c0)

**Reproduction summary:**
I reproduced the issue by sending `curl` requests to the `POST auth/register` and `POST auth/login` endpoints to create an account, `POST /profiles` to create a profile with no data or content since the default for those fields are `None`, and then `POST /reviews` and `POST /reviews/{reviewID}` to create the review and check its status. From what I observed, the system allows for the review to be made rather than rejecting it or raising an error to handle and it eventually switches from `status: pending` to `status: complete` with a generic analysis/review. View the reproduction steps and results in the [`REPRODUCE_ISSUE.md`](REPRODUCE_ISSUE.md)

**PLAN.md link:** [https://github.com/AndyNguwin/pathreview/blob/test/88-reviews-no-documents-test/PLAN.md](https://github.com/AndyNguwin/pathreview/blob/test/88-reviews-no-documents-test/PLAN.md)

**Blockers or open questions:**
- What exactly is the expected behavior for this edge case? Should there be an error response JSON? Is there a specific error message or status code it should have?

## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
- I have reviewed the test files in `tests/unit/` to understand their structure and patterns using `pytest`. I see patterns such as naming conventions starting with "test" and then description of what's being tested, `@pytest.mark.unit` to tag test classes as unit tests and then `@pytest.fixture` for anything reusable for the test cases in a file, using mocks, etc.
- I have started working on creating the fixtures and understanding how mocks are used and implemented in `test_review_routes.py`

**Next steps:**
- Continue setting up the test file, specifically the test cases that will use the fixtures of the client, database, user, and profile.

**Blockers:**
- Just new to learning about mocks, fixtures, and syntax.

---

### Check-in 2 (end of week)

**PR link:** [https://github.com/ascherj/pathreview/pull/549](https://github.com/ascherj/pathreview/pull/549)

**Branch:** `test/88-reviews-no-documents-test`

**What you built:**
I created an endpoint-level test for `POST /reviews` when the submitted profile has no data source or ingested content. The test uses FastAPI `TestClient` with mocked auth and db dependencies. I have the current test marked as `xfail` because the current behavior doesn't align with the expected behavior written in the issue writeup. The expected behavior was for the endpoint to return an appropriate error such as `400`, but it instead processes successfully and creates/commits new reviews to the database.

**Tests added or updated:**
`tests/unit/test_review_routes.py`

**Self-review confirmation:** 
- [X] make check passes
- [X] make test-unit passes
- There were previous tests that were not passing, but my changes do not touch any other files.

**Draft PR feedback received from:** None
