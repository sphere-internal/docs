---
description: >-
  Details on our real-time tax calculation API as well as error codes and
  messages are provided below.
---

# Tax Calculation

## Tax Calculation Endpoint

Returns the tax due on each line item for a given customer location, broken down by jurisdiction, plus a `sphere_tax_calculation_id` to store on the resulting payment or invoice.

### Authentication

Send your API key in the `X-API-KEY` header. Contact Sphere to obtain a key. See API.

### Prerequisites

| Requirement                                                                       | If missing                                  |
| --------------------------------------------------------------------------------- | ------------------------------------------- |
| Billing provider connected in Sphere, so your products appear in the Products tab | `product_id` values are not recognised      |
| A Product Tax Code on every product you send                                      | `400 invalid_product_tax_code`              |
| A registration in Sphere for the customer's region, with tax calculation enabled  | `200` with empty `tax_amounts` (no tax due) |

{% openapi-operation spec="sphere-transaction-api" path="/calculate_tax" method="post" %}
[OpenAPI sphere-transaction-api](https://server.getsphere.com/v1/openapi.json)
{% endopenapi-operation %}

### Example

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://server.getsphere.com/tax_api/calculate_tax \
  -H "Content-Type: application/json" \
  -H "X-API-KEY: YOUR_API_KEY" \
  -d '{
    "customer_id": "cus_RQx5PQDFSMH6Ku",
    "customer_address": {
      "address1": "Investors Boulevard",
      "city": "Myrtle Beach",
      "state": "SC",
      "postal_code": "29579",
      "country": "US"
    },
    "line_items": [
      { "id": "line_1", "amount": 10000, "product_id": "prod_RArEhwhXLfX5jF" }
    ],
    "currency": "usd"
  }'
```
{% endtab %}

{% tab title="Node.js" %}
```javascript
const res = await fetch('https://server.getsphere.com/tax_api/calculate_tax', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json', 'X-API-KEY': process.env.SPHERE_API_KEY },
  body: JSON.stringify({
    customer_id: 'cus_RQx5PQDFSMH6Ku',
    customer_address: { address1: 'Investors Boulevard', city: 'Myrtle Beach', state: 'SC', postal_code: '29579', country: 'US' },
    line_items: [{ id: 'line_1', amount: 10000, product_id: 'prod_RArEhwhXLfX5jF' }],
    currency: 'usd',
  }),
});
const { data } = await res.json();
```
{% endtab %}

{% tab title="Python" %}
```python
import os, requests

r = requests.post(
    "https://server.getsphere.com/tax_api/calculate_tax",
    headers={"X-API-KEY": os.environ["SPHERE_API_KEY"]},
    json={
        "customer_id": "cus_RQx5PQDFSMH6Ku",
        "customer_address": {"address1": "Investors Boulevard", "city": "Myrtle Beach",
                             "state": "SC", "postal_code": "29579", "country": "US"},
        "line_items": [{"id": "line_1", "amount": 10000, "product_id": "prod_RArEhwhXLfX5jF"}],
        "currency": "usd",
    },
)
r.raise_for_status()
data = r.json()["data"]
```
{% endtab %}
{% endtabs %}

### Tax IDs and exemptions

| Situation                                 | Send                                | Result                                                                                                                                   |
| ----------------------------------------- | ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| Business customer with a VAT / GST number | `"tax_id": "DE123456789"`           | Where a reverse charge applies, `tax_amounts` is empty; with `include_taxability_reason: true`, `taxability_reason` is `reverse_charge`. |
| Customer holds an exemption               | `"tax_exempt": true`                | No tax for this request.                                                                                                                 |
| You need to know why a line has no tax    | `"include_taxability_reason": true` | Reason on each result, e.g. `product_exempt`, `reverse_charge`.                                                                          |

```json
{
  "customer_id": "cus_RQx5PQDFSMH6Ku",
  "tax_id": "DE123456789",
  "include_taxability_reason": true,
  "customer_address": { "city": "Berlin", "postal_code": "10115", "country": "DE" },
  "line_items": [{ "id": "line_1", "amount": 10000, "product_id": "prod_RArEhwhXLfX5jF" }],
  "currency": "eur"
}
```

{% hint style="info" %}
Reverse-charge treatment for business customers depends on your account configuration. Contact Sphere to enable it.
{% endhint %}

### Errors

All errors are `{"type": "api_error", "code": "…", "message": "…"}`.

| Status | `code`                          | Cause                                                           | Resolution                                                  |
| ------ | ------------------------------- | --------------------------------------------------------------- | ----------------------------------------------------------- |
| 401    | `unauthorized_access`           | Missing or invalid API key                                      | Check the `X-API-KEY` header and that the key is active     |
| 400    | `validation_errors`             | Malformed request                                               | `message` lists the failing fields                          |
| 400    | `customer_tax_location_invalid` | Address incomplete or unresolvable                              | Check `city`, `postal_code`, `country` (and `state` for US) |
| 400    | `invalid_product_tax_code`      | A `product_id` has no tax code in Sphere                        | Assign one in the Products tab                              |
| 400    | `unsupported_feature`           | `pull_customer_address` not available for your billing provider | Send `customer_address`                                     |
| 400    | `customer_address_pull_failed`  | Address could not be read from the billing provider             | Send `customer_address`                                     |
| 429    | `too_many_requests`             | Rate limit                                                      | Back off and retry                                          |
| 500    | `internal_server_error`         | Unexpected error                                                | Retry; contact support if it persists                       |
