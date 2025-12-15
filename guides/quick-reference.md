---
layout: default
title: Quick Reference
---

# Quick Reference

Fast lookup for common API tasks and endpoints.

## 🚀 Quick Start Commands

### Login
```bash
curl -X POST https://api.example.com/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"user","password":"pass"}'
```

### Get User Info
```bash
curl -X GET https://api.example.com/auth/me \
  -H "Authorization: Bearer <token>"
```

### Get Reports
```bash
curl -X GET "https://api.example.com/financial/reports/user-reports/current?distributor=KONTOR" \
  -H "Authorization: Bearer <token>"
```

### Get Download URLs
```bash
curl -X GET "https://api.example.com/financial/reports/user-reports/download/options/7289" \
  -H "Authorization: Bearer <token>"
```

### Download File
```bash
curl -O "https://s3.amazonaws.com/bucket/reports/7289_full.csv?..."
```

---

## 📋 All Endpoints

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/auth/login` | POST | No | Authenticate user |
| `/auth/refresh` | POST | Cookie | Refresh token |
| `/auth/me` | GET | Yes | Get current user |
| `/auth/logout` | POST | Yes | Logout |
| `/financial/reports/user-reports/current` | GET | Yes | List reports |
| `/financial/reports/user-reports/download/options/:id` | GET | Yes | Get download URLs |
| `/external/wordpress/submit-release` | POST | Yes | Submit release for QC |

---

## 🔑 Auth Token

### Getting a Token

```bash
curl -X POST https://api.example.com/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"john","password":"pass"}'
```

### Using the Token

```bash
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Token Details

- **Lifetime:** 15 minutes (900 seconds)
- **Type:** JWT Bearer Token
- **Refresh:** `/auth/refresh` endpoint or automatic via cookies

---

## 📊 Common Queries

### Get Reports for KONTOR
```bash
GET /financial/reports/user-reports/current?distributor=KONTOR
```

### Get Reports for BELIEVE
```bash
GET /financial/reports/user-reports/current?distributor=BELIEVE
```

### Download All Formats
```bash
# Get download options
GET /financial/reports/user-reports/download/options/7289

# Then download each URL:
# - fullCatalog
# - groupedByLabels
# - groupedByArtists
# - groupedByReleases
# - groupedByPlatform
# - groupedByCountries
```

---

## ⚠️ Status Codes

| Code | Meaning | Fix |
|------|---------|-----|
| 200 | Success | None needed |
| 400 | Bad request | Check parameters |
| 401 | Unauthorized | Login again |
| 403 | Forbidden | Check permissions |
| 404 | Not found | Verify ID |
| 429 | Rate limited | Wait before retry |
| 500 | Server error | Retry later |

---

## 🛠️ Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| 401 error | Token expired | Call `/auth/refresh` or login again |
| 400 error | Missing parameter | Add `distributor` query param |
| Download returns 404 | URL expired | Request new URL from API |
| 429 error | Too many requests | Wait 1 minute before retrying |
| File is null | Still generating | Wait a few minutes and try again |

---

## 📚 Data Reference

### Distributors
- `KONTOR` - Kontor New Media
- `BELIEVE` - Believe Digital

### Payment Status
- `PENDING` - In progress
- `PAID` - Complete
- `CANCELLED` - Rejected

### Month Format
Format: `YYYYMM` (e.g., `202508` = August 2025)

### Currencies
`EUR`, `USD`, `GBP`, `SEK`, `NOK`

---

## 🔐 Security Checklist

- ✅ Use HTTPS in production
- ✅ Store tokens securely
- ✅ Refresh tokens before expiry
- ✅ Don't log sensitive data
- ✅ Handle 401 errors with re-auth
- ✅ Implement retry logic for 5xx
- ✅ Never hardcode credentials

---

## 📞 Getting Help

**Contact:** Your CMG administrator

**Information to provide:**
- Error message and status code
- Endpoint you're accessing
- What you were trying to do
- Request/response details (without tokens)

---

## 🔗 Documentation Links

- [Getting Started](./getting-started.md)
- [Authentication](./authentication.md)
- [Best Practices](./best-practices.md)
- [Complete Examples](./examples.md)
- [Data Reference](./data-reference.md)
- [Financial Reports API](../api/financial-reports.md)
- [Music Distribution API](../api/music-distribution.md)

---

**Last Updated:** December 15, 2025
