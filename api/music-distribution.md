---
layout: default
title: Music Distribution API
---

# Music Distribution API

Complete reference for music distribution and quality control endpoints.

## 🎵 Overview

The Music Distribution API allows you to:

- Submit music releases for quality control (QC)
- Integrate with the WordPress legacy QC system
- Propose track and album metadata changes
- Track submission status through WordPress Gravity Forms

---

## Submit Release for Quality Control

Submit a music release to the WordPress legacy system for quality control review. This endpoint validates the release in DMB, fetches all release details, and submits them to WordPress Gravity Forms.

### Request

**Endpoint:** `POST /external/wordpress/submit-release`

**Headers:**

```
Authorization: Bearer <access_token>
Content-Type: application/json
```

**Body:**

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

### Request Fields

| Field                       | Type   | Required | Description                 | Example                  |
| --------------------------- | ------ | -------- | --------------------------- | ------------------------ |
| `productCode`               | string | Yes      | EAN/UPC code (13 digits)    | `1234567890123`          |
| `newAlbumTitle`             | string | No       | Proposed new album title    | `Greatest Hits (Deluxe)` |
| `newAlbumVersion`           | string | No       | Proposed version for album  | `Explicit`               |
| `trackChanges`              | array  | No       | List of track modifications | See below                |
| `trackChanges[].trackId`    | string | Yes      | DMB Track ID                | `98765`                  |
| `trackChanges[].newTitle`   | string | No       | Proposed track title        | `Hit Song`               |
| `trackChanges[].newVersion` | string | No       | Proposed track version      | `Radio Edit`             |

### Example Requests

**Basic submission (album only):**

```bash
curl -X POST https://api.example.com/external/wordpress/submit-release \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{
    "productCode": "1234567890123"
  }'
```

**With album changes:**

```bash
curl -X POST https://api.example.com/external/wordpress/submit-release \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{
    "productCode": "1234567890123",
    "newAlbumTitle": "Greatest Hits (Deluxe Edition)",
    "newAlbumVersion": "Explicit"
  }'
```

**With track changes:**

```bash
curl -X POST https://api.example.com/external/wordpress/submit-release \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{
    "productCode": "1234567890123",
    "trackChanges": [
      {
        "trackId": "98765",
        "newTitle": "Hit Song",
        "newVersion": "Radio Edit"
      },
      {
        "trackId": "98766",
        "newTitle": "Another Track"
      }
    ]
  }'
```

**Complete submission:**

```bash
curl -X POST https://api.example.com/external/wordpress/submit-release \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{
    "productCode": "1234567890123",
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
        "newTitle": "Another Track"
      }
    ]
  }'
```

### Response (200 OK)

```json
{
  "entryId": 42,
  "productCode": "1234567890123",
  "releaseTitle": "Greatest Hits",
  "submittedAt": "2025-11-11T15:30:00.000Z",
  "gravityFormsUrl": "https://wordpress.example.com/wp-admin/admin.php?page=gf_entries&view=entry&id=91&lid=42"
}
```

### Response Fields

| Field             | Type   | Description                      | Example                             |
| ----------------- | ------ | -------------------------------- | ----------------------------------- |
| `entryId`         | number | WordPress Gravity Forms entry ID | `42`                                |
| `productCode`     | string | EAN/UPC code submitted           | `1234567890123`                     |
| `releaseTitle`    | string | Title of the release             | `Greatest Hits`                     |
| `submittedAt`     | string | Submission timestamp (ISO 8601)  | `2025-11-11T15:30:00.000Z`          |
| `gravityFormsUrl` | string | Direct link to WordPress entry   | `https://wordpress.example.com/...` |

---

## Process Flow

The submission process works as follows:

### 1️⃣ Validation

- System validates the product code format (13 digits)
- Checks if release exists in DMB

### 2️⃣ Status Check

- Verifies release is in **"Ready for QC"** status
- Ensures no conflicting submissions exist

### 3️⃣ Data Fetching

- Retrieves complete release metadata from DMB
- Includes: tracks, artists, labels, formats, etc.
- Combines with user-provided metadata changes

### 4️⃣ Submission

- Submits all data to WordPress Gravity Forms (Form 91)
- Includes authenticated user information
- Stores submission metadata

### 5️⃣ Response

- Returns WordPress entry ID
- Provides link to view submission in WordPress
- Confirmation of successful processing

---

## Error Responses

### 400 Bad Request

**Cause:** Invalid product code format or missing required fields

```json
{
  "statusCode": 400,
  "message": "Product code must be 13 digits",
  "error": "Bad Request"
}
```

**Fix:** Verify product code is exactly 13 digits

### 401 Unauthorized

**Cause:** Missing or invalid authentication token

```json
{
  "statusCode": 401,
  "message": "Invalid token",
  "error": "Unauthorized"
}
```

**Fix:** Login to get a fresh token

### 403 Forbidden

**Cause:** User doesn't have permission (not USER or ADMIN role)

```json
{
  "statusCode": 403,
  "message": "You don't have permission to submit releases",
  "error": "Forbidden"
}
```

**Fix:** Contact administrator to grant permissions

### 404 Not Found

**Cause:** Release doesn't exist in DMB with given product code

```json
{
  "statusCode": 404,
  "message": "Release not found in DMB",
  "error": "Not Found"
}
```

**Fix:** Verify the product code is correct

### 422 Unprocessable Entity

**Cause:** Release exists but is not ready for QC

```json
{
  "statusCode": 422,
  "message": "Release status is 'Draft' - must be 'Ready for QC'",
  "error": "Unprocessable Entity"
}
```

**Fix:** Wait for release to be in "Ready for QC" status in DMB

### 500 Internal Server Error

**Cause:** Server error (temporary or WordPress integration issue)

```json
{
  "statusCode": 500,
  "message": "Failed to submit to WordPress",
  "error": "Internal Server Error"
}
```

**Fix:** Retry after a moment or contact support

---

## Important Notes

### Release Status Requirements

Only releases in these DMB statuses can be submitted:

- ✅ `Ready for QC` - Can submit

Releases in other statuses cannot be submitted:

- ❌ `Draft` - Still being prepared
- ❌ `Released` - Already released
- ❌ `Rejected` - Failed QC previously
- ❌ `Archived` - No longer active

### Automatic Data Handling

- ✅ User email is automatically included
- ✅ User ID is automatically included
- ✅ Submission timestamp is automatically recorded
- ✅ User role is verified for permission

### Changes Management

- You can submit changes for some or all tracks
- Omitted track IDs won't be modified
- Album changes are optional
- Empty strings clear optional fields

---

## Typical Workflow

### Step 1: Verify Release Status

Check that your release in DMB is in "Ready for QC" status.

### Step 2: Prepare Changes

Decide which metadata needs correcting:

- Album title corrections
- Track title corrections
- Version information

### Step 3: Submit Release

```bash
curl -X POST https://api.example.com/external/wordpress/submit-release \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "productCode": "9876543210987",
    "newAlbumTitle": "Corrected Album Title",
    "trackChanges": [
      {
        "trackId": "1001",
        "newTitle": "Track Title Fix"
      }
    ]
  }'
```

### Step 4: Verify Submission

Check the response for the entry ID and gravityFormsUrl:

```json
{
  "entryId": 42,
  "gravityFormsUrl": "https://wordpress.example.com/wp-admin/admin.php?page=gf_entries&view=entry&id=91&lid=42"
}
```

### Step 5: Track Progress

Visit the gravityFormsUrl to see QC reviewer comments and status.

---

## Best Practices

✅ **Do:**

- Verify release status before submitting
- Test with one track before submitting album-wide changes
- Keep product codes secure (they're sensitive)
- Use the gravityFormsUrl to monitor QC progress

❌ **Don't:**

- Submit releases multiple times for the same corrections
- Submit incomplete or test product codes
- Change multiple unrelated things at once
- Rely on automation without verification

---

## Troubleshooting

### "Release not found in DMB"

- Verify product code is correct (13 digits)
- Check that release exists in DMB

### "Release not ready for QC"

- Check release status in DMB
- Ensure it's in "Ready for QC" status

### "Failed to submit to WordPress"

- Check your internet connection
- Verify WordPress service is available
- Try again in a few moments

### Submission appears to succeed but no WordPress entry

- Check gravityFormsUrl from response
- May take a few seconds to appear in WordPress
- Contact support if issue persists

---

**Need help?** Contact your CMG administrator for support.
