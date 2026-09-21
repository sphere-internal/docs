---
description: Create products, assign Sphere product tax codes, and retrieve them.
---

# Products

A product represents something you sell. Assign a Sphere **product tax code** so the tax engine can determine taxability and rates. Reference a product as `product_id` on a transaction line.

{% openapi-operation spec="sphere-transaction-api" path="/products" method="post" %}
[OpenAPI sphere-transaction-api](https://server.getsphere.com/v1/openapi.json)
{% endopenapi-operation %}

{% openapi-operation spec="sphere-transaction-api" path="/products/{external_id}" method="get" %}
[OpenAPI sphere-transaction-api](https://server.getsphere.com/v1/openapi.json)
{% endopenapi-operation %}
