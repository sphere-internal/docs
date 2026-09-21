---
description: Instructions on how to setup Sphere's API while using Stripe-hosted Checkout
hidden: true
---

# Stripe-hosted Checkout

## Overview

To create a Stripe-hosted Checkout session with taxes calculated using the Sphere API, follow these steps:

1. Call the Sphere Tax Calculation API to retrieve applicable tax rates.
2. Create Stripe Tax Rates corresponding to each rate provided by Sphere. For details, refer to [Stripe’s Tax Rate documentation](https://docs.stripe.com/api/tax_rates/create).
3. Create a Stripe Checkout Session (Step 3a) or an Invoice (Step 3b) and, associate the Stripe tax rates derived from the Sphere API calculations.

***

## **Step 1: Call the Sphere Tax Calculation API** <a href="#step-1-call-the-sphere-tax-calculation-api" id="step-1-call-the-sphere-tax-calculation-api"></a>

Make a **POST** request to the Sphere Tax Calculation API to retrieve applicable tax rates for a given customer address and product.

**Endpoint:** [https://server.getsphere.com/tax\_api/calculate\_tax](https://server.getsphere.com/tax_api/calculate_tax)

**Request Headers:**

* **Header Key:** X-API-KEY
* **Header Value:** YOUR\_API\_KEY

**Sample Request:**

```sh
curl -X \
 POST https://server.getsphere.com/tax_api/calculate_tax \
-H \
 "Content-Type: application/json" \
-H \
 "X-API-KEY: sph_api_key" \
-d '{
  "customer_address": {
    "address1": "Investors Boulevard",
    "city": "Myrtle Beach",
    "state": "SC",
    "postal_code": "29579",
    "country": "US"
  },
  "line_items": [
    {
      "amount": 10000,
      "product_id": "prod_RArEhwhXLfX5jF",
      "discount_amount": 0,
      "tax_inclusive": false
    }
  ],
  "currency": "usd"
}'

```

**Sample Response:**

```json
{
  "lines": [
    {
      "id": "prod_RArEhwhXLfX5jF",
      "tax_amounts": [
        {
          "amount": 900,
          "taxable_amount": 10000,
          "tax_rate": {
            "percentage": 6.0,
            "inclusive": false,
            "display_name": "South Carolina",
            "jurisdiction": "South Carolina",
            "country": "US",
            "state": "SC"
          }
        }
      ]
    }
  ],
  "sphere_tax_calculation_id": "cfd1e35a-ccb8-4bf1-86ed-49e332be220b"
}

```

***



## **Step 2: Create Stripe Tax Rate Objects**

For each tax rate returned by the Sphere Api, create a corresponding Stripe Tax Rate object. You’ll need to pass Stripe the tax details provided by the Sphere Api. [Stripe’s Tax Rate documentation](https://docs.stripe.com/api/tax_rates/create).

**Sample Requests:**

```bash
curl https://api.stripe.com/v1/tax_rates \
  -u "your-stripe-api-key-here" \
  -d display_name="South Carolina" \
  -d description="South Carolina Tax" \
  -d percentage=9.0 \
  -d jurisdiction="South Carolina" \
  -d inclusive=false
```

**Note:**

* Replace **your-stripe-api-key-here** with your actual Stripe API key.
* All other data in the payload is sourced from the Sphere Tax API response. **(Refer to the sample response in Step 1 for details.)**

After each successful request, Stripe will return a Tax Rate object that includes an **id**. Save each **id**, as you will need these in Step 3. Sample Tax Rate ID:

txr\_1QJqyzLSmZDVqIUdFs615ZB6

***



## **Step 3a: Create the Stripe Checkout Session**

To initiate a checkout session or directly create an invoice, you’ll need to provide Stripe with the Tax Rate ID (from Step 2) and other relevant details. For more guidance, refer to [Stripe’s Checkout Session documentation](https://docs.stripe.com/api/checkout/sessions/create).

**Note:** Make sure to include the **sphere\_tax\_calculation\_id** (from Step 1) in the metadata field of the Stripe session. This ID links Sphere’s tax calculation to the Stripe checkout session, ensuring accurate tracking. The method for including the sphere\_tax\_calculation\_id varies between subscriptions and one-time payments.

#### **1. Subscription Checkout Session**

A subscription checkout is used for recurring payments where the customer is billed on a regular basis (e.g., monthly or annually). The checkout session links to Stripe’s subscription system, allowing for automatic renewals, billing cycles, and management of recurring charges.

For subscriptions, add the **sphere\_tax\_calculation\_id** within the `subscription_data[metadata]` field.

**Sample cURL for Subscription:**

```bash
curl https://api.stripe.com/v1/checkout/sessions \
  -u "your-stripe-api-key-here" \
  --data-urlencode "success_url=redirect-url" \
  -d "line_items[0][price]=stripe-price-id" \
  -d "line_items[0][quantity]=1" \
  -d "line_items[0][tax_rates][0]=txr_1QJqyzLSmZDVqIUdFs615ZB6" \
  -d "subscription_data[metadata][sphere_tax_calculation_id]=cfd1e35a-ccb8-4bf1-86ed-49e332be220b" \
  -d "customer=stripe-customer-id"
  -d "mode=subscription"
```

**Note:**

* Replace **your-stripe-api-key-here** with your actual Stripe API key.
* The **Tax Rate ID** (txr\_1QJqyzLSmZDVqIUdFs615ZB6) used in the cURL, was generated in Step 2.
* The **sphere\_tax\_calculation\_id**: cfd1e35a-ccb8-4bf1-86ed-49e332be220b was provided by the Sphere Api, in Step 1.
* Replace the **stripe-price-id** and **stripe-customer-id** with actual values.



**2. One-Time Payment Checkout Session**

A one-time payment checkout is designed for a single transaction, with no additional charges applied unless a new payment session is created. This is commonly used for one-off purchases or services. Please note that invoices are not automatically generated for One-Time Payment Checkout Sessions.

To enable invoice creation, you need to set the property **invoice\_creation\[enabled]=true**.

Additionally, the methods for passing **sphere\_tax\_calculation\_id** differ depending on whether invoice generation is enabled.

Here’s how you should include the **sphere\_tax\_calculation\_id** in your request:

**Sample cURL for One-Time Payment&#x20;**_**with**_**&#x20;invoice generation:**

```bash
curl https://api.stripe.com/v1/checkout/sessions \
  -u "your-stripe-api-key-here" \
  --data-urlencode "success_url=redirect-url" \
  -d "line_items[0][price]=stripe-price-id" \
  -d "line_items[0][quantity]=1" \
  -d "line_items[0][tax_rates][0]=txr_1QJqyzLSmZDVqIUdFs615ZB6" \
  -d "invoice_creation[enabled]=true" \
  -d "invoice_creation[invoice_data][custom_fields][0][name]=sphere_tax_calculation_id" \
  -d "invoice_creation[invoice_data][custom_fields][0][value]=cfd1e35a-ccb8-4bf1-86ed-49e332be220b" \
  -d "customer=stripe-customer-id"
  -d "mode=payment"
```

**Sample cURL for One-Time Payment&#x20;**_**without**_**&#x20;invoice generation:**

```bash
curl https://api.stripe.com/v1/checkout/sessions \
  -u "your-stripe-api-key-here" \
  --data-urlencode "success_url=redirect-url" \
  -d "line_items[0][price]=stripe-price-id" \
  -d "line_items[0][quantity]=1" \
  -d "line_items[0][tax_rates][0]=txr_1QJqyzLSmZDVqIUdFs615ZB6" \
  -d "metadata[sphere_tax_calculation_id]=cfd1e35a-ccb8-4bf1-86ed-49e332be220b" \
  -d "customer=stripe-customer-id"
  -d "mode=payment"
```

**Note:**

* Replace **your-stripe-api-key-here** with your actual Stripe API key.
* The **Tax Rate ID** (txr\_1QJqyzLSmZDVqIUdFs615ZB6) used in the cURL, was generated in Step 2.
* The **sphere\_tax\_calculation\_id**: cfd1e35a-ccb8-4bf1-86ed-49e332be220b was provided by the Sphere Api, in Step 1.
* Replace the **stripe-price-id** and **stripe-customer-id** with actual values.<br>

***



## **Step 3b: Direct Invoice Creation (One-Time Payment)**

Creating an invoice directly for a one-time payment involves two steps:

> **a. Generate the Invoice Object**\
> First, create the invoice object for the customer.

> **b. Create Invoice Items with Tax Rates**\
> Next, add items to the invoice and apply tax rates to each item, associating them with the invoice object.

**a. Creating an Invoice**

To issue a direct invoice for a customer, use the following cURL command. The invoice will automatically charge the customer upon finalization.

\
**Sample cURL for Generating a Stripe Invoice:**

```bash
curl https://api.stripe.com/v1/invoices \
-u "your-stripe-api-key-here" \
-d "customer=stripe-customer-id" \
-d "auto_advance=true" \
-d "collection_method=charge_automatically" \
-d "metadata[sphere_tax_calculation_id]=cfd1e35a-ccb8-4bf1-86ed-49e332be220b"
```

**Parameters:**

* **customer:** The ID of the customer to whom the invoice is issued.
* **auto\_advance:** When set to true, the invoice will automatically finalize after it is created.
* **collection\_method:** charge\_automatically means Stripe will charge the customer’s stored payment method.
* **metadata\[sphere\_tax\_calculation\_id]:** Associates the invoice with an external tax calculation ID for tracking.

**b. Adding Invoice Items with Tax Rates**

Once the invoice is created, you can add items to it, each with applicable tax rates.

**Sample cURL for Adding Stripe Invoice Items:**

```bash
curl https://api.stripe.com/v1/invoiceitems \
-u "your-stripe-api-key-here" \
-d "customer=stripe-customer-id" \
-d "price=stripe-price-id" \
-d "quantity=1" \
-d "description=Product Name" \
-d "invoice=in_1QJwL6LSmZDVqIUd8wrBeGMK" \
-d "tax_rates[]=txr_1QJqyzLSmZDVqIUdFs615ZB6"
```

**Parameters:**

* **customer:** The ID of the customer being invoiced.
* **price:** The price ID of the product or service being added to the invoice.
* **quantity:** Quantity of the product being invoiced.
* **description:** A description for the invoice item, such as the product name.
* **invoice:** The ID of the invoice to which this item should be added.
* **tax\_rates\[]:** List of tax rate IDs to apply to the item.

**Note:**

* Replace **your-stripe-api-key-here** with your actual Stripe API key.
* The **Tax Rate ID** (txr\_1QJqyzLSmZDVqIUdFs615ZB6) used in the cURL, was generated in Step 2.
* The **sphere\_tax\_calculation\_id**: cfd1e35a-ccb8-4bf1-86ed-49e332be220b was provided by the Sphere Api, in Step 1.
* Replace the **stripe-price-id** and **stripe-customer-id** with actual values.
