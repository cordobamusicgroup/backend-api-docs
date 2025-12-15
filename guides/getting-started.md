---
layout: default
title: Getting Started
---

# Getting Started with CMG API

This guide will help you set up and make your first API request to the Córdoba Music Group platform.

## 📍 Base URL

All API endpoints are accessible at your environment's base URL:

### Environments

| Environment | Base URL | Purpose |
|-------------|----------|---------|
| **Development** | `http://localhost:3000` | Local development |
| **Staging** | `[Contact admin]` | Pre-production testing |
| **Production** | `[Contact admin]` | Live production system |

> **Note:** Contact your administrator for staging and production URLs and credentials.

---

## 🔑 API Credentials

To use this API, you need:

1. **Username** - Your user account username
2. **Password** - Your secure account password

> ⚠️ **Security:** Never share your credentials. Keep your password secure and never commit credentials to version control.

---

## 📝 First API Request

### Step 1: Authenticate

Make your first request to obtain an access token:

```bash
curl -X POST https://api.example.com/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "your_username",
    "password": "your_password"
  }'
```

**Response:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 900
}
```

### Step 2: Use Your Token

Include the token in all subsequent requests:

```bash
curl -X GET "https://api.example.com/auth/me" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

---

## 🛠️ API Clients

### Using cURL (Command Line)

Already shown above! cURL is available on most systems.

### Using JavaScript/Node.js

```javascript
const response = await fetch('https://api.example.com/auth/login', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    username: 'your_username',
    password: 'your_password',
  })
});

const data = await response.json();
const accessToken = data.access_token;
```

### Using Python

```python
import requests

response = requests.post('https://api.example.com/auth/login', json={
    'username': 'your_username',
    'password': 'your_password'
})

data = response.json()
access_token = data['access_token']
```

### Using Postman

1. Create a new request
2. Set method to `POST`
3. Enter URL: `https://api.example.com/auth/login`
4. Go to **Body** tab → select **raw** → select **JSON**
5. Enter your credentials:
   ```json
   {
     "username": "your_username",
     "password": "your_password"
   }
   ```
6. Click **Send**
7. Copy the `access_token` from the response
8. In future requests, add header: `Authorization: Bearer <your_token>`

---

## 🔒 Authentication Methods

### Method 1: Simple Authentication (Recommended for Development)

Authenticate before each request or session:

```bash
# Login
TOKEN=$(curl -s -X POST https://api.example.com/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"user","password":"pass"}' \
  | grep -o '"access_token":"[^"]*"' | cut -d'"' -f4)

# Use token
curl -X GET "https://api.example.com/auth/me" \
  -H "Authorization: Bearer $TOKEN"
```

### Method 2: Refresh Token Flow (Recommended for Production)

For applications that need to stay logged in:

1. Login and get `access_token`
2. A `refresh_token` is stored automatically in cookies
3. When `access_token` expires, call refresh endpoint
4. Get new `access_token` without re-entering credentials

See [Authentication Guide](./authentication.md) for details.

---

## ⏱️ Rate Limiting

The API implements rate limiting to prevent abuse:

- **Limit:** 100 requests per minute per API key
- **Status Code:** 429 (Too Many Requests) when exceeded
- **Retry After:** Check the `Retry-After` header

If you hit rate limits, wait before retrying.

---

## 📊 Response Format

All responses follow a standard JSON format:

### Success Response (200-299)

```json
{
  "data": {
    // Response content here
  }
}
```

### Error Response (400+)

```json
{
  "statusCode": 400,
  "message": "Error description",
  "error": "Bad Request"
}
```

---

## ✅ Common Status Codes

| Code | Meaning | Action |
|------|---------|--------|
| 200 | Success | Request completed |
| 400 | Bad Request | Check parameters |
| 401 | Unauthorized | Token invalid/expired - login again |
| 403 | Forbidden | No permission for this resource |
| 404 | Not Found | Resource doesn't exist |
| 429 | Too Many Requests | Wait before retrying |
| 500 | Server Error | Try again later |

---

## 📚 Next Steps

- Learn about [Authentication](./authentication.md) in detail
- Explore [API Endpoints](../api/) documentation
- Review [Best Practices](./best-practices.md) for production
- Check [Complete Examples](./examples.md) for workflows

---

**Need help?** Contact your CMG administrator for support.
