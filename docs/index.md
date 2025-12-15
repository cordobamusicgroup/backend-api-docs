---
title: CMG REST API Documentation
hero:
  badge: REST API
  title: Music Royalty Management API
  description: Complete REST API documentation for Córdoba Music Group's financial reporting and music distribution platform.
  actions:
    - label: API Reference
      url: api/index.md
      primary: true
    - label: REST Guidelines
      url: guides/best-practices.md
  metrics:
    - label: API Version
      value: v1.0
    - label: Format
      value: JSON
    - label: Protocol
      value: HTTPS
features:
  - title: RESTful API
    description: Industry-standard REST API with intuitive endpoints following best practices.
    link: guides/best-practices.md
    label: View guidelines
    icon: 🔄
  - title: JWT Authentication
    description: Secure authentication using JWT tokens with role-based access control.
    link: guides/authentication.md
    label: Learn more
    icon: 🔐
  - title: Financial Reports
    description: Endpoints to query balances, splits, and payment statements in JSON format.
    link: api/financial-reports.md
    label: View endpoints
    icon: 📊
  - title: Music Distribution
    description: API for managing catalogs, releases, and distribution metadata.
    link: api/music-distribution.md
    label: View API
    icon: 🎵
---

## Quick Start

=== "First Steps"
    1. Read the [Getting Started](guides/getting-started.md) guide to understand the API
    2. Review [Authentication](guides/authentication.md) to obtain your credentials
    3. Explore [Examples](guides/examples.md) to see real-world use cases

=== "Development"
    1. Check the [API Reference](api/index.md) for all available endpoints
    2. Use the [Quick Reference](guides/quick-reference.md) for common queries
    3. Follow [Best Practices](guides/best-practices.md) to optimize your integration

=== "Integration"
    1. Review [Data Reference](guides/data-reference.md) for response schemas
    2. Implement [Best Practices](guides/best-practices.md) for security
    3. Test your integration with the provided examples

## Technical Information

| Feature              | Details                                                           |
| -------------------- | ----------------------------------------------------------------- |
| **Base URL**         | Provided by your administrator                                    |
| **Authentication**   | JWT Bearer Token                                                  |
| **Token Expiration** | 15 minutes                                                        |
| **API Version**      | 1.0.0                                                             |
| **Content-Type**     | application/json                                                  |
| **Last Updated**     | 2025-12-15                                                        |

## Support

!!! info "API Access"
    Contact your CMG account manager to request API credentials and the base URL for your environment.

!!! warning "Security Best Practices"
    - Always use **HTTPS** for all API requests
    - Never share tokens or credentials
    - Tokens are personal and should not be reused across environments
    - Implement automatic token rotation in your applications
