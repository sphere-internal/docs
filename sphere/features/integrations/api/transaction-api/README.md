---
description: >-
  Create and manage taxable transactions, customers and products in Sphere.
  Authentication, conventions, errors and rate limits.
---

# Transaction API

The Transaction API lets a custom billing system create taxable transactions directly in Sphere. It records both invoices and credit notes. You also use it to manage the customer and product reference data that Sphere needs to calculate tax and to produce VAT-compliant records.

Use the [Tax Calculation](../tax-calculation.md) endpoint for a real-time quote at checkout. Use the Transaction API to record the finalized transaction that Sphere reports and files on.

## Base URL

`https://server.getsphere.com/v1`

## Authentication

Send your API key in the `X-API-KEY` header. Keys begin with `sph_`, are scoped to your Sphere organization, and are shown once at creation and stored hashed. Treat the raw value as a secret.

```
X-API-KEY: sph_your_api_key
Content-Type: application/json
```

## Identifiers

Every record is addressed by an **`external_id` that you choose**. Every reference between records, such as `customer_id`, `product_id`, and `original_transaction_id`, is one of your own external ids.

`external_id` is also the **idempotency key**. Re-posting the same `external_id` updates the existing record instead of creating a duplicate. Records are fetched individually by their `external_id`. There is no list endpoint in v1.

{% hint style="info" %}
Use a stable, unique id from your own system (for example `txn_123`, `cus_123`, `prod_123`). Treat it as an opaque key that can be any string.
{% endhint %}

## Amounts and currency

* Amounts are **major-unit decimal strings**. `"100.00"` is one hundred, not minor units. Responses return amounts as two-decimal strings.
* `currency` is a currency code. It is stored and returned **lowercased**, so `"USD"` in a request comes back as `"usd"`.

## Errors

Successful calls return `200`. Errors use standard HTTP status codes.

| Status | Body                                          | Meaning                                                                                                                                                                           |
| ------ | --------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `400`  | `{ "<field>": ["message"] }`                  | Validation error, keyed by the failing field. Nested fields use dotted keys such as `line_items.0.amount`, and request-level errors use `body`. Returned by the create endpoints. |
| `403`  | `{ "detail": "..." }`                         | Missing, invalid, or inactive API key, or a key not provisioned for the Transaction API.                                                                                          |
| `404`  | `{ "detail": "Not found." }`                  | No record with that `external_id`. Returned by the retrieve and cancel endpoints.                                                                                                 |
| `405`  | `{ "detail": "Method \"...\" not allowed." }` | The HTTP method is not supported on that route, for example a `GET` on a create-only collection.                                                                                  |
| `429`  | `{ "error": { ... } }`                        | Rate limit exceeded. See below.                                                                                                                                                   |

Example validation error:

```json
{ "customer_id": ["No customer with external_id 'cus_999'."] }
```

## Rate limits

Limits apply **per API key and per organization**.

| Operation                                                                          | Limit                |
| ---------------------------------------------------------------------------------- | -------------------- |
| Writes: transaction create, finalize, and cancel, plus customer and product writes | 10 requests / second |
| Reads: any `GET`                                                                   | 50 requests / second |

A throttled request returns `429` with a `Retry-After` header and this body:

```json
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "The transaction write limit has been exceeded.",
    "retryable": true,
    "retry_after_seconds": 1,
    "request_id": "req_5f8d2c1e9b3a47d6a1c0e2f4b6d8a0c2"
  }
}
```

A `429` also carries `RateLimit-Limit` and `RateLimit-Remaining` headers. Retry with exponential backoff and jitter.

## Resources

* [Transactions](transactions.md): create invoices and credit notes, retrieve, and cancel.
* [Customers](customers.md): customer records and tax IDs.
* [Products](products.md): products and Sphere product tax codes.
