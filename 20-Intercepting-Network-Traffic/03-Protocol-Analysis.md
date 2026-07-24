# Module 20: Intercepting Network Traffic - Protocol Analysis

## Overview

Protocol analysis means understanding the data being transmitted. This helps identify API endpoints, data formats, and security issues.

## Analyzing HTTP/HTTPS

### Request Structure

Every HTTP request has:
- Method (GET, POST, PUT, DELETE)
- URL (endpoint)
- Headers (authentication, content type)
- Body (parameters, JSON data)

**Example request:**
```
POST /api/login HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
Content-Type: application/json

{"username": "admin", "password": "secret123"}
```

### What to Look For

| Element | What to Check |
|---------|---------------|
| Endpoints | API structure, hidden endpoints |
| Authentication | Token format, token location |
| Parameters | What data is sent |
| Responses | Error messages, data exposure |

## Identifying API Endpoints

From intercepted traffic, you can map the entire API:

```
/api/login
/api/users/{id}
/api/users/{id}/profile
/api/upload
/api/admin/users
```

## Analyzing Custom Protocols

Some apps use non-HTTP protocols. Use Wireshark for these.

**What to look for:**
- Plain text data
- Custom encryption
- Binary protocols

## Summary

Protocol analysis involves examining HTTP requests/responses to understand the API structure, authentication mechanisms, and data formats. Look for endpoints, tokens, parameters, and response data. Map the full API from intercepted traffic.