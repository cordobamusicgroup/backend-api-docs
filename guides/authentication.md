---
layout: default
title: Authentication
---

# Authentication Guide

This guide explains how to authenticate with the CMG API using JWT (JSON Web Tokens).

## 🔐 Overview

The CMG API uses **JWT Bearer Token authentication**. All requests (except login) require a valid access token sent in the `Authorization` header.

### Key Points

- **Token Type:** JWT Bearer Token
- **Expiration:** 15 minutes (900 seconds)
- **Refresh:** Automatic via cookies or manual refresh endpoint
- **Scope:** All endpoints except `/auth/login`

---

## 📋 Login Endpoint

Authenticate and receive an access token.

### Request

**Endpoint:** `POST /auth/login`

**Headers:**

```
Content-Type: application/json
```

**Body:**

```json
{
  "username": "your_username",
  "password": "your_password"
}
```

### Response (200 OK)

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
  "expires_in": 900
}
```

### Response Fields

| Field          | Type   | Description                                  |
| -------------- | ------ | -------------------------------------------- |
| `access_token` | string | JWT token for authenticated requests         |
| `expires_in`   | number | Token lifetime in seconds (900 = 15 minutes) |

### Example Usage

**cURL:**

```bash
curl -X POST https://api.example.com/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "john.doe",
    "password": "securePassword123"
  }'
```

**JavaScript:**

```javascript
const response = await fetch("https://api.example.com/auth/login", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    username: "john.doe",
    password: "securePassword123",
  }),
});

const { access_token } = await response.json();
```

---

## 🎫 Using the Access Token

Include the token in the `Authorization` header for all authenticated requests:

```
Authorization: Bearer <access_token>
```

### Example

```bash
curl -X GET https://api.example.com/auth/me \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

---

## 🔄 Refresh Token Flow

When your access token expires, obtain a new one without re-entering credentials.

### Request

**Endpoint:** `POST /auth/refresh`

**Headers:**

```
Cookie: refresh_token=<your_refresh_token>
```

The refresh token is automatically stored in cookies by the server. No request body needed.

### Response (200 OK)

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 900
}
```

### When to Refresh

- **Automatic (for browsers):** Cookies handle this automatically
- **Manual (for API clients):** Check for 401 responses and refresh before retrying
- **Proactive (best practice):** Refresh when approaching expiration time

### Example Implementation

```javascript
class APIClient {
  constructor(baseURL) {
    this.baseURL = baseURL;
    this.accessToken = null;
    this.expiresAt = null;
  }

  async login(username, password) {
    const response = await fetch(`${this.baseURL}/auth/login`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ username, password }),
    });

    const data = await response.json();
    this.accessToken = data.access_token;
    this.expiresAt = Date.now() + data.expires_in * 1000;
  }

  async refresh() {
    const response = await fetch(`${this.baseURL}/auth/refresh`, {
      method: "POST",
      credentials: "include", // Send cookies
    });

    const data = await response.json();
    this.accessToken = data.access_token;
    this.expiresAt = Date.now() + data.expires_in * 1000;
  }

  async request(endpoint, options = {}) {
    // Refresh if token expired or about to expire
    if (Date.now() >= this.expiresAt - 60000) {
      // 1 minute before expiry
      await this.refresh();
    }

    const headers = {
      ...options.headers,
      Authorization: `Bearer ${this.accessToken}`,
    };

    const response = await fetch(`${this.baseURL}${endpoint}`, {
      ...options,
      headers,
      credentials: "include",
    });

    if (response.status === 401) {
      // Token invalid, refresh and retry
      await this.refresh();
      return this.request(endpoint, options);
    }

    return response.json();
  }
}
```

---

## 👤 Get Current User

Retrieve information about the authenticated user.

### Request

**Endpoint:** `GET /auth/me`

**Headers:**

```
Authorization: Bearer <access_token>
```

### Response (200 OK)

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

### Response Fields

| Field       | Type   | Description                      |
| ----------- | ------ | -------------------------------- |
| `id`        | number | Unique user identifier           |
| `username`  | string | Login username                   |
| `email`     | string | User email address               |
| `role`      | string | User role (`USER` or `ADMIN`)    |
| `clientId`  | number | Associated client/account ID     |
| `createdAt` | string | Account creation date (ISO 8601) |

---

## 🚪 Logout

End your session and revoke tokens.

### Request

**Endpoint:** `POST /auth/logout`

**Headers:**

```
Authorization: Bearer <access_token>
```

### Response (200 OK)

```json
{
  "message": "Logged out successfully"
}
```

### Important Notes

- Revokes the current access token
- Clears refresh token from cookies
- After logout, you must login again to get a new token
- Logging out is optional; tokens expire automatically

---

## ⚠️ Error Responses

### Invalid Credentials (401)

```json
{
  "statusCode": 401,
  "message": "Invalid username or password",
  "error": "Unauthorized"
}
```

### Token Expired (401)

```json
{
  "statusCode": 401,
  "message": "Token expired",
  "error": "Unauthorized"
}
```

**Action:** Call the refresh endpoint or login again.

### Token Invalid (401)

```json
{
  "statusCode": 401,
  "message": "Invalid token",
  "error": "Unauthorized"
}
```

**Action:** Login again.

### Missing Token (401)

```json
{
  "statusCode": 401,
  "message": "Authorization header missing",
  "error": "Unauthorized"
}
```

**Fix:** Include `Authorization: Bearer <token>` header.

### Forbidden (403)

```json
{
  "statusCode": 403,
  "message": "You don't have permission to access this resource",
  "error": "Forbidden"
}
```

**Note:** Some endpoints require `ADMIN` role.

---

## 🔒 Security Best Practices

### 1. Token Storage

- ✅ **Do:** Store tokens securely in memory or secure HTTP-only cookies
- ❌ **Don't:** Store tokens in localStorage (vulnerable to XSS attacks)
- ❌ **Don't:** Log or print tokens
- ❌ **Don't:** Commit tokens to version control

### 2. Token Handling

- ✅ **Do:** Refresh tokens proactively before expiration
- ✅ **Do:** Use HTTPS in production (required)
- ✅ **Do:** Validate tokens haven't been tampered with
- ❌ **Don't:** Reuse tokens across different users
- ❌ **Don't:** Share tokens with others
- ❌ **Don't:** Use expired tokens

### 3. Password Management

- ✅ **Do:** Use strong, unique passwords
- ✅ **Do:** Store passwords securely
- ✅ **Do:** Change passwords regularly
- ❌ **Don't:** Share passwords
- ❌ **Don't:** Use simple passwords
- ❌ **Don't:** Hardcode credentials in code

### 4. API Integration

- ✅ **Do:** Implement exponential backoff for 401 errors
- ✅ **Do:** Log authentication events
- ✅ **Do:** Monitor for unusual access patterns
- ❌ **Don't:** Disable HTTPS verification
- ❌ **Don't:** Accept self-signed certificates in production
- ❌ **Don't:** Log sensitive request/response data

---

## 🛠️ Troubleshooting

### "Invalid token" Error

**Cause:** Token has been modified or is corrupted

**Solution:** Login again to get a fresh token

### "Token expired" Error

**Cause:** Token's 15-minute lifetime has passed

**Solution:** Call `/auth/refresh` or login again

### 401 Unauthorized on Every Request

**Cause:** Token not sent in header or wrong format

**Solution:** Ensure header is exactly: `Authorization: Bearer <token>`

### 403 Forbidden

**Cause:** Your account lacks required permissions

**Solution:** Contact your administrator

### Connection Refused

**Cause:** Wrong base URL or server is down

**Solution:** Check environment URL and server status

---

## 📚 Next Steps

- Learn about [Best Practices](./best-practices.md)
- Explore [API Endpoints](../api/)
- Review [Complete Examples](./examples.md)

---

**Need help?** Contact your CMG administrator for support.
