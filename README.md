# Petstore API Tests — Postman + Newman

[![API tests](https://github.com/msvjr/petstore-api-tests/actions/workflows/api-tests.yml/badge.svg)](https://github.com/msvjr/petstore-api-tests/actions/workflows/api-tests.yml)

API test suite for the public [Swagger Petstore](https://petstore.swagger.io/) running on every push and weekly in GitHub Actions, with an HTML report attached to each run.

## Coverage

| # | Request | Checks |
|---|---|---|
| 01 | `POST /pet` | 200, echoed id and name, default status, response time |
| 02 | `GET /pet/{id}` | 200, required fields, field types |
| 03 | `PUT /pet` | status transition `available → sold` |
| 04 | `GET /pet/findByStatus` with invalid value | no 5xx, no pet with another status leaks in |
| 05 | `GET /pet/abc` | 404 for non-numeric id |
| 06 | `DELETE /pet/{id}` | 200 |
| 07 | `GET /pet/{id}` after delete | 404 + error message |
| 08 | `POST /store/order` | 200, status and quantity preserved |
| 09 | `GET /store/order/{id}` | 200, same id |
| 10 | `DELETE /store/order/{id}` | 200 |
| 11 | `GET /store/inventory` | 200, integer counts |

## Design choices

- **Unique test data per run** (`Date.now()` id, random name) so parallel runs and other sandbox users don't collide.
- **Full lifecycle in one flow**: the delete is verified by a follow-up `GET` returning 404, not by trusting the delete's 200.
- **Known sandbox behavior:** the public Petstore is shared and eventually consistent, so reads wait 1.5 s after writes. A flaky public dependency is documented, not hidden.

## Run locally

```bash
npm install -g newman newman-reporter-htmlextra
newman run petstore.postman_collection.json -e petstore.postman_environment.json -r cli,htmlextra
```

Or import both JSON files into Postman.

---
*Part of [Marcio Viana Junior's portfolio](https://github.com/msvjr).*
