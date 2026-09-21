---
description: >-
  Real-time tax calculation allows you to instantly add the correct tax to each
  transaction.
icon: calculator
---

# Calculation

**Overview**&#x20;

Sphere has built it's own AI-enabled tax engine that is able to instantly calculate the right tax to apply at the point of transaction (e.g. invoices, checkout).

There are two key things that our engine needs to calculate tax correctly:

1. _The product being sold and whether its attributes are taxable in the region its being sold into_; this is know as the **Tax Determination**
2. _The address of the customer_; which not only impacts taxability but the **Rate** that must be used

We've provided more details on both these concepts below as well as how we integrate our tax engine into the billing flows of our customers.

### **Tax Determination**&#x20;

{% embed url="https://drive.google.com/file/d/1kDk-FA3RaIwCVrrErOHYz2faEEDZAyB6/view?usp=sharing" %}
Walkthrough of the Tax Determination feature in Sphere
{% endembed %}

In order to accurately assess whether your product is taxable in a region we need to understand the various attributes of your products that could impact its taxability.&#x20;

When you onboard onto Sphere we pull in all your products from your billing systems and ask you to assign a Product Tax Code to each.

<figure><img src="../.gitbook/assets/Assign code.png" alt=""><figcaption><p>The Products feature allows you to assign Product Tax Codes to each of your products </p></figcaption></figure>

To assign a Product Tax Code, we ask a series of questions which capture the key taxability characteristics of your product. At the end of the questions, a Product Tax Code is suggested.&#x20;

<figure><img src="../.gitbook/assets/Tax code (1).png" alt=""><figcaption><p>Questions are asked to determine the taxable characteristics of your products</p></figcaption></figure>

Sphere has created a taxonomy of Product Tax Codes for each key region globally. We then map this taxonomy to the tax law in that region and how that region would tax the various attributes that are contained in a particular Product Tax Code.&#x20;

Traditionally, tax software vendors have done this mapping process manually which leads to errors and the mapping going stale as legislation changes. At Sphere, we've indexed the indirect tax legislation of every key economic region to not only map our taxonomy but to monitor the legislation for ongoing changes. This means global coverage for our customers that is always kept up to date to ensure you are always in compliance.&#x20;

Note that our in-house tax experts **always review and verify** the mappings that our AI system produces to ensure that outputs are accurate.&#x20;

### Product Tax Codes

After you've assigned your Product Tax Code, you'll see it in the Tax Code column of the Products tab, followed by a label explaining the category.&#x20;

Using the below example, `62/MS-01 - SaaS - Business Canned ...` the Tax Code is `62/MS-01`  followed by a descriptive label that defines the category the product is in. In this case, `SaaS - Business Canned Software` , `Mississippi`&#x20;

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

The Tax Code itself is a number, optionally followed by one or more `/`-separated modifiers that capture region- or attribute-specific variations — for example `62`, `62/NJ-02`, or `62/NJ-02/MB-02`.

You can copy the Tax Code from this page, or via the export button on the same page which will send you an email attachment with the Tax Code as a column.&#x20;

| Product          | Tax Code   |
| ---------------- | ---------- |
| Starter Plan     | `62`       |
| Growth Plan      | `62/NJ-02` |
| Enterprise Plan  | `63/IL-01` |
| Analytics Add-on | `67/MB-02` |

### Best practices around Product Tax Code assignment&#x20;

Below are a number of best practices that we advise companies to adhere to when assigning Product Tax Codes:&#x20;

* **Ensure ALL of your products have Product Tax Codes** - our Monitoring and Calculation features both require Product Tax Codes to be assigned to all products. With respect to Monitoring, if Product Tax Codes are missing (even for historical products that are no longer in use), the transactions associated with those products won't be counted in your nexus analysis. With respect to Calculation, if a product being sold doesn't have a Product Tax Code, we won't be able to calculate tax on the transaction and an error will be return in your billing flow.
* **Avoid creating one-off products** - we often see businesses creating individual products for every invoice they send out / every different customer. This causes issues as you have to assign tax codes to every single product you create which can be tedious and lead to errors. Every major billing system usually has a way to setup products so that you can reuse them in subsequent invoices (e.g. Stripe has a Product Catalog that allows you to standardize your product classes and avoid creating one-off products). This means you just need to setup your product tax codes once!
* **Never bundle your products** - we also see businesses bundling services with a software / tangible product, or bundling a B2C and B2B offering. This causes big issues with tax authorities as these different offerings have very different tax determinations, meaning you will over / underpay tax. You need to split these products out in your billing system so that seperate tax codes can be applied to each&#x20;
* **If unsure, ask your Sphere rep** - we've dealt with tax determinations for many different types of products and can provide you with information on request so that you can make the right choice.&#x20;

## **Rates**&#x20;

Once we know whether your product is taxable in the region you are selling it in, we then need to determine the rate to assign.

Rates change in every region and in some instances change at the rooftop level (especially in the US!).

Sphere uses advanced scraping technology to collate and monitor rates across tax authority websites, information bulletins and third party sources around the world. This means we have the world's broadest, best maintained global rate database that we offer to our customers.&#x20;

### Region assignment&#x20;

To ensure we are pulling the correct rate for each transaction, we need to be able to accurately assign a taxable region to each transaction. However, quality of location data can be variable and so we have a priority ranking mechanism to assign the correct taxable region.

We've included a simplified priority ranking mechanism below (although this changes based on billing system used):

1. Shipping Address of Transaction
2. Shipping Address of Customer (there is often a customer profile / object within billing systems)
3. Billing Address of Transaction
4. Billing Address of Customer
5. Card Issue Country

Ultimately, tax authorities want to know where the services / goods are being used, hence shipping address is always seen as the gold standard in allocating the taxable region.

## **Tax ID Verification**

{% embed url="https://drive.google.com/file/d/1eJew95IgALhgihtOh723lalLp9tY8_--/view?usp=sharing" %}
How to add a **Tax Exemption Certificates**
{% endembed %}

In some instances, tax is not collected on transactions due to the nature of the recipient. There are two main examples of this which Sphere caters to.

### Cross border B2B software sales

When selling internationally, many countries allow B2B software vendors to apply what is called the _reverse charge mechanism,_ where a non-resident vendor can push the tax obligation onto the customer IF the following conditions are met:

* The customer provides a valid VAT / GST ID&#x20;
* The invoice provided to the customer notes that the reverse charge mechanism is being applied as well as including the validated ID number&#x20;

Sphere has connected to tax ID verifications APIs from each region around the world to help you validate VAT IDs in two ways:

_**Automatically**_: we can extract VAT IDs directly from billing systems and return whether the ID was successfully validated or not (if it was then tax will not be applied to the transaction, if not tax will be applied).

_**Manually**_: In the Customers section of the Sphere app, click on any customer, scroll down to the Tax ID table, click 'Add Tax ID' and then enter the region and the Tax ID number. A validation / error message will be returned.

#### Tax ID Status Definitions

Sphere uses the following statuses to describe the state of each tax ID:

* `Verified` - The tax ID has been verified with the tax ID database with the relevant authority. Tax IDs in this status **apply** the reverse charge mechanism.
* `Pending` - The tax ID is pending verification with the tax ID database with the relevant tax authority. Tax IDs with this status **apply** the reverse charge mechanism.
*   `Valid` - The tax ID format is valid, but verification with the relevant tax authority is not available.

    Tax IDs with this status **apply** the reverse charge mechanism.&#x20;
* `Invalid` - The tax ID format is invalid. Tax IDs with this status **do not apply** the reverse charge mechanism.
* `Failed` - The tax ID failed verification with the relevant authority's tax ID database (for example, it was not found in the relevant tax authority database). Tax IDs with this status **do not apply** the reverse charge mechanism.
* `Unsupported` - the region of this tax ID is not supported.

<figure><img src="../.gitbook/assets/Tax id.png" alt=""><figcaption><p>International VAT and GST IDs can be added and validated within the Customers feature in Sphere</p></figcaption></figure>

### **Tax Exemption Certificates**&#x20;

In the US, customers may hold tax exemption certificates for a variety of different reasons (they may be resellers or in an exempt industry category). This means that these customers are exempt from sales tax.

Sphere offers Exemption Certificate Management in the Customers section of the Sphere app. If a customer sends you their certificate, click on the relevant customer in the Customers section, scroll down to the Tax ID table and upload the tax exemption certificate. You can also select the date for which the certificate is valid and Sphere will send you an email to remind you to request a new certificate from your customer when it expires.



