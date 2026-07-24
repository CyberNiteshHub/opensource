# Module 12: Insecure Authentication - Tools and Resources

## Overview

This lesson covers tools to test authentication vulnerabilities and resources for implementing secure authentication.

## Tools for Testing Authentication

### 1. Burp Suite

Burp Suite is essential for testing authentication.

**What it can test:**
- Password strength (by intercepting registration)
- Token handling (checking tokens in requests)
- Session management (token expiry, invalidation)
- MFA bypass (removing MFA parameters)
- Rate limiting (sending many requests)

**How to use:**
```
1. Set up Burp Suite proxy
2. Perform login actions
3. Check requests for password/token handling
4. Try to bypass authentication checks
5. Test rate limiting by replaying requests
```

### 2. JWT_Tool

A tool specifically for testing JWT (JSON Web Token) authentication.

**Installation:**
```
git clone https://github.com/ticarpi/jwt_tool
cd jwt_tool
python jwt_tool.py
```

**What it tests:**
- Algorithm confusion (alg:none attack)
- Weak signing keys
- Token expiry validation
- JWT injection

**Usage:**
```
python jwt_tool.py eyJhbGciOiJIUzI1NiIs...
python jwt_tool.py -t https://api.example.com -rc "jwt=token"
```

### 3. Hydra

Hydra is a password brute-forcing tool.

**Usage:**
```
hydra -l admin -P rockyou.txt api.example.com https-post-form "/login:username=^USER^&password=^PASS^:F=incorrect"
```

**What it tests:**
- Password strength
- Account lockout mechanisms
- Rate limiting

### 4. OWASP ZAP

ZAP (Zed Attack Proxy) includes authentication testing features.

**What it tests:**
- Authentication bypass
- Session management
- Token handling
- CSRF in auth flows

### 5. Android Studio

Android Studio can help test local authentication.

**What to check:**
- SharedPreferences for login state
- SQLite for stored credentials
- Token storage security
- Biometric implementation

## Tool Comparison

| Tool | Password Testing | Token Testing | Session Testing | MFA Testing | Ease |
|------|----------------|---------------|----------------|-------------|------|
| Burp Suite | Yes | Yes | Yes | Yes | Easy |
| JWT_Tool | No | Yes | Limited | No | Medium |
| Hydra | Yes | No | No | No | Medium |
| OWASP ZAP | Yes | Yes | Yes | Yes | Easy |
| Android Studio | No | Yes | Yes | No | Easy |

## Manual Testing Checklist

### Password Testing

```
[ ] Try registering with weak passwords (a, 123456, password)
[ ] Try common passwords against existing accounts
[ ] Check for rate limiting on login
[ ] Check for account lockout after failures
[ ] Verify password validation exists on server (not just client)
```

### MFA Testing

```
[ ] Check if MFA is required
[ ] Check if MFA can be bypassed via API
[ ] Check if MFA is required for sensitive actions
[ ] Check MFA code brute force protection
[ ] Check MFA fallback mechanisms
```

### Token Testing

```
[ ] Check where tokens are stored (SharedPreferences? Keystore?)
[ ] Check if tokens are sent over HTTP
[ ] Check token expiry time
[ ] Check if logout invalidates the token
[ ] Try to reuse an old token
```

### Session Testing

```
[ ] Check session timeout
[ ] Check concurrent session handling
[ ] Check if session survives password change
[ ] Check session fixation protection
```

## Resources

### Official Documentation

| Resource | URL |
|----------|-----|
| Android Credentials API | developer.android.com/training/sign-in |
| Biometric Authentication | developer.android.com/training/sign-in/biometric-auth |
| Android Keystore | developer.android.com/training/articles/keystore |
| OAuth Best Practices | datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics |

### OWASP Resources

| Resource | URL |
|----------|-----|
| OWASP Mobile Top 10 (M4) | owasp.org/www-project-mobile-top-10 |
| Authentication Cheat Sheet | cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html |
| Mobile Security Testing Guide | owasp.org/www-project-mobile-security-testing-guide |

### Password Security Resources

| Resource | URL |
|----------|-----|
| Have I Been Pwned API | haveibeenpwned.com/API |
| NIST Password Guidelines | pages.nist.gov/800-63-3 |
| Common Password List | github.com/danielmiessler/SecLists |

## Summary

Test authentication using Burp Suite for API-level testing, JWT_Tool for token testing, and Hydra for password brute force testing. Check password policies, MFA implementation, token security, and session management. Use official Android documentation and OWASP guides for secure authentication implementation.