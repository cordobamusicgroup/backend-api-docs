---
layout: default
title: Complete Examples
---

# Complete Usage Examples

Real-world workflow examples from authentication to downloading reports.

## 📊 Complete Workflow: From Login to Download

This section demonstrates a complete end-to-end workflow.

### Step 1: Authenticate

First, obtain an access token:

```bash
#!/bin/bash

# Set variables
API_URL="https://api.example.com"
USERNAME="john.doe"
PASSWORD="securePassword123"

# Authenticate
RESPONSE=$(curl -s -X POST "$API_URL/auth/login" \
  -H "Content-Type: application/json" \
  -d "{
    \"username\": \"$USERNAME\",
    \"password\": \"$PASSWORD\"
  }")

# Extract token
TOKEN=$(echo $RESPONSE | grep -o '"access_token":"[^"]*"' | cut -d'"' -f4)

echo "Token: $TOKEN"
```

### Step 2: Verify User Info

Get current user details:

```bash
curl -X GET "$API_URL/auth/me" \
  -H "Authorization: Bearer $TOKEN"
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

### Step 3: Get Reports List

Retrieve all reports from a specific distributor:

```bash
curl -X GET "$API_URL/financial/reports/user-reports/current?distributor=KONTOR" \
  -H "Authorization: Bearer $TOKEN"
```

**Response:**

```json
[
  {
    "id": 7289,
    "reportingMonth": "202508",
    "totalRoyalties": 86.4,
    "debitState": "PAID",
    "currency": "EUR",
    "paidOn": "2025-11-08T21:45:40.628Z"
  },
  {
    "id": 7288,
    "reportingMonth": "202507",
    "totalRoyalties": 125.75,
    "debitState": "PAID",
    "currency": "EUR",
    "paidOn": "2025-10-10T18:30:15.234Z"
  }
]
```

### Step 4: Get Download Options

Get various format options for a specific report:

```bash
REPORT_ID="7289"

curl -X GET "$API_URL/financial/reports/user-reports/download/options/$REPORT_ID" \
  -H "Authorization: Bearer $TOKEN"
```

**Response:**

```json
{
  "groupedByLabels": "https://s3.amazonaws.com/bucket/reports/7289_labels.csv?...",
  "groupedByArtists": "https://s3.amazonaws.com/bucket/reports/7289_artists.csv?...",
  "groupedByReleases": "https://s3.amazonaws.com/bucket/reports/7289_releases.csv?...",
  "groupedByPlatform": "https://s3.amazonaws.com/bucket/reports/7289_platforms.csv?...",
  "groupedByCountries": "https://s3.amazonaws.com/bucket/reports/7289_countries.csv?...",
  "fullCatalog": "https://s3.amazonaws.com/bucket/reports/7289_full.csv?..."
}
```

### Step 5: Download Report

Download the full catalog:

```bash
DOWNLOAD_URL=$(echo $RESPONSE | grep -o '"fullCatalog":"[^"]*"' | cut -d'"' -f4)

curl -O "$DOWNLOAD_URL"
```

The file is now saved as `7289_full.csv`.

---

## 💻 Complete JavaScript Example

```javascript
class CMGAPIClient {
  constructor(baseURL) {
    this.baseURL = baseURL;
    this.accessToken = null;
    this.tokenExpiresAt = null;
  }

  // Step 1: Login
  async login(username, password) {
    const response = await fetch(`${this.baseURL}/auth/login`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ username, password }),
      credentials: "include", // Include cookies for refresh token
    });

    if (!response.ok) {
      throw new Error("Login failed");
    }

    const data = await response.json();
    this.accessToken = data.access_token;
    this.tokenExpiresAt = Date.now() + data.expires_in * 1000;

    console.log("✅ Logged in successfully");
    return data;
  }

  // Refresh token if needed
  async ensureValidToken() {
    const timeUntilExpiry = this.tokenExpiresAt - Date.now();

    if (timeUntilExpiry < 60000) {
      // Less than 1 minute
      console.log("🔄 Refreshing token...");

      const response = await fetch(`${this.baseURL}/auth/refresh`, {
        method: "POST",
        credentials: "include", // Send cookies
      });

      const data = await response.json();
      this.accessToken = data.access_token;
      this.tokenExpiresAt = Date.now() + data.expires_in * 1000;

      console.log("✅ Token refreshed");
    }
  }

  // Make authenticated request
  async request(endpoint, options = {}) {
    await this.ensureValidToken();

    const response = await fetch(`${this.baseURL}${endpoint}`, {
      ...options,
      headers: {
        ...options.headers,
        Authorization: `Bearer ${this.accessToken}`,
      },
      credentials: "include",
    });

    if (!response.ok) {
      const error = await response.json();
      throw new Error(error.message || `API error: ${response.status}`);
    }

    return response.json();
  }

  // Get current user
  async getCurrentUser() {
    const data = await this.request("/auth/me");
    console.log("👤 Current user:", data);
    return data;
  }

  // Get reports
  async getReports(distributor) {
    const data = await this.request(`/financial/reports/user-reports/current?distributor=${distributor}`);
    console.log("📊 Reports:", data);
    return data;
  }

  // Get download options
  async getDownloadOptions(reportId) {
    const data = await this.request(`/financial/reports/user-reports/download/options/${reportId}`);
    console.log("📥 Download options:", data);
    return data;
  }

  // Download file
  async downloadFile(url, filename) {
    const response = await fetch(url, { credentials: "omit" }); // No auth needed

    if (!response.ok) {
      throw new Error(`Download failed: ${response.status}`);
    }

    const blob = await response.blob();
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = filename || "report.csv";
    link.click();
    URL.revokeObjectURL(link.href);

    console.log("✅ Download complete");
  }

  // Logout
  async logout() {
    await this.request("/auth/logout", { method: "POST" });
    this.accessToken = null;
    console.log("✅ Logged out");
  }
}

// Usage
async function main() {
  const client = new CMGAPIClient("https://api.example.com");

  try {
    // Step 1: Login
    await client.login("john.doe", "password123");

    // Step 2: Get current user
    const user = await client.getCurrentUser();

    // Step 3: Get reports
    const reports = await client.getReports("KONTOR");

    // Step 4: Get download options for first report
    if (reports.length > 0) {
      const options = await client.getDownloadOptions(reports[0].id);

      // Step 5: Download full catalog
      if (options.fullCatalog) {
        await client.downloadFile(options.fullCatalog, `report-${reports[0].id}.csv`);
      }
    }

    // Step 6: Logout
    await client.logout();
  } catch (error) {
    console.error("❌ Error:", error.message);
  }
}

// Run
main();
```

---

## 🐍 Complete Python Example

```python
import requests
import time
import csv
from datetime import datetime, timedelta

class CMGAPIClient:
    def __init__(self, base_url):
        self.base_url = base_url
        self.access_token = None
        self.token_expires_at = None
        self.session = requests.Session()

    def login(self, username, password):
        """Step 1: Login and get access token"""
        response = self.session.post(
            f'{self.base_url}/auth/login',
            json={
                'username': username,
                'password': password
            }
        )
        response.raise_for_status()

        data = response.json()
        self.access_token = data['access_token']
        self.token_expires_at = datetime.now() + timedelta(
            seconds=data['expires_in']
        )

        print('✅ Logged in successfully')
        return data

    def ensure_valid_token(self):
        """Refresh token if needed"""
        time_remaining = (self.token_expires_at - datetime.now()).total_seconds()

        if time_remaining < 60:  # Less than 1 minute
            print('🔄 Refreshing token...')

            response = self.session.post(
                f'{self.base_url}/auth/refresh'
            )
            response.raise_for_status()

            data = response.json()
            self.access_token = data['access_token']
            self.token_expires_at = datetime.now() + timedelta(
                seconds=data['expires_in']
            )

            print('✅ Token refreshed')

    def request(self, method, endpoint, **kwargs):
        """Make authenticated request"""
        self.ensure_valid_token()

        headers = kwargs.pop('headers', {})
        headers['Authorization'] = f'Bearer {self.access_token}'

        response = self.session.request(
            method,
            f'{self.base_url}{endpoint}',
            headers=headers,
            **kwargs
        )
        response.raise_for_status()

        return response.json()

    def get_current_user(self):
        """Step 2: Get current user"""
        data = self.request('GET', '/auth/me')
        print(f'👤 Current user: {data["username"]}')
        return data

    def get_reports(self, distributor):
        """Step 3: Get reports"""
        data = self.request(
            'GET',
            f'/financial/reports/user-reports/current?distributor={distributor}'
        )
        print(f'📊 Found {len(data)} reports')
        return data

    def get_download_options(self, report_id):
        """Step 4: Get download options"""
        data = self.request(
            'GET',
            f'/financial/reports/user-reports/download/options/{report_id}'
        )
        print(f'📥 Download options retrieved')
        return data

    def download_file(self, url, filename):
        """Step 5: Download file"""
        response = requests.get(url, stream=True)
        response.raise_for_status()

        with open(filename, 'wb') as f:
            for chunk in response.iter_content(chunk_size=8192):
                f.write(chunk)

        print(f'✅ Downloaded: {filename}')

    def logout(self):
        """Step 6: Logout"""
        self.request('POST', '/auth/logout')
        self.access_token = None
        print('✅ Logged out')

# Usage
def main():
    client = CMGAPIClient('https://api.example.com')

    try:
        # Step 1: Login
        client.login('john.doe', 'password123')

        # Step 2: Get current user
        user = client.get_current_user()

        # Step 3: Get reports
        reports = client.get_reports('KONTOR')

        # Step 4: Download each report in multiple formats
        for report in reports:
            print(f'\n📄 Processing report {report["id"]} ({report["reportingMonth"]})')

            options = client.get_download_options(report['id'])

            # Step 5: Download full catalog
            if options.get('fullCatalog'):
                filename = f'report-{report["id"]}-full.csv'
                client.download_file(options['fullCatalog'], filename)

            # Also download grouped by artists
            if options.get('groupedByArtists'):
                filename = f'report-{report["id"]}-artists.csv'
                client.download_file(options['groupedByArtists'], filename)

        # Step 6: Logout
        client.logout()

    except requests.exceptions.RequestException as e:
        print(f'❌ Error: {e}')

if __name__ == '__main__':
    main()
```

---

## 🎵 Music Distribution Example

Submit a release for quality control:

```bash
#!/bin/bash

API_URL="https://api.example.com"
TOKEN="your_access_token"
PRODUCT_CODE="9876543210987"

# Submit release with track changes
curl -X POST "$API_URL/external/wordpress/submit-release" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "productCode": "'$PRODUCT_CODE'",
    "newAlbumTitle": "Greatest Hits (Deluxe Edition)",
    "newAlbumVersion": "Explicit",
    "trackChanges": [
      {
        "trackId": "98765",
        "newTitle": "Hit Song",
        "newVersion": "Radio Edit"
      },
      {
        "trackId": "98766",
        "newTitle": "Another Great Track"
      }
    ]
  }'
```

**Response:**

```json
{
  "entryId": 42,
  "productCode": "9876543210987",
  "releaseTitle": "Greatest Hits",
  "submittedAt": "2025-12-15T10:30:00.000Z",
  "gravityFormsUrl": "https://wordpress.example.com/wp-admin/admin.php?page=gf_entries&view=entry&id=91&lid=42"
}
```

Visit the `gravityFormsUrl` to track QC progress.

---

## ⚠️ Error Handling Example

```javascript
async function robustAPICall() {
  const maxRetries = 3;
  let lastError;

  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const response = await client.getReports("KONTOR");
      return response; // Success
    } catch (error) {
      lastError = error;

      if (error.status === 401) {
        // Token invalid - refresh and retry
        try {
          await client.login(username, password);
          continue;
        } catch (loginError) {
          throw new Error("Authentication failed");
        }
      }

      if (error.status === 429) {
        // Rate limited - exponential backoff
        const waitMs = Math.pow(2, attempt) * 1000;
        console.log(`Rate limited. Waiting ${waitMs}ms before retry...`);
        await new Promise((r) => setTimeout(r, waitMs));
        continue;
      }

      if (error.status >= 500) {
        // Server error - exponential backoff
        const waitMs = Math.pow(2, attempt) * 1000;
        console.log(`Server error. Retrying in ${waitMs}ms...`);
        await new Promise((r) => setTimeout(r, waitMs));
        continue;
      }

      // Client error - don't retry
      throw error;
    }
  }

  throw lastError;
}
```

---

## 📚 Next Steps

- Review [Best Practices](./best-practices.md)
- Check [Data Reference](./data-reference.md)
- Explore [API Endpoints](../api/)

---

**Need help?** Contact your CMG administrator for support.
