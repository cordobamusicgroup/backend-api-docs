---
layout: default
title: Best Practices
---

# API Best Practices

This guide provides recommendations for using the CMG API in production environments.

## 🔐 Security

### Token Management

#### ✅ Best Practices

- Store tokens securely in memory or secure HTTP-only cookies
- Refresh tokens before expiration (proactively)
- Handle 401 responses by refreshing and retrying
- Clear tokens on logout
- Use HTTPS in production (required)

#### ❌ Avoid

- Storing tokens in localStorage (XSS vulnerability)
- Logging or printing tokens
- Hardcoding tokens in source code
- Committing tokens to version control
- Sharing tokens between users
- Using expired tokens

#### Example: Secure Token Storage

```javascript
class SecureTokenStore {
  setToken(token, expiresIn) {
    // Store only in memory or secure HTTP-only cookie
    this.token = token;
    this.expiresAt = Date.now() + expiresIn * 1000;
    this.scheduleRefresh();
  }

  scheduleRefresh() {
    // Refresh 1 minute before expiry
    const timeUntilExpiry = this.expiresAt - Date.now();
    const refreshTime = timeUntilExpiry - 60000;

    if (refreshTime > 0) {
      setTimeout(() => this.refreshToken(), refreshTime);
    }
  }

  getToken() {
    if (Date.now() >= this.expiresAt) {
      return null; // Token expired
    }
    return this.token;
  }

  clear() {
    this.token = null;
    this.expiresAt = null;
  }
}
```

### Password Security

- Use strong, unique passwords (minimum 12 characters)
- Include uppercase, lowercase, numbers, and symbols
- Change passwords regularly
- Never share passwords
- Use password managers for secure storage
- Enable two-factor authentication if available

---

## 🔄 Error Handling & Retry Logic

### Retry Strategy

Implement exponential backoff for transient errors:

```javascript
async function makeRequestWithRetry(url, options, maxRetries = 3) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const response = await fetch(url, options);

      if (response.status === 401) {
        // Token expired - refresh and retry
        await refreshToken();
        options.headers.Authorization = `Bearer ${newToken}`;
        continue;
      }

      if (response.ok) {
        return await response.json();
      }

      if (response.status === 429) {
        // Rate limited - exponential backoff
        const waitTime = Math.pow(2, attempt) * 1000;
        await new Promise((resolve) => setTimeout(resolve, waitTime));
        continue;
      }

      if (response.status >= 500) {
        // Server error - retry with exponential backoff
        const waitTime = Math.pow(2, attempt) * 1000;
        await new Promise((resolve) => setTimeout(resolve, waitTime));
        continue;
      }

      // Client error - don't retry
      const error = await response.json();
      throw new Error(error.message);
    } catch (error) {
      if (attempt === maxRetries - 1) throw error;

      const waitTime = Math.pow(2, attempt) * 1000;
      await new Promise((resolve) => setTimeout(resolve, waitTime));
    }
  }
}
```

### Error Response Handling

```javascript
async function handleAPIError(response, context) {
  const data = await response.json();

  switch (response.status) {
    case 400:
      console.error(`Invalid request: ${data.message}`);
      // Show user-friendly error
      return { error: data.message, userMessage: "Please check your input" };

    case 401:
      // Automatically handled by retry logic
      return { error: "Authentication expired", retry: true };

    case 403:
      console.error(`Access denied: ${data.message}`);
      return { error: "You do not have permission" };

    case 404:
      return { error: "Resource not found" };

    case 429:
      return { error: "Too many requests", retry: true };

    case 500:
      console.error(`Server error: ${data.message}`);
      return { error: "Server error, please try again later", retry: true };

    default:
      return { error: "Unknown error" };
  }
}
```

---

## ⚡ Performance Optimization

### Rate Limiting Awareness

- **Limit:** 100 requests per minute
- **Strategy:** Batch requests when possible
- **Monitoring:** Track request count per minute
- **Response:** Implement backoff when approaching limit

```javascript
class RateLimitManager {
  constructor(maxRequests = 100, windowMs = 60000) {
    this.maxRequests = maxRequests;
    this.windowMs = windowMs;
    this.requests = [];
  }

  async waitIfNeeded() {
    const now = Date.now();

    // Remove old requests outside window
    this.requests = this.requests.filter((time) => now - time < this.windowMs);

    if (this.requests.length >= this.maxRequests) {
      const oldestRequest = this.requests[0];
      const waitTime = this.windowMs - (now - oldestRequest);

      if (waitTime > 0) {
        console.warn(`Rate limit approaching. Waiting ${waitTime}ms`);
        await new Promise((resolve) => setTimeout(resolve, waitTime));
      }
    }

    this.requests.push(Date.now());
  }

  getRemainingRequests() {
    const now = Date.now();
    const recentRequests = this.requests.filter((time) => now - time < this.windowMs);
    return this.maxRequests - recentRequests.length;
  }
}
```

### Caching Strategies

- **Don't cache:** Download URLs (expire after 1 hour)
- **Don't cache:** Authentication tokens (manage via refresh)
- **Cache reasonable:** User metadata (cache 5-10 minutes)
- **Cache reasonable:** Report lists (cache 10-15 minutes)

```javascript
class APICache {
  constructor(ttlMs = 300000) {
    // 5 minutes default
    this.cache = new Map();
    this.ttlMs = ttlMs;
  }

  get(key) {
    const item = this.cache.get(key);
    if (!item) return null;

    if (Date.now() - item.timestamp > this.ttlMs) {
      this.cache.delete(key);
      return null;
    }

    return item.data;
  }

  set(key, data) {
    this.cache.set(key, {
      data,
      timestamp: Date.now(),
    });
  }

  clear() {
    this.cache.clear();
  }
}
```

---

## 📊 Monitoring & Logging

### What to Log

✅ **Do log:**

- API request/response status codes
- Error messages and stack traces
- Authentication events
- Unusual access patterns
- Rate limit events

❌ **Don't log:**

- Request/response bodies (may contain sensitive data)
- Authentication tokens
- Passwords or credentials
- Personal user information

```javascript
class APILogger {
  logRequest(method, endpoint, status) {
    const timestamp = new Date().toISOString();
    console.log(`[${timestamp}] ${method} ${endpoint} - ${status}`);
  }

  logError(endpoint, error) {
    const timestamp = new Date().toISOString();
    console.error(`[${timestamp}] ERROR at ${endpoint}: ${error.message}`);
  }

  logRateLimit(remaining) {
    console.warn(`Rate limit warning: ${remaining} requests remaining`);
  }

  logAuthEvent(event, username) {
    const timestamp = new Date().toISOString();
    console.info(`[${timestamp}] AUTH: ${event} - ${username}`);
  }
}
```

### Monitoring Alerts

Set up alerts for:

- High error rates (>5% of requests failing)
- Repeated 401 errors (token issues)
- Repeated 429 errors (rate limiting)
- Server errors (500+)
- Unusual access patterns

---

## 📥 Downloading Files

### Best Practices

```javascript
async function downloadReportSafely(url, filename) {
  try {
    // Validate URL before download
    if (!url.startsWith("https://")) {
      throw new Error("URL must be HTTPS");
    }

    // Fetch with timeout
    const controller = new AbortController();
    const timeout = setTimeout(() => controller.abort(), 30000); // 30s timeout

    const response = await fetch(url, {
      signal: controller.signal,
      credentials: "omit", // Presigned URLs don't need credentials
    });

    clearTimeout(timeout);

    // Validate response
    if (!response.ok) {
      throw new Error(`Download failed: ${response.status}`);
    }

    // Validate content type
    const contentType = response.headers.get("content-type");
    if (!contentType?.includes("text/csv")) {
      console.warn(`Unexpected content type: ${contentType}`);
    }

    // Validate file size (e.g., max 500MB)
    const contentLength = response.headers.get("content-length");
    if (contentLength > 500 * 1024 * 1024) {
      throw new Error("File too large");
    }

    // Stream to file
    const blob = await response.blob();
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = filename || "report.csv";
    link.click();
    URL.revokeObjectURL(link.href);
  } catch (error) {
    console.error(`Download failed: ${error.message}`);
    throw error;
  }
}
```

### Download URL Management

```javascript
class DownloadURLManager {
  constructor(expirationMs = 3600000) {
    // 1 hour
    this.urls = new Map();
    this.expirationMs = expirationMs;
  }

  addURL(key, url) {
    const expiresAt = Date.now() + this.expirationMs;
    this.urls.set(key, { url, expiresAt });
  }

  getURL(key) {
    const item = this.urls.get(key);

    if (!item) return null;
    if (Date.now() > item.expiresAt) {
      this.urls.delete(key);
      return null; // URL expired, request fresh one
    }

    return item.url;
  }

  isExpiring(key) {
    const item = this.urls.get(key);
    if (!item) return true;

    const timeRemaining = item.expiresAt - Date.now();
    return timeRemaining < 300000; // 5 minutes remaining
  }

  clear() {
    this.urls.clear();
  }
}
```

---

## 🧪 Testing

### Test Scenarios

```javascript
// Test error handling
async function testErrorHandling() {
  // Test 401 response
  // Test 429 response
  // Test 500 response
  // Test network timeout
  // Test invalid response format
}

// Test token refresh
async function testTokenRefresh() {
  // Get initial token
  // Wait for near-expiration
  // Verify token is refreshed
  // Verify new token works
}

// Test rate limiting
async function testRateLimiting() {
  // Make 100 requests quickly
  // Verify 429 response
  // Verify backoff works
  // Verify recovery after wait
}

// Test file downloads
async function testFileDownloads() {
  // Test successful download
  // Test URL expiration
  // Test large files
  // Test corrupted responses
}
```

---

## 🚀 Production Checklist

Before deploying to production:

- [ ] All tokens stored securely (no localStorage)
- [ ] Error handling implemented (retry logic)
- [ ] Rate limiting handled (exponential backoff)
- [ ] Logging configured (no sensitive data)
- [ ] HTTPS enabled (required)
- [ ] Credentials not in code (use environment variables)
- [ ] Token refresh implemented
- [ ] Download URLs managed properly (not cached permanently)
- [ ] File size limits checked
- [ ] Timeout handling added
- [ ] Monitoring and alerts set up
- [ ] Error messages user-friendly
- [ ] Security review completed
- [ ] Load testing done
- [ ] Documentation updated

---

## 📚 Next Steps

- Review [Complete Examples](./examples.md)
- Explore [API Endpoints](../api/)
- Check [Data Reference](./data-reference.md)

---

**Need help?** Contact your CMG administrator for support.
