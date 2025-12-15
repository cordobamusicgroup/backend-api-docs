# CMG Backend API Documentation

> Comprehensive API documentation for the Córdoba Music Group royalty management platform

[![Documentation](https://img.shields.io/badge/docs-latest-blue)](https://cordobamusicgroup.github.io/backend-api-docs)
[![API Version](https://img.shields.io/badge/api-v1.0.0-green)](./guides/data-reference.md)
[![License](https://img.shields.io/badge/license-MIT-blue)](#license)

## 📦 Auto-Deploy to GitHub Pages

This documentation automatically updates whenever you push changes!

- **✅ Trigger:** Any `.md` file or `_config.yml` changes
- **🎯 Target branch:** `main` or `master`
- **⏱️ Deploy time:** ~1-2 minutes after push
- **📊 Status:** Check [Actions](https://github.com/cordobamusicgroup/backend-api-docs/actions) tab

→ [Learn about CI/CD & Deployment](./guides/deployment.md)

## 📚 Quick Links

- **📖 [View Live Documentation](https://cordobamusicgroup.github.io/backend-api-docs)** - GitHub Pages site
- **🚀 [Get Started](./guides/getting-started.md)** - Begin integration
- **🔑 [Authentication Guide](./guides/authentication.md)** - JWT token setup
- **💻 [Code Examples](./guides/examples.md)** - JavaScript, Python, cURL
- **📋 [Quick Reference](./guides/quick-reference.md)** - Common tasks
- **🔄 [Deployment Info](./guides/deployment.md)** - CI/CD pipeline

## 🎯 Overview

The CMG Backend API provides access to:

- 🔐 **User Authentication** - JWT-based token authentication
- 📊 **Financial Reports** - Royalty reports from multiple distributors
- 📥 **Report Downloads** - Multiple format options (CSV)
- 🎵 **Music Distribution** - Quality control submission system

---

## 🌐 GitHub Pages Hosting & Auto-Deploy

This documentation is hosted on **GitHub Pages** with **automatic CI/CD deployment**:

### How It Works

1. **Push Markdown changes** to `main` or `master` branch
2. **GitHub Actions** automatically detects the changes
3. **Jekyll builds** the static site
4. **GitHub Pages** deploys the updated documentation
5. **Site updates** in ~1-2 minutes

### Setup Required

Everything is already configured! No additional setup needed.

### File Structure

```
.github/workflows/deploy.yml    # CI/CD Pipeline (auto-configured)
_config.yml                      # Jekyll configuration
guides/                         # Documentation guides
api/                            # API reference docs
index.md                        # Home page
```

→ [Full Deployment Guide](./guides/deployment.md)

---

## 🚀 Getting Started

### 1. Read the Getting Started Guide

Learn about base URLs, authentication, and making your first request.

→ [Read Guide](./guides/getting-started.md)

### Login

Authenticate and receive an access token to use with subsequent requests.

**Endpoint:** `POST /auth/login`

**Request:**

```json
{
  "username": "your_username",
  "password": "your_password"
}
```

**Response:**

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 900
}
```

**Important Notes:**

- The access token expires after **15 minutes** (900 seconds)
- For API integrations, we recommend re-authenticating with each request
- A refresh token is automatically stored in cookies for web applications

**Using the Token:**

Include the access token in the `Authorization` header for all authenticated requests:

```
Authorization: Bearer <access_token>
```

---

### Refresh Token (Optional)

If your access token expires, you can get a new one without re-entering credentials.

**Endpoint:** `POST /auth/refresh`

The refresh token is automatically sent via cookies. No request body needed.

**Response:**

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 900
}
```

---

### Get Current User

Retrieve information about the authenticated user.

**Endpoint:** `GET /auth/me`

**Headers:**

```
Authorization: Bearer <access_token>
```

**Response:**

```json
{
  "id": 123,
  "username": "john.doe",
  "email": "john.doe@example.com",
  "role": "USER",
  "clientId": 456,
  "createdAt": "2025-01-15T10:30:00.000Z"
}
```

---

### Logout

End your session and revoke tokens.

**Endpoint:** `POST /auth/logout`

**Response:**

```json
{
  "message": "Logged out successfully"
}
```

---

## Financial Reports

### Get User Reports

Retrieve all financial reports for the authenticated user, filtered by distributor.

**Endpoint:** `GET /financial/reports/user-reports/current`

**Query Parameters:**

| Parameter     | Required | Description            | Valid Values        |
| ------------- | -------- | ---------------------- | ------------------- |
| `distributor` | Yes      | Music distributor name | `KONTOR`, `BELIEVE` |

**Headers:**

```
Authorization: Bearer <access_token>
```

**Example Request:**

```
GET /financial/reports/user-reports/current?distributor=KONTOR
```

**Response:**

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

**Response Fields:**

| Field            | Type   | Description                                                   |
| ---------------- | ------ | ------------------------------------------------------------- |
| `id`             | number | Unique report identifier (use this to download files)         |
| `createdAt`      | string | When the report was created (ISO 8601 format)                 |
| `updatedAt`      | string | Last update timestamp (ISO 8601 format)                       |
| `currency`       | string | Currency code (EUR, USD, etc.)                                |
| `distributor`    | string | Distributor name: KONTOR, BELIEVE, or DMB                     |
| `reportingMonth` | string | Report period in YYYYMM format (e.g., "202508" = August 2025) |
| `totalRoyalties` | number | Total royalty amount for this period                          |
| `debitState`     | string | Payment status: `PENDING`, `PAID`, or `CANCELLED`             |
| `paidOn`         | string | Payment date (null if not yet paid)                           |

---

### Get Download Options

Get download links for a specific report. Reports are available in multiple formats grouped by different criteria.

**Endpoint:** `GET /financial/reports/user-reports/download/options/:id`

**Path Parameters:**

| Parameter | Type   | Description                       |
| --------- | ------ | --------------------------------- |
| `id`      | number | Report ID (from the reports list) |

**Headers:**

```
Authorization: Bearer <access_token>
```

**Example Request:**

```
GET /financial/reports/user-reports/download/options/7289
```

**Response:**

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

**Download Options:**

| Field                | Description                                                             |
| -------------------- | ----------------------------------------------------------------------- |
| `groupedByLabels`    | Report data grouped by record labels                                    |
| `groupedByArtists`   | Report data grouped by artists                                          |
| `groupedByReleases`  | Report data grouped by music releases                                   |
| `groupedByPlatform`  | Report data grouped by streaming platforms (Spotify, Apple Music, etc.) |
| `groupedByCountries` | Report data grouped by countries                                        |
| `fullCatalog`        | Complete report with all detailed data                                  |

**Important Notes:**

- All URLs are **temporary presigned links** that expire after **1 hour**
- Download URLs can be `null` if the file is still being generated (try again in a few minutes)
- Each URL points directly to a CSV file - no authentication needed for download
- Simply open the URL in a browser or use it with any HTTP client to download

**Example Download:**

```bash
# Download the full catalog file
curl -O "https://s3.amazonaws.com/bucket/reports/7289_full.csv?AWSAccessKeyId=..."
```

---

## Music Distribution (WordPress Integration)

### Submit Release for Quality Control

Submit a music release to the WordPress legacy system for quality control review. This endpoint validates the release in DMB, fetches all release details, and submits them to WordPress Gravity Forms.

**Endpoint:** `POST /external/wordpress/submit-release`

**Request Headers:**

```
Authorization: Bearer <access_token>
Content-Type: application/json
```

**Request Body:**

```json
{
  "productCode": "1234567890123",
  "newAlbumTitle": "New Album Title (Optional)",
  "newAlbumVersion": "Explicit (Optional)",
  "trackChanges": [
    {
      "trackId": "12345",
      "newTitle": "New Track Title (Optional)",
      "newVersion": "Radio Edit (Optional)"
    }
  ]
}
```

**Request Fields:**

| Field                       | Type   | Required | Description                             |
| --------------------------- | ------ | -------- | --------------------------------------- |
| `productCode`               | string | Yes      | EAN/UPC code of the release (13 digits) |
| `newAlbumTitle`             | string | No       | Proposed new title for the album        |
| `newAlbumVersion`           | string | No       | Proposed new version for the album      |
| `trackChanges`              | array  | No       | List of track modifications             |
| `trackChanges[].trackId`    | string | Yes      | DMB Track ID                            |
| `trackChanges[].newTitle`   | string | No       | Proposed new title for the track        |
| `trackChanges[].newVersion` | string | No       | Proposed new version for the track      |

**Example Request:**

```bash
curl -X POST https://api.example.com/external/wordpress/submit-release \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{
    "productCode": "1234567890123",
    "newAlbumTitle": "Greatest Hits (Deluxe Edition)",
    "trackChanges": [
      {
        "trackId": "98765",
        "newTitle": "Hit Song",
        "newVersion": "Radio Edit"
      }
    ]
  }'
```

**Response (200 OK):**

```json
{
  "entryId": 42,
  "productCode": "1234567890123",
  "releaseTitle": "Greatest Hits",
  "submittedAt": "2025-11-11T15:30:00.000Z",
  "gravityFormsUrl": "https://wordpress.example.com/wp-admin/admin.php?page=gf_entries&view=entry&id=91&lid=42"
}
```

**Response Fields:**

| Field             | Type   | Description                                      |
| ----------------- | ------ | ------------------------------------------------ |
| `entryId`         | number | WordPress Gravity Forms entry ID                 |
| `productCode`     | string | EAN/UPC code submitted                           |
| `releaseTitle`    | string | Title of the release                             |
| `submittedAt`     | string | Submission timestamp (ISO 8601)                  |
| `gravityFormsUrl` | string | Direct link to view the entry in WordPress admin |

**Process Flow:**

1. **Validation:** System validates the release exists in DMB and is ready for QC
2. **Data Fetch:** Retrieves complete release details from DMB (tracks, artists, label, etc.)
3. **Submission:** Submits all data to WordPress Gravity Forms (Form ID 91)
4. **Response:** Returns the entry ID and confirmation details

**Important Notes:**

- Only releases in **"Ready for QC"** status in DMB can be submitted
- The system automatically fetches all release metadata from DMB
- User email and ID are automatically included from the authenticated session
- The endpoint is available for both USER and ADMIN roles

**Error Responses:**

| Status Code | Description                                                   |
| ----------- | ------------------------------------------------------------- |
| 400         | Bad Request - Invalid product code or missing required fields |
| 401         | Unauthorized - Invalid or missing access token                |
| 403         | Forbidden - User doesn't have permission                      |
| 404         | Not Found - Release not found in DMB                          |
| 422         | Unprocessable Entity - Release not ready for QC in DMB        |
| 500         | Internal Server Error - Failed to submit to WordPress         |

---

## Complete Usage Example

Here's a complete workflow from login to downloading a report:

### Step 1: Login

```bash
curl -X POST https://api.example.com/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "john.doe",
    "password": "securePassword123"
  }'
```

**Response:**

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 900
}
```

### Step 2: Get Reports List

```bash
curl -X GET "https://api.example.com/financial/reports/user-reports/current?distributor=KONTOR" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

**Response:**

```json
[
  {
    "id": 7289,
    "reportingMonth": "202508",
    "totalRoyalties": 86.40,
    "debitState": "PAID",
    ...
  }
]
```

### Step 3: Get Download URLs

```bash
curl -X GET "https://api.example.com/financial/reports/user-reports/download/options/7289" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

**Response:**

```json
{
  "fullCatalog": "https://s3.amazonaws.com/bucket/reports/7289_full.csv?...",
  "groupedByArtists": "https://s3.amazonaws.com/bucket/reports/7289_artists.csv?...",
  ...
}
```

### Step 4: Download the File

```bash
curl -O "https://s3.amazonaws.com/bucket/reports/7289_full.csv?..."
```

---

## Error Responses

All errors follow this format:

```json
{
  "statusCode": 400,
  "message": "Error description",
  "error": "Bad Request"
}
```

### Common HTTP Status Codes

| Code | Meaning               | What to Do                                        |
| ---- | --------------------- | ------------------------------------------------- |
| 200  | Success               | Request completed successfully                    |
| 400  | Bad Request           | Check your request parameters                     |
| 401  | Unauthorized          | Your token is invalid or expired - login again    |
| 403  | Forbidden             | You don't have permission to access this resource |
| 404  | Not Found             | The resource doesn't exist                        |
| 429  | Too Many Requests     | You're making too many requests - slow down       |
| 500  | Internal Server Error | Something went wrong on our end - try again later |

---

## Best Practices

### 1. Token Management

- **Access tokens expire after 15 minutes**
- For simple API clients: Re-login before each request or session
- For advanced clients: Implement refresh token flow
- Always handle 401 errors by re-authenticating

### 2. Error Handling

```javascript
// Example pseudocode
try {
  response = makeAPIRequest();
  if (response.status === 401) {
    // Token expired - login again
    newToken = login();
    // Retry original request
    response = makeAPIRequest();
  }
} catch (error) {
  // Handle network or other errors
}
```

### 3. Download URLs

- Download URLs expire after 1 hour
- Don't store these URLs permanently
- Request new URLs when needed
- If a URL returns null, the file is still being generated - wait and try again

### 4. Rate Limiting

- The API has rate limits to prevent abuse
- If you receive a 429 error, wait before retrying
- Implement exponential backoff for retries

---

## Data Reference

### Distributor Values

- `KONTOR` - Kontor New Media
- `BELIEVE` - Believe Digital

### Payment Status (debitState)

- `PENDING` - Payment not yet processed
- `PAID` - Payment completed
- `CANCELLED` - Payment was cancelled

### Reporting Month Format

Format: `YYYYMM`

- `202501` = January 2025
- `202508` = August 2025
- `202512` = December 2025

---

## Support

### Getting Help

- Contact your account manager for API credentials
- Report issues or bugs through your support channel
- Check this documentation for updates

### Security

- Always use **HTTPS** in production
- Never share your credentials or tokens
- Tokens are personal - don't use someone else's token
- Keep your password secure

---

## Quick Reference

| Endpoint                                               | Method | Auth Required | Description                                   |
| ------------------------------------------------------ | ------ | ------------- | --------------------------------------------- |
| `/auth/login`                                          | POST   | No            | Login and get access token                    |
| `/auth/refresh`                                        | POST   | Cookie        | Refresh expired token                         |
| `/auth/me`                                             | GET    | Yes           | Get current user info                         |
| `/auth/logout`                                         | POST   | Cookie        | Logout and revoke tokens                      |
| `/financial/reports/user-reports/current`              | GET    | Yes           | List all reports by distributor               |
| `/financial/reports/user-reports/download/options/:id` | GET    | Yes           | Get download URLs for a report                |
| `/external/wordpress/submit-release`                   | POST   | Yes           | Submit music release for quality control (QC) |

---

**API Version:** 1.0.0
**Last Updated:** 2025-11-11
**Support:** Contact your CMG administrator
