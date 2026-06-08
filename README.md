# Postman Newman CI — DummyJSON API Test Suite

A complete API testing project built with Postman and Newman, featuring 20+ automated test cases and a GitHub Actions CI pipeline that runs on every push and publishes an HTML report.

---

## Project Overview

This project tests the [DummyJSON](https://dummyjson.com) public API across four resources: Posts, Users, Auth, and Search. Tests are written inside Postman using JavaScript assertions, executed headlessly via Newman CLI, and automated through GitHub Actions on every commit.

---

## Tools Used

| Tool | Purpose |
|---|---|
| **Postman** | Building and organizing the collection, writing test assertions and pre-request scripts |
| **Newman CLI** | Running the Postman collection from the command line without opening Postman |
| **newman-reporter-htmlextra** | Generating a rich HTML report with detailed test results |
| **GitHub Actions** | Automating the test run on every push to the repository |

---

## Collection Structure

```
DummyJSON API Tests
│
├── Posts
│   ├── GET all posts
│   ├── GET single post
|   ├── GET search posts with keyword - valid query
│   ├── GET post - invalid ID
│   ├── GET post - non-existent ID
│   ├── POST create post
│   ├── PUT update post
│   └── DELETE post
│
├── Users
│   ├── GET all users
│   ├── GET single user
│   ├── GET user - invalid ID
│   ├── GET user - non-existent ID
│   └── POST create user
│
├── Auth
│   ├── POST login - valid credentials

```

---

## What Is Tested

- **Status codes** — every request validates the correct HTTP status
- **Response body structure** — checks that required fields exist and have correct types
- **Response body values** — validates correct IDs, keywords, and data are returned
- **Response headers** — verifies the `Date` header is present and valid
- **Response time** — ensures requests respond within an acceptable threshold
- **Negative cases** — invalid IDs, wrong credentials, and missing fields return correct error responses
- **Search accuracy** — verifies the response contains the queried keyword

---

## Environment Files

Two environment files are included:

| File | Base URL |
|---|---|
| `dev-environment.postman_environment.json` | `https://dummyjson.com` |
| `prod-environment.postman_environment.json` | `https://dummyjson.com` |

Variables used across the collection:

| Variable | Description |
|---|---|
| `baseUrl` | Base URL of the API |
| `postId` | ID used for single post requests |
| `userId` | ID used for single user requests |

---

## Project Structure

```
postman-newman-ci/
├── collections/
│   └── dummyjson-collection.postman_collection.json
├── environments/
│   ├── dev-environment.postman_environment.json
│   └── prod-environment.postman_environment.json
├── reports/
│   └── report.html
└── .github/
    └── workflows/
        └── newman.yml
```

---

## Running Locally

### Prerequisites

```bash
npm install -g newman
npm install -g newman-reporter-htmlextra
```

### Run the collection

```bash
newman run collections/dummyjson-collection.postman_collection.json \
  -e environments/dev-environment.postman_environment.json
```

### Run with HTML report

```bash
newman run collections/dummyjson-collection.postman_collection.json \
  -e environments/dev-environment.postman_environment.json \
  -r htmlextra \
  --reporter-htmlextra-export reports/report.html
```

Open `reports/report.html` in your browser to view results.

---

## CI Pipeline

The GitHub Actions workflow triggers automatically on every push to `master`.

**Pipeline steps:**
1. Checkout the repository
2. Install Node.js 18
3. Install Newman and the HTML reporter
4. Run the full collection against the dev environment
5. Upload the HTML report as a downloadable artifact

The report is always uploaded even if tests fail, so failures can be investigated.

**Workflow file:** `.github/workflows/newman.yml`

To view a report from a past run:
1. Go to the **Actions** tab on GitHub
2. Click any workflow run
3. Scroll to **Artifacts** and download `newman-report`

---

## Key Learnings

- Postman environment variables must have their **current value** set before exporting, not just the initial value
- `{{variable}}` syntax only works in Postman fields — inside JavaScript test scripts use `pm.environment.get("variable")`
- `parseInt()` is required when comparing environment variables to numeric response fields since all env variables are strings
- URL-encoded search params must be decoded with `decodeURIComponent()` before asserting against response text
- GitHub Actions should use `@v4` for all actions — v3 is deprecated

---

## Target API

[DummyJSON](https://dummyjson.com) — a free public REST API for testing and prototyping. No authentication required.
