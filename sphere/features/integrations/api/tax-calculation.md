---
description: >-
  Details on our real-time tax calculation API as well as error codes and
  messages are provided below.
---

# Tax Calculation

## Tax Calculation Endpoint

`POST https://server.getsphere.com/tax_api/calculate_tax`

Returns the tax due on each line item for a given customer location, broken down by jurisdiction, plus a `sphere_tax_calculation_id` to store on the resulting payment or invoice.

### Authentication

Send your API key in the `X-API-KEY` header. Contact Sphere to obtain a key. See API.

### Prerequisites

| Requirement                                                                       | If missing                                  |
| --------------------------------------------------------------------------------- | ------------------------------------------- |
| Billing provider connected in Sphere, so your products appear in the Products tab | `product_id` values are not recognised      |
| A Product Tax Code on every product you send                                      | `400 invalid_product_tax_code`              |
| A registration in Sphere for the customer's region, with tax calculation enabled  | `200` with empty `tax_amounts` (no tax due) |

### Request

| Field                       | Type    | Required | Description                                                                                                                                                                     |
| --------------------------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `currency`                  | string  | Yes      | ISO 4217 code, lowercase (`usd`, `eur`).                                                                                                                                        |
| `line_items[]`              | array   | Yes      | At least one item. See below.                                                                                                                                                   |
| `customer_address`          | object  | Yes\*    | Address to calculate against. Provide `city`, `postal_code`, `country`; for the US also `state`, and `address1` for the most accurate result.                                   |
| `customer_id`               | string  | No       | Customer id in your billing system (`cus_…` for Stripe). Recommended.                                                                                                           |
| `pull_customer_address`     | boolean | No       | `true` to use the address stored on the billing-system customer instead of `customer_address`. Requires `customer_id`. Stripe only. Shipping address is preferred over billing. |
| `tax_id`                    | string  | No       | Customer's tax identification number (e.g. EU VAT number). See Tax IDs and exemptions.                                                                                          |
| `tax_exempt`                | boolean | No       | `true` to treat the customer as exempt for this calculation.                                                                                                                    |
| `include_taxability_reason` | boolean | No       | Include a `taxability_reason` on each result.                                                                                                                                   |
| `ship_from`                 | object  | No       | Origin address for physical goods. Can also be set per line.                                                                                                                    |

\* One of `customer_address` or `pull_customer_address: true` is required.

**Address object:** `address1`, `address2`, `city`, `state`, `postal_code`, `country` (all strings).

**Line item:**

| Field                  | Type    | Required | Description                                                                                               |
| ---------------------- | ------- | -------- | --------------------------------------------------------------------------------------------------------- |
| `id`                   | string  | No       | Your identifier for the line, echoed in the response. Use a unique id per line. Defaults to `product_id`. |
| `amount`               | integer | Yes      | Line total in the currency's minor unit. `10000` = $100.00.                                               |
| `product_id`           | string  | Yes      | Product id in your billing system (`prod_…` for Stripe). Must have a Product Tax Code in Sphere.          |
| `discount_amount`      | integer | No       | Discount on this line, minor units. Default `0`.                                                          |
| `tax_inclusive`        | boolean | No       | `true` if `amount` already includes tax. Default `false`.                                                 |
| `ship_to`, `ship_from` | object  | No       | Per-line address overrides for physical goods.                                                            |

### Response

`200` with:

```json
{
  "message": "Tax calculated successfully",
  "data": {
    "lines": [
      {
        "id": "line_1",
        "tax_amounts": [
          {
            "amount": 600,
            "taxable_amount": 10000,
            "tax_rate": {
              "percentage": 6.0,
              "inclusive": false,
              "display_name": "Sales Tax",
              "jurisdiction": "South Carolina",
              "country": "US",
              "state": "SC",
              "tax_type": "sales_tax"
            }
          }
        ]
      }
    ],
    "sphere_tax_calculation_id": "cfd1e35a-ccb8-4bf1-86ed-49e332be220b"
  }
}
```

| Field                                                     | Description                                                                                                                                                     |
| --------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `data.lines[]`                                            | One per line item, in request order.                                                                                                                            |
| `lines[].id`                                              | The `id` you sent, or the `product_id`.                                                                                                                         |
| `lines[].tax_amounts[]`                                   | One entry per tax applied to the line. Empty when no tax is due.                                                                                                |
| `tax_amounts[].amount`                                    | Tax, minor units.                                                                                                                                               |
| `tax_amounts[].taxable_amount`                            | Base the tax was calculated on, minor units.                                                                                                                    |
| `tax_amounts[].tax_rate.percentage`                       | Rate as a percentage (`6.0`, not `0.06`).                                                                                                                       |
| `tax_amounts[].tax_rate.inclusive`                        | Whether the tax is included in `amount`.                                                                                                                        |
| `tax_amounts[].tax_rate.display_name`                     | Name to show the customer.                                                                                                                                      |
| `tax_amounts[].tax_rate.jurisdiction`, `country`, `state` | Where the tax is due.                                                                                                                                           |
| `tax_amounts[].tax_rate.tax_type`                         | `sales_tax`, `vat`, `gst`, `hst`, `pst`, `qst`, `jct`, `igst`, `lease_tax`, `amusement_tax`, `communications_tax`, `retail_delivery_fee`, `rst`, `service_tax`. |
| `tax_amounts[].tax_rate.taxability_reason`                | Present when `include_taxability_reason` is `true`. E.g. `standard_rated`, `product_exempt`, `reverse_charge`.                                                  |
| `data.sphere_tax_calculation_id`                          | Store on the payment or invoice (Stripe `metadata`).                                                                                                            |

{% hint style="info" %}
A `200` with an empty `tax_amounts` array means no tax is due on that line — the customer is exempt, the sale is reverse-charged, or you have no active registration for the location. If the response has no `sphere_tax_calculation_id`, there is nothing to store.
{% endhint %}

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
