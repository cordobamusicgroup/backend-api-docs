---
layout: default
title: CMG Backend API Documentation
---

# 📚 CMG Backend API Documentation

Welcome to the **Córdoba Music Group** API documentation. This comprehensive guide will help you integrate with our royalty management platform.

## 🚀 Quick Start

- **New here?** Start with the [Getting Started Guide](./guides/getting-started.md)
- **Need authentication help?** Check [Authentication Guide](./guides/authentication.md)
- **Ready to code?** Explore [API Endpoints](./api/)
- **Want examples?** See [Complete Examples](./guides/examples.md)

## 📋 Documentation Overview

### [Getting Started](./guides/getting-started.md)

Learn the basics: base URLs, environment configuration, and initial setup.

### [Authentication](./guides/authentication.md)

Deep dive into JWT authentication, token management, and security best practices.

### [API Endpoints](./api/)

Complete reference for all available API endpoints:

- [Financial Reports API](./api/financial-reports.md)
- [Music Distribution API](./api/music-distribution.md)
- [User Management API](./api/user-management.md)

### [Complete Examples](./guides/examples.md)

Real-world workflow examples from login to downloading reports.

### [Best Practices](./guides/best-practices.md)

Guidelines for token management, error handling, and rate limiting.

### [Data Reference](./guides/data-reference.md)

Lookup tables for distributors, payment statuses, and format specifications.

---

## 🔑 Key Information

| Item               | Details                                                             |
| ------------------ | ------------------------------------------------------------------- |
| **Base URL**       | See [Getting Started](./guides/getting-started.md)                  |
| **Authentication** | JWT Bearer Token (see [Authentication](./guides/authentication.md)) |
| **Token Expiry**   | 15 minutes                                                          |
| **API Version**    | 1.0.0                                                               |
| **Last Updated**   | 2025-12-15                                                          |

---

## 📞 Support & Resources

### Getting Help

- Contact your account manager for API credentials
- Report issues through your support channel
- Check the documentation for updates

### Security Guidelines

- Always use **HTTPS** in production
- Never share credentials or tokens
- Keep passwords secure
- Tokens are personal - don't reuse them

---

## 📖 Quick Reference Table

| Endpoint                                               | Method | Auth   | Description                |
| ------------------------------------------------------ | ------ | ------ | -------------------------- |
| `/auth/login`                                          | POST   | No     | Login and get access token |
| `/auth/refresh`                                        | POST   | Cookie | Refresh expired token      |
| `/auth/me`                                             | GET    | Yes    | Get current user info      |
| `/auth/logout`                                         | POST   | Cookie | Logout                     |
| `/financial/reports/user-reports/current`              | GET    | Yes    | List reports               |
| `/financial/reports/user-reports/download/options/:id` | GET    | Yes    | Get download URLs          |
| `/external/wordpress/submit-release`                   | POST   | Yes    | Submit release for QC      |

See [Quick Reference](./guides/quick-reference.md) for the complete list.

---

**Last Updated:** December 15, 2025  
**API Version:** 1.0.0  
**Support:** Contact your CMG administrator
