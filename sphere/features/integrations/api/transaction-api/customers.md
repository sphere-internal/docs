---
description: >-
  Create, update and retrieve the customers used to calculate tax and generate
  invoices.
---

# Customers

Customers represent the individuals or organizations on your taxable transactions. A customer record stores billing and shipping addresses, tax identification numbers and exemption status, and is referenced as `customer_id` on a transaction.

{% openapi-operation spec="sphere-transaction-api" path="/customers" method="post" %}
[OpenAPI sphere-transaction-api](https://server.getsphere.com/v1/openapi.json)
{% endopenapi-operation %}

{% openapi-operation spec="sphere-transaction-api" path="/customers/{external_id}" method="get" %}
[OpenAPI sphere-transaction-api](https://server.getsphere.com/v1/openapi.json)
{% endopenapi-operation %}
