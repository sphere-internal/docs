---
description: Create invoices and credit notes, retrieve them, and cancel a transaction.
---

# Transactions

A transaction is an invoice (`type: debit`) or a credit note (`type: credit`). Create it as a `pending` draft or `finalized` in one call and finalizing runs the tax engine and returns `subtotal`, `tax` and `total`. A credit note sets `type: credit` and references the original invoice with `original_transaction_id`.

{% openapi-operation spec="sphere-transaction-api" path="/transactions" method="post" %}
[OpenAPI sphere-transaction-api](https://server.getsphere.com/v1/openapi.json)
{% endopenapi-operation %}

{% openapi-operation spec="sphere-transaction-api" path="/transactions/{external_id}" method="get" %}
[OpenAPI sphere-transaction-api](https://server.getsphere.com/v1/openapi.json)
{% endopenapi-operation %}

{% openapi-operation spec="sphere-transaction-api" path="/transactions/{external_id}/cancel" method="post" %}
[OpenAPI sphere-transaction-api](https://server.getsphere.com/v1/openapi.json)
{% endopenapi-operation %}
