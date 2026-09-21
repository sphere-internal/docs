# Transaction Export

The transaction export produces a CSV file with one row per **line item**, not one row per transaction. A transaction with 3 line items appears as 3 rows, each sharing the same transaction-level fields (dates, status, customer) but with distinct line item details and tax calculations.

When filters are applied, the export includes metadata rows at the top of the file showing the organization name, date range, and filter selections.

***

### Identity & Source

**Transaction ID** — The invoice number or identifier from your billing system (e.g., `INV-0042`). If no human-readable invoice number exists, the system's internal identifier is used instead (e.g., `in_1Abc123`).

**Customer Id** — The customer's identifier in your billing system (e.g., Stripe's `cus_xxx`).

**Customer Name** — The customer's name from their billing record. If no name is on file, their email address is shown instead.

**Source Transaction Type** — The transaction type as reported by your billing system. Common values include `invoice`, `credit_note`, `refund`, and `charge`. This is the billing provider's classification, not Sphere's — see **Tax Reporting Type** for Sphere's tax-specific classification.

**Line Item Id** — The billing system's identifier for this specific line item within the transaction.

**Transaction Status** — The status of the transaction in Sphere. Values include `paid`, `finalized`, `cancelled`, and `void`.

**Transaction Source** — The billing integration this transaction came from, such as `stripe`, `quickbooks`, `orb`, or `chargebee`.

***

### Region & Address

**Region** — The name of the tax jurisdiction Sphere assigned to this transaction (e.g., "California", "Ontario", "United Kingdom"). If Sphere could not determine a tax jurisdiction, this field is empty.

**Source-to Address** — The address Sphere used for tax determination, shown as a single comma-separated string. Depending on the integration and product type, this may be the customer's shipping address, billing address, or a normalized version thereof.

***

### Dates & Tax Reporting

**Transaction Finalized Date** — When the invoice was finalized or issued by the billing system.

**Transaction Paid Date** — When payment was received.

**Transaction Cancelled Date** — When the invoice was voided or cancelled.

**Transaction Marked Uncollectible Date** — When the invoice was written off as uncollectible (bad debt).

**Tax Reporting Date** — The date Sphere uses to assign this row to a filing period. This is not always the same as the finalized date:

| Scenario                                   | Tax Reporting Date is                                            |
| ------------------------------------------ | ---------------------------------------------------------------- |
| Normal sale                                | The finalized date                                               |
| Refund or credit note                      | The finalized date of the refund itself                          |
| Cancellation or bad debt                   | The cancellation or marked uncollectible date                    |
| Credit note offsetting a cancelled invoice | The original invoice's cancellation or marked uncollectible date |

For example, if an invoice was finalized in January but cancelled in March, it appears as a `sale` in January's filing period (increasing tax liability) and as a `cancellation` in March's filing period (decreasing liability or creating a carryforward credit).

**Tax Reporting Type** — Sphere's classification of this row for tax filing purposes. Determines whether amounts are positive (revenue) or negative (reversal).

| Value                           | Direction | Meaning                                                                                            |
| ------------------------------- | --------- | -------------------------------------------------------------------------------------------------- |
| `sale`                          | Positive  | Invoice finalized in the reporting period                                                          |
| `current_period_refund`         | Negative  | Credit note where both the refund and original invoice were finalized in the same reporting period |
| `past_period_refund`            | Negative  | Credit note finalized in this period, but the original invoice was in a prior period               |
| `cancellation`                  | Negative  | Invoice cancelled or marked uncollectible during this period                                       |
| `cancelled_invoice_credit_note` | Positive  | Credit note that offsets a cancelled invoice — counteracts the cancellation's negative amount      |

A single transaction can appear in multiple reporting periods with different types. For example, a cancelled invoice shows as a `sale` in the period it was finalized, then as a `cancellation` in the period it was cancelled.

***

### Line Item & Product

**Line item Description** — The line item description from your billing system (e.g., "Pro Plan - Monthly", "Usage charges for API calls").

**Product Name** — The name of the product in Sphere linked to this line item.

**Product Tax Code** — The product tax code assigned in Sphere, which determines how the item is taxed. May include modifier codes separated by `/` (e.g., `10010/digital/recurring`).

***

### Tax Status

**Taxable** — Whether any portion of this line item was subject to tax. `True` if tax applies in at least one jurisdiction, `False` if fully exempt or non-taxable.

**Tax Exemption Reason** — Why tax was not charged on this line item. Common values include "Reverse Charge", "Exempt Product", and "Non Taxable Region". If multiple reasons apply, they are listed separated by commas. Empty when the line item is taxable.

**Tax ID Number Status** — The verification status of the customer's tax ID that was used for reverse charge exemption. Only populated when the transaction has a reverse charge exemption and the customer has a matching tax ID for the transaction's region. Possible values:

| Status        | Meaning                                                                      |
| ------------- | ---------------------------------------------------------------------------- |
| `verified`    | Verified through a tax ID validation service (e.g., VIES)                    |
| `valid`       | Passed format checks, but no validation service available to verify directly |
| `failed`      | Failed verification with the validation service                              |
| `invalid`     | Failed format checks                                                         |
| `unavailable` | Validation service was temporarily unreachable — treated as accepted         |
| `unsupported` | Tax ID verification is not supported in this region                          |

**Tax ID Number** — The customer's tax identification number (e.g., a VAT number like `DE123456789`). Shown when the transaction has a reverse charge exemption and the customer has a matching tax ID for the transaction's country, regardless of verification status.

**Tax ID Added Date** — When the tax ID was added to Sphere.

***

### Amounts — Transaction Currency

All amounts reflect the line item level, not the full transaction. Refunds and credit notes appear as negative amounts.

**Transaction Currency** — The currency the transaction was originally conducted in (e.g., `usd`, `eur`, `gbp`).

**Transaction Total** — The line item amount before discounts and tax.

**Transaction Discount** — The discount applied to this line item.

**Gross Sales in Transaction Currency** — The line item amount after discounts but before tax. This is Transaction Total minus Transaction Discount.

**Taxable Sales in Transaction Currency** — The portion of the line item that was subject to tax. This may be less than Gross Sales if part of the line item is exempt.

**Tax Collected in Transaction Currency** — The tax amount actually charged to the customer by the billing provider.

**Tax Calculated in Transaction Currency** — The tax amount Sphere's tax engine computed for this line item. This may differ from Tax Collected if the billing provider applied a different rate than what Sphere calculated.

***

### Amounts — Filing Currency

When a transaction's currency differs from the tax jurisdiction's filing currency, these columns show the amounts converted at the applicable exchange rate. If the transaction currency and filing currency are the same, these values match the transaction currency amounts.

**Transaction Filing Currency** — The currency of the tax jurisdiction where this transaction is filed (e.g., `USD`, `GBP`, `CAD`).

**Filing Currency Exchange Rate** — The exchange rate used to convert from the transaction currency to the filing currency.

**Gross Sales in Filing Currency** — Gross Sales converted to the filing currency.

**Taxable Sales in Filing Currency** — Taxable Sales converted to the filing currency.

**Tax Collected in Filing Currency** — Tax Collected converted to the filing currency.

**Tax Calculated in Filing Currency** — Tax Calculated converted to the filing currency.

***

### Filing & Region Status

**Filing Status** — Whether this transaction has been included in a tax filing.

| Value            | Meaning                                              |
| ---------------- | ---------------------------------------------------- |
| `Filed`          | Included in a submitted tax filing                   |
| `Pending filing` | Assigned to a filing that has not yet been submitted |
| `Not Filed`      | Not yet included in any filing                       |

**Region Status** — Your organization's tax obligation status in this transaction's region at the time of the transaction.

| Value        | Meaning                                                                                                                       |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| `Pre-breach` | Your organization had not exceeded the economic and/or physical nexus threshold in this region at the time of the transaction |
| `Breached`   | The nexus threshold had been exceeded, but you had not yet registered to collect tax                                          |
| `Registered` | You were actively registered to collect tax in this region                                                                    |

***

### Tax Breakout by Jurisdiction

The final 18 columns break down the tax by jurisdiction level. Each of the six jurisdiction levels has three columns: **Name**, **Tax Rate**, and **Tax Amount**.

The six levels are: **Country**, **State**, **County**, **City**, **District**, and **Transit Authority**.

For example, the Country columns are `Tax Breakout - Country Name`, `Tax Breakout - Country Tax Rate`, and `Tax Breakout - Country Tax Amount`. The same pattern applies to all six levels.

* **Name** — The jurisdiction name (e.g., "California", "New York City")
* **Tax Rate** — The tax rate applied at this level, shown as a percentage (e.g., `6.5` for 6.5%)
* **Tax Amount** — The tax amount charged at this level

If no tax applies at a given level, those columns are empty. A transaction taxed only at the state level would have values in the State columns and empty Country, County, City, District, and Transit Authority columns.
