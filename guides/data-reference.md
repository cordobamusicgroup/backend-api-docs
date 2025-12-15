---
layout: default
title: Data Reference
---

# Data Reference

Quick reference for common data values and formats used in the API.

## 🏢 Distributors

Supported music distributors for financial reports:

| Code | Name | Region | Description |
|------|------|--------|-------------|
| `KONTOR` | Kontor New Media | Europe | European distributor focusing on digital distribution |
| `BELIEVE` | Believe Digital | Global | Global digital music distribution platform |

## 💳 Payment Status (debitState)

Payment statuses for financial reports:

| Status | Meaning | Next Action |
|--------|---------|------------|
| `PENDING` | Payment is being processed or scheduled | Wait for status update |
| `PAID` | Payment has been completed | Download and verify report |
| `CANCELLED` | Payment was cancelled or rejected | Contact support if unexpected |

## 📅 Reporting Month Format

Reports are organized by month using the format: **YYYYMM**

### Format Breakdown

- **YYYY** = 4-digit year
- **MM** = 2-digit month (01-12)

### Examples

| Format | Date | Description |
|--------|------|-------------|
| `202501` | January 2025 | First month of 2025 |
| `202502` | February 2025 | Second month of 2025 |
| `202508` | August 2025 | Eighth month of 2025 |
| `202512` | December 2025 | Twelfth month (last) of 2025 |
| `202407` | July 2024 | Historical report from previous year |

## 💰 Currencies

Common currency codes used in reports:

| Code | Currency | Region |
|------|----------|--------|
| `EUR` | Euro | Europe |
| `USD` | US Dollar | Americas |
| `GBP` | British Pound | UK |
| `SEK` | Swedish Krona | Sweden |
| `NOK` | Norwegian Krone | Norway |

## 👤 User Roles

Available user roles in the system:

| Role | Permissions | Description |
|------|-------------|-------------|
| `USER` | Read own reports, submit releases | Standard user account |
| `ADMIN` | All permissions, manage accounts | Administrator account |

## 📁 Download Format Groupings

When downloading a report, choose from these grouping options:

| Format | Field Name | Content | Best For |
|--------|-----------|---------|----------|
| Full Catalog | `fullCatalog` | All data with complete details | Complete analysis, archiving |
| Grouped by Labels | `groupedByLabels` | Data aggregated by record label | Label performance analysis |
| Grouped by Artists | `groupedByArtists` | Data aggregated by artist | Artist earnings tracking |
| Grouped by Releases | `groupedByReleases` | Data aggregated by album/single | Release performance |
| Grouped by Platform | `groupedByPlatform` | Data aggregated by streaming platform | Platform comparison (Spotify vs Apple Music etc.) |
| Grouped by Countries | `groupedByCountries` | Data aggregated by country | Geographic analysis |

## 🔄 API Status Codes

Standard HTTP status codes used by the API:

### Success Codes (2xx)

| Code | Status | Meaning |
|------|--------|---------|
| `200` | OK | Request successful |
| `201` | Created | Resource created successfully |
| `204` | No Content | Request successful (no content) |

### Client Error Codes (4xx)

| Code | Status | Meaning | Action |
|------|--------|---------|--------|
| `400` | Bad Request | Invalid parameters or malformed request | Check request format |
| `401` | Unauthorized | Missing/invalid authentication token | Login again |
| `403` | Forbidden | User lacks permission | Contact administrator |
| `404` | Not Found | Resource doesn't exist | Verify IDs are correct |
| `429` | Too Many Requests | Rate limit exceeded | Wait and retry |

### Server Error Codes (5xx)

| Code | Status | Meaning | Action |
|------|--------|---------|--------|
| `500` | Internal Server Error | Server error (temporary) | Retry after delay |
| `502` | Bad Gateway | Service temporarily unavailable | Retry after delay |
| `503` | Service Unavailable | Server maintenance or overload | Retry later |

## 📝 Field Formats

Common field formats and their specifications:

### Email

- Format: Standard email format
- Example: `john.doe@example.com`
- Validation: Must be valid email address

### Date/Time (ISO 8601)

- Format: `YYYY-MM-DDTHH:mm:ss.sssZ`
- Example: `2025-11-08T21:45:40.775Z`
- Timezone: Always UTC (Z suffix)

### Product Code (EAN/UPC)

- Format: 13-digit numeric string
- Example: `1234567890123`
- Validation: Exactly 13 digits

### JWT Token

- Format: `header.payload.signature`
- Example: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOi...`
- Length: Typically 300-500 characters

### Monetary Values

- Format: Decimal number
- Example: `86.4017394675`
- Precision: Up to 10 decimal places
- Currency: Specified in separate `currency` field

## 🔐 Authentication Details

### JWT Token Specifications

| Aspect | Value | Notes |
|--------|-------|-------|
| Algorithm | HS256 | HMAC with SHA-256 |
| Expiration | 900 seconds | 15 minutes |
| Type | Bearer Token | Use in Authorization header |
| Header Format | `Authorization: Bearer <token>` | Exact format required |

### Refresh Token

| Aspect | Value | Notes |
|--------|-------|-------|
| Storage | HTTP-only cookies | Automatic handling |
| Expiration | 7 days | Typical value |
| Refresh Endpoint | `/auth/refresh` | POST request |
| Authentication | Cookie-based | No header required |

## 📊 Response Envelope Format

All API responses follow standard format:

### Success Response (200-299)

```json
{
  "data": { }
}
```

### Error Response (400+)

```json
{
  "statusCode": 400,
  "message": "Error description",
  "error": "Error type"
}
```

## 🎵 Music Distribution Fields

### Release Status (DMB)

| Status | Description | Can Submit |
|--------|-------------|-----------|
| `Draft` | Being prepared | ❌ No |
| `Ready for QC` | Ready for quality control | ✅ Yes |
| `QC In Progress` | Under review | ❌ No |
| `QC Approved` | Passed review | ❌ No |
| `Released` | Live on platforms | ❌ No |
| `Rejected` | Failed QC | ❌ No |
| `Archived` | No longer active | ❌ No |

### Track ID Format

- Format: Numeric string
- Example: `98765`
- Source: DMB system
- Uniqueness: Unique per release

## 🛠️ Version Information

Current API version and compatibility:

| Item | Version | Status |
|------|---------|--------|
| API Version | 1.0.0 | Stable |
| Documentation | 2025-12-15 | Current |
| Authentication | JWT | Standard |
| Primary Protocol | HTTPS REST | Required |

## 📞 Support Reference

When contacting support, reference:

- **API Version:** 1.0.0
- **Documentation Date:** 2025-12-15
- **Base URL:** Provided by administrator
- **Your User ID:** Available from `/auth/me` endpoint

---

## 🔗 Related Documentation

- [Getting Started](./getting-started.md) - Initial setup
- [Authentication](./authentication.md) - Token management
- [Best Practices](./best-practices.md) - Production guidelines
- [Complete Examples](./examples.md) - Code samples

---

**Need help?** Contact your CMG administrator for support.
