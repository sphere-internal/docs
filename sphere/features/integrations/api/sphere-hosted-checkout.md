---
description: >-
  Create a Sphere-hosted checkout page where Sphere calculates tax and Stripe
  processes payment in a single API call.
---

# Sphere-hosted Checkout

## Overview

Sphere-hosted Checkout is the simplest way to take a Stripe payment with Sphere tax applied. You make **one** server-side API call with your cart; Sphere returns a hosted checkout URL you redirect the buyer to. Sphere renders the checkout page, collects the buyer's address (and tax ID where it applies), calculates tax live, and Stripe processes the payment.

Unlike the Stripe Elements and Stripe-hosted Checkout flows, you do **not** call the Tax Calculation API or create Stripe Tax Rate objects yourself. Sphere handles tax end to end.

1. Create a hosted checkout session (one API call) and receive a checkout URL.
2. Redirect the buyer to the URL.
3. The buyer completes checkout on Sphere's page; Sphere applies tax and Stripe takes payment.

***

## Step 1: Create a hosted checkout session

Make a **POST** request with your Sphere API key. The `checkout_session` object accepts standard [Stripe Checkout Session](https://docs.stripe.com/api/checkout/sessions/create) parameters (line items, mode, and so on). Sphere forwards them to Stripe and layers tax on top.

**Endpoint:** `https://api.getsphere.com/v1/stripe/checkout/hosted_sessions`

**Request Headers:**

* **Header Key:** X-API-KEY
* **Header Value:** YOUR\_API\_KEY

**Sample Request (one-time payment):**

```sh
curl -X POST https://api.getsphere.com/v1/stripe/checkout/hosted_sessions \
  -H "Content-Type: application/json" \
  -H "X-API-KEY: sph_api_key" \
  -d '{
    "success_url": "https://your-site.com/order/complete",
    "publishable_key": "pk_live_your_key",
    "checkout_session": {
      "mode": "payment",
      "line_items": [
        {
          "price_data": {
            "currency": "usd",
            "product_data": { "name": "Pro plan" },
            "unit_amount": 4000
          },
          "quantity": 1
        }
      ]
    }
  }'
```

**Sample Response:**

```json
{ "url": "https://checkout.getsphere.com/s/#<token>" }
```

\
**Notes:**

* **success\_url** (required): where the buyer is sent after a successful payment.
* **publishable\_key:** your Stripe publishable key (`pk_…`), used to mount the session in the buyer's browser. It is not a secret. On a direct-install Connect connection you may omit it, and Sphere uses the connected account's own key.
* **checkout\_session:** your Stripe Checkout Session parameters. Use `price` IDs instead of `price_data` if you prefer, set `"mode": "subscription"` for recurring billing, `"allow_promotion_codes": true`, and so on.
* Sphere owns the hosted experience and tax, so a few Stripe parameters are set for you and **rejected if included**: `ui_mode`, `return_url`, `cancel_url`, `success_url` (use the top-level `success_url` instead), and the tax parameters `automatic_tax`, `tax_id_collection`, and the line-item `tax_rates` and `dynamic_tax_rates`.
* Optional **prefill** pre-populates the form: `"prefill": { "country": "US", "email": "buyer@example.com", "name": "Ada Lovelace" }`.

***

## Step 2: Redirect the buyer

Redirect the buyer to the `url` from Step 1. On Sphere's hosted page the buyer enters their email and address (and a tax ID where reverse charge applies), Sphere calculates tax from the address in real time, and Stripe's Payment Element collects payment. On success the buyer is returned to your `success_url`.

***

## What Sphere handles

* **Tax, calculated live** from the buyer's address - US sales tax, EU/UK VAT, GST, and more.
* **Reverse charge** applied automatically when the buyer enters a valid tax ID in a supported jurisdiction.
* **One-time payments and subscriptions**, via `mode`.
* **The hosted page**, the Stripe Payment Element, and the order summary.

**Notes:**

* Checkout sessions expire 24 hours after creation.
* **(Connect)** A platform creating sessions on a connected account can pass `stripe_account_id`.

***

## API reference

The full request and response schema for the hosted checkout endpoint, generated from the Sphere API's OpenAPI specification.

{% hint style="info" %}
The `checkout_session` object accepts the standard [Stripe Checkout Session](https://docs.stripe.com/api/checkout/sessions/create) create parameters such as `line_items`, `mode`, `subscription_data`, `allow_promotion_codes`, and so on - except the few Sphere sets for you (listed under Step 1).
{% endhint %}

{% openapi-operation spec="sphere-transaction-api" path="/stripe/checkout/hosted_sessions" method="post" %}
[OpenAPI sphere-transaction-api](https://server.getsphere.com/v1/openapi.json)
{% endopenapi-operation %}
