# Transaction Export

Export your Sphere transaction data programmatically via CSV. The API uses an async job model: you create an export, poll for completion, and download the file via a presigned URL.

### Prerequisites

* A Sphere account with an active billing provider connection (via the **Integrations** section of the app).
* An API key (see **Settings > API Keys** in the Sphere dashboard). The key must be included in every request header.

### Authentication

All endpoints require an API key in the request header.

* **Header Key:** `X-API-KEY`
* **Header Value:** `YOUR_API_KEY`

### Endpoints

#### 1. Create Export

Starts an asynchronous export job. Returns a `job_id` that you use to poll for status.

**URL:** `https://server.getsphere.com/tax_api/exports`

**Method:** POST

**Request Payload**

| Field              | Type             | Required | Description                                                                         |
| ------------------ | ---------------- | -------- | ----------------------------------------------------------------------------------- |
| `date_range_start` | string           | Yes      | Start date in `YYYY-MM-DD` format                                                   |
| `date_range_end`   | string           | Yes      | End date in `YYYY-MM-DD` format                                                     |
| `country_in`       | array of strings | No       | Filter by country codes (e.g., `["US", "CA"]`). Default: all countries              |
| `status_in`        | array of strings | No       | Filter by transaction status (e.g., `["finalized", "paid"]`). Default: all statuses |
| `sources_in`       | array of strings | No       | Filter by data source (e.g., `["stripe", "netsuite"]`). Default: all sources        |
| `tax_authority_in` | array of strings | No       | Filter by tax authority keys (e.g., `["sc_us"]`). Default: all regions              |
| `include_zero`     | boolean          | No       | Include zero-amount transactions. Default: `true`                                   |
| `processing`       | string           | No       | One of: `"all"`, `"processed"`, `"unprocessed"`. Default: `"all"`                   |

**Request Example**

```bash
curl -X POST https://server.getsphere.com/tax_api/exports \
  -H "X-API-KEY: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "date_range_start": "2025-01-01",
    "date_range_end": "2025-03-31",
    "status_in": ["finalized", "paid"],
    "country_in": ["US"]
  }'
```

**Response (201 Created)**

```json
{
  "job_id": 12345,
  "status": "pending"
}
```

#### 2. Get Export Status

Poll this endpoint to check whether your export is ready for download.

**URL:** `https://server.getsphere.com/tax_api/exports/{job_id}`

**Method:** GET

**Request Example**

```bash
curl https://server.getsphere.com/tax_api/exports/12345 \
  -H "X-API-KEY: YOUR_API_KEY"
```

**Response — Pending or Running**

```json
{
  "job_id": 12345,
  "status": "pending",
  "created_at": "2025-03-20T10:30:00+00:00"
}
```

The `status` field will be one of: `"pending"`, `"running"`, `"completed"`, or `"failed"`.

**Response — Completed**

When the export is ready, the response includes a `download_url` — a presigned S3 URL that expires after 24 hours.

```json
{
  "job_id": 12345,
  "status": "completed",
  "created_at": "2025-03-20T10:30:00+00:00",
  "completed_at": "2025-03-20T10:30:45+00:00",
  "download_url": "https://sphere-file-storage.s3.amazonaws.com/..."
}
```

Download the file by making a GET request to the `download_url`. No authentication header is needed for the download — the URL is self-authenticating.

**Response — Failed**

```json
{
  "job_id": 12345,
  "status": "failed",
  "created_at": "2025-03-20T10:30:00+00:00",
  "error_message": "Export failed due to an internal error."
}
```

#### 3. Cancel Export

Cancel a pending or in-progress export job.

**URL:** `https://server.getsphere.com/tax_api/exports/{job_id}/cancel`

**Method:** POST

**Request Example**

```bash
curl -X POST https://server.getsphere.com/tax_api/exports/12345/cancel \
  -H "X-API-KEY: YOUR_API_KEY"
```

**Response (200 OK)**

```json
{
  "job_id": 12345,
  "status": "cancelled"
}
```

Note: Only jobs with status `"pending"` or `"running"` can be cancelled. Attempting to cancel a completed or failed job returns a 400 error.

### Recommended Polling Strategy

After creating an export, poll the status endpoint at a reasonable interval:

```
1. POST /tax_api/exports → get job_id
2. Wait 2-5 seconds
3. GET /tax_api/exports/{job_id}
4. If status is "pending" or "running", go to step 2
5. If status is "completed", download the file from download_url
6. If status is "failed", handle the error
```

Most exports complete within a few seconds. Larger exports (many months of data across all regions) may take longer.

### Rate Limits

You can have a maximum of **3 concurrent exports** per organization. Jobs with status `"pending"` or `"running"` count toward this limit. Completed, failed, and cancelled jobs do not.

If you exceed this limit, the API returns a 429 error:

```json
{
  "error": "Too many concurrent exports. Maximum 3 allowed."
}
```

### CSV Columns

The exported CSV includes the following columns:

| Column                       | Description                                           |
| ---------------------------- | ----------------------------------------------------- |
| Invoice Id                   | Transaction identifier                                |
| Customer Id                  | Customer identifier from your billing system          |
| Customer Name                | Customer display name                                 |
| Type                         | Transaction type (e.g., invoice, credit\_note)        |
| Line Item Id                 | Line item identifier                                  |
| Status                       | Transaction status (finalized, paid, cancelled, etc.) |
| Transaction Source           | Data source (e.g., stripe, netsuite)                  |
| Region                       | Tax jurisdiction name                                 |
| Finalized On                 | Date the transaction was finalized                    |
| Paid On                      | Date the transaction was paid                         |
| Cancelled On                 | Date the transaction was cancelled                    |
| Event Date                   | Tax reporting date                                    |
| Event Type                   | Tax reporting type (sale, refund)                     |
| Address                      | Customer address used for tax calculation             |
| Description                  | Line item description                                 |
| Product Name                 | Product display name                                  |
| Product Tax Code             | Tax code assigned to the product                      |
| Currency                     | Transaction currency code                             |
| Subtotal                     | Line item subtotal (in transaction currency)          |
| Discount                     | Discount applied                                      |
| Total                        | Line item total                                       |
| Taxable Amount               | Amount subject to tax                                 |
| Total Tax Collected          | Tax collected on the transaction                      |
| Sphere Calculated Tax        | Tax amount calculated by Sphere                       |
| Filing Currency              | Currency used for tax filing                          |
| Filing Exchange Rate         | Exchange rate to filing currency                      |
| Filing Total                 | Total in filing currency                              |
| Filing Taxable Amount        | Taxable amount in filing currency                     |
| Filing Total Tax Collected   | Tax collected in filing currency                      |
| Filing Sphere Calculated Tax | Sphere-calculated tax in filing currency              |
| Filing Status                | Filing status for this transaction                    |

The CSV also includes **tax breakout columns** for each jurisdiction level (Country, State, County, City, District, Transit Authority), each with Name, Tax Rate, and Tax Amount sub-columns.

### Error Codes and Messages

#### 1. Unauthorized Access

```json
{
  "type": "api_error",
  "code": "unauthorized_access",
  "message": "Invalid API key."
}
```

**Description:** The API key provided in the request header is either missing or invalid.

**Resolution:** Verify that the API key is correctly included in the request headers. Ensure the key is correct, active, and authorized for this endpoint.

#### 2. Missing Date Range

```json
{
  "error": "date_range_start and date_range_end are required."
}
```

**Description:** One or both of the required date range fields are missing from the request body.

**Resolution:** Include both `date_range_start` and `date_range_end` in your request payload in `YYYY-MM-DD` format.

#### 3. Invalid Date Format

```json
{
  "error": "Invalid date format. Use YYYY-MM-DD."
}
```

**Description:** A date field could not be parsed.

**Resolution:** Ensure dates are in `YYYY-MM-DD` format (e.g., `2025-01-31`).

#### 4. Too Many Concurrent Exports

```json
{
  "error": "Too many concurrent exports. Maximum 3 allowed."
}
```

**Description:** Your organization already has 3 exports in progress.

**Resolution:** Wait for an existing export to complete or cancel one, then retry.

#### 5. Export Not Found

```json
{
  "error": "Export job not found."
}
```

**Description:** The `job_id` does not exist or belongs to a different organization.

**Resolution:** Verify the `job_id` returned from the create endpoint.
