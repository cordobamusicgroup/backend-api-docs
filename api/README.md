---
layout: default
title: API Endpoints Overview
---

# API Endpoints Overview

Complete index of all available API endpoints organized by category.

## 🔐 Authentication Endpoints

All endpoints for user authentication and token management.

### [Authentication Guide](../guides/authentication.md)

| Endpoint | Method | Auth | Purpose |
|----------|--------|------|---------|
| `/auth/login` | POST | No | Obtain JWT access token |
| `/auth/refresh` | POST | Cookie | Refresh expired token |
| `/auth/me` | GET | Yes | Get authenticated user info |
| `/auth/logout` | POST | Yes | Logout and revoke tokens |

**Key Details:**
- Token expiration: 15 minutes
- Refresh token stored in HTTP-only cookies
- All endpoints except `/login` require authentication

---

## 💰 Financial Reports Endpoints

Access and download financial reports from multiple distributors.

### [Financial Reports API](./financial-reports.md)

| Endpoint | Method | Auth | Purpose |
|----------|--------|------|---------|
| `/financial/reports/user-reports/current` | GET | Yes | List user reports by distributor |
| `/financial/reports/user-reports/download/options/:id` | GET | Yes | Get download links for report |

**Query Parameters:**
- `distributor` (required): `KONTOR` or `BELIEVE`

**Features:**
- Multiple download formats
- Grouped by label, artist, release, platform, country
- Full catalog export
- Presigned S3 URLs (1-hour expiration)

---

## 🎵 Music Distribution Endpoints

Submit music releases for quality control.

### [Music Distribution API](./music-distribution.md)

| Endpoint | Method | Auth | Purpose |
|----------|--------|------|---------|
| `/external/wordpress/submit-release` | POST | Yes | Submit release for QC |

**Features:**
- DMB integration
- WordPress Gravity Forms submission
- Track-level metadata changes
- Album metadata updates

---

## 📊 Response Format

### Success Response

```json
{
  "data": {
    // Response content
  }
}
```

### Error Response

```json
{
  "statusCode": 400,
  "message": "Error description",
  "error": "Error type"
}
```

---

## 🔄 Rate Limiting

- **Limit:** 100 requests per minute per API key
- **Response:** 429 Too Many Requests
- **Retry-After:** Check header for wait time
- **Strategy:** Implement exponential backoff

---

## 🗂️ Documentation Structure

### Guides
- [Getting Started](../guides/getting-started.md) - Initial setup
- [Authentication](../guides/authentication.md) - Token management
- [Best Practices](../guides/best-practices.md) - Production guidelines
- [Complete Examples](../guides/examples.md) - Code samples
- [Data Reference](../guides/data-reference.md) - Values and formats
- [Quick Reference](../guides/quick-reference.md) - Fast lookup

### API Documentation
- [Financial Reports API](./financial-reports.md) - Report endpoints
- [Music Distribution API](./music-distribution.md) - QC submission

---

## 📚 Next Steps

1. Start with [Getting Started](../guides/getting-started.md)
2. Learn [Authentication](../guides/authentication.md)
3. Explore specific endpoint documentation
4. Review [Best Practices](../guides/best-practices.md)
5. Check [Complete Examples](../guides/examples.md) for your use case

---

**Need help?** Contact your CMG administrator for support.
