---
layout: default
title: Financial Reports API
---

# Financial Reports API

Complete reference for the financial reporting endpoints.

## 📊 Overview

The Financial Reports API allows you to:

- Retrieve all financial reports for authenticated users
- Filter reports by distributor (KONTOR, BELIEVE, etc.)
- Download reports in multiple formats
- Track payment status and royalty information

---

## Get User Reports

Retrieve all financial reports for the authenticated user, filtered by distributor.

### Request

**Endpoint:** `GET /financial/reports/user-reports/current`

**Headers:**

```
Authorization: Bearer <access_token>
```

**Query Parameters:**

| Parameter     | Required | Type   | Description                              |
| ------------- | -------- | ------ | ---------------------------------------- |
| `distributor` | Yes      | string | Music distributor: `KONTOR` or `BELIEVE` |

### Example Request

```bash
curl -X GET "https://api.example.com/financial/reports/user-reports/current?distributor=KONTOR" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

### Response (200 OK)

```json
[
  {
    "id": 7289,
    "createdAt": "2025-10-30T14:34:32.382Z",
    "updatedAt": "2025-11-08T21:45:40.775Z",
    "currency": "EUR",
    "distributor": "KONTOR",
    "reportingMonth": "202508",
    "totalRoyalties": 86.4017394675,
    "debitState": "PAID",
    "paidOn": "2025-11-08T21:45:40.628Z"
  },
  {
    "id": 7288,
    "createdAt": "2025-09-28T09:15:22.123Z",
    "updatedAt": "2025-10-10T18:30:15.456Z",
    "currency": "EUR",
    "distributor": "KONTOR",
    "reportingMonth": "202507",
    "totalRoyalties": 125.7542316789,
    "debitState": "PAID",
    "paidOn": "2025-10-10T18:30:15.234Z"
  }
]
```

### Response Fields

| Field            | Type   | Description                                  | Example                        |
| ---------------- | ------ | -------------------------------------------- | ------------------------------ |
| `id`             | number | Unique report identifier (use for downloads) | `7289`                         |
| `createdAt`      | string | Report creation date (ISO 8601)              | `2025-10-30T14:34:32.382Z`     |
| `updatedAt`      | string | Last update timestamp (ISO 8601)             | `2025-11-08T21:45:40.775Z`     |
| `currency`       | string | Currency code                                | `EUR`, `USD`                   |
| `distributor`    | string | Distributor name                             | `KONTOR`, `BELIEVE`            |
| `reportingMonth` | string | Report period (YYYYMM format)                | `202508`                       |
| `totalRoyalties` | number | Total royalty amount for period              | `86.4017394675`                |
| `debitState`     | string | Payment status                               | `PENDING`, `PAID`, `CANCELLED` |
| `paidOn`         | string | Payment date or null                         | `2025-11-08T21:45:40.628Z`     |

---

## Get Download Options

Get download links for a specific report. Reports are available in multiple grouping formats.

### Request

**Endpoint:** `GET /financial/reports/user-reports/download/options/:id`

**Path Parameters:**

| Parameter | Type   | Description                     |
| --------- | ------ | ------------------------------- |
| `id`      | number | Report ID from the reports list |

**Headers:**

```
Authorization: Bearer <access_token>
```

### Example Request

```bash
curl -X GET "https://api.example.com/financial/reports/user-reports/download/options/7289" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

### Response (200 OK)

```json
{
  "groupedByLabels": "https://s3.amazonaws.com/bucket/reports/7289_labels.csv?AWSAccessKeyId=...",
  "groupedByArtists": "https://s3.amazonaws.com/bucket/reports/7289_artists.csv?AWSAccessKeyId=...",
  "groupedByReleases": "https://s3.amazonaws.com/bucket/reports/7289_releases.csv?AWSAccessKeyId=...",
  "groupedByPlatform": "https://s3.amazonaws.com/bucket/reports/7289_platforms.csv?AWSAccessKeyId=...",
  "groupedByCountries": "https://s3.amazonaws.com/bucket/reports/7289_countries.csv?AWSAccessKeyId=...",
  "fullCatalog": "https://s3.amazonaws.com/bucket/reports/7289_full.csv?AWSAccessKeyId=..."
}
```

### Download Format Options

| Field                | Description                         | Use Case                        |
| -------------------- | ----------------------------------- | ------------------------------- |
| `groupedByLabels`    | Data grouped by record labels       | Analyze performance by label    |
| `groupedByArtists`   | Data grouped by artists             | Track artist earnings           |
| `groupedByReleases`  | Data grouped by music releases      | Analyze release performance     |
| `groupedByPlatform`  | Data grouped by streaming platforms | Compare platform performance    |
| `groupedByCountries` | Data grouped by countries           | Analyze geographic distribution |
| `fullCatalog`        | Complete detailed data              | Complete analysis or archiving  |

### Important Notes

⏱️ **URL Expiration:** All download URLs expire after **1 hour**

📁 **File Format:** All files are CSV format, ready to open in Excel or import into tools

🔄 **File Generation:** URLs may be `null` if still generating - wait a few minutes and retry

🔓 **Direct Downloads:** URLs are presigned and don't require authentication to download

❌ **No Caching:** Don't store these URLs - request fresh ones each time

### Downloading Files

**Using cURL:**

```bash
curl -O "https://s3.amazonaws.com/bucket/reports/7289_full.csv?..."
```

**Using Browser:**
Simply open the URL in your browser

**Using Python:**

```python
import requests

response = requests.get(url)
with open('report.csv', 'wb') as f:
    f.write(response.content)
```

**Using JavaScript:**

```javascript
fetch(url)
  .then((response) => response.blob())
  .then((blob) => {
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = "report.csv";
    link.click();
  });
```

---

## Error Responses

### 400 Bad Request

**Cause:** Missing or invalid distributor parameter

```json
{
  "statusCode": 400,
  "message": "Distributor parameter is required",
  "error": "Bad Request"
}
```

### 401 Unauthorized

**Cause:** Missing or invalid authentication token

```json
{
  "statusCode": 401,
  "message": "Invalid token",
  "error": "Unauthorized"
}
```

**Solution:** Login again to get a fresh token

### 404 Not Found

**Cause:** Report ID doesn't exist or belongs to another user

```json
{
  "statusCode": 404,
  "message": "Report not found",
  "error": "Not Found"
}
```

### 429 Too Many Requests

**Cause:** Too many requests in short period

```json
{
  "statusCode": 429,
  "message": "Too many requests, try again later",
  "error": "Too Many Requests"
}
```

**Solution:** Wait before retrying (see `Retry-After` header)

### 500 Internal Server Error

**Cause:** Server error (temporary)

```json
{
  "statusCode": 500,
  "message": "Internal server error",
  "error": "Internal Server Error"
}
```

**Solution:** Wait a moment and retry

---

## Workflow Example

### Step 1: Get List of Reports

```bash
curl "https://api.example.com/financial/reports/user-reports/current?distributor=KONTOR" \
  -H "Authorization: Bearer $TOKEN"
```

Response includes report IDs like `7289`.

### Step 2: Get Download Options

```bash
curl "https://api.example.com/financial/reports/user-reports/download/options/7289" \
  -H "Authorization: Bearer $TOKEN"
```

Response includes download URLs.

### Step 3: Download Report

```bash
curl -O "https://s3.amazonaws.com/bucket/reports/7289_full.csv?..."
```

File is now saved locally.

---

## Best Practices

✅ **Do:**

- Request fresh download URLs each time (don't cache)
- Use appropriate grouping format for your analysis
- Handle 429 errors with exponential backoff
- Download files immediately after getting URLs

❌ **Don't:**

- Store download URLs permanently
- Assume URLs don't expire
- Request too many reports in quick succession
- Cache authentication tokens longer than 15 minutes

---

## Data Reference

### Distributor Values

| Value     | Name             | Region |
| --------- | ---------------- | ------ |
| `KONTOR`  | Kontor New Media | Europe |
| `BELIEVE` | Believe Digital  | Global |

### Payment Status (debitState)

| Status      | Meaning                           |
| ----------- | --------------------------------- |
| `PENDING`   | Payment processing or scheduled   |
| `PAID`      | Payment completed                 |
| `CANCELLED` | Payment was cancelled or rejected |

### Reporting Month Format

Format: `YYYYMM`

Examples:

- `202501` = January 2025
- `202508` = August 2025
- `202512` = December 2025

---

**Need help?** Contact your CMG administrator for support.
