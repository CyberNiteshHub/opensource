# Module 12: Insecure Authentication - Definition

## What is Insecure Authentication?

Insecure Authentication is the number four risk in the OWASP Mobile Top 10 (M4). It means the app does not properly verify the identity of users. Attackers can bypass login, impersonate users, or gain unauthorized access.

## OWASP Mobile Top 10 - M4

M4 covers authentication weaknesses:
- Weak password policies
- No multi-factor authentication (MFA)
- Predictable login credentials
- Session management issues
- Biometric authentication weaknesses
- Token-based authentication flaws

## Authentication vs Authorization

**Authentication:** Verifying who the user is (login)
**Authorization:** Verifying what the user can do (permissions)

Both are important. M4 covers authentication only.

## Mobile Authentication Challenges

Mobile apps face unique authentication challenges compared to web apps:

| Challenge | Description |
|-----------|-------------|
| Multiple sessions | User may use app on multiple devices |
| Offline access | App may need to work without internet |
| Biometrics | Touch ID / Face ID introduce new attack surface |
| Token storage | Tokens stored on device can be stolen |
| Push notifications | Authentication via push has its own risks |
| Deep links | Authentication flows can be bypassed via deep links |

## Common Authentication Vulnerabilities

### 1. Weak Password Policies

The app accepts weak passwords.

**Examples:**
- No minimum password length
- No complexity requirements (uppercase, numbers, symbols)
- No maximum attempts
- Common passwords allowed (password123, admin)

### 2. No Multi-Factor Authentication

The app only uses username and password.

Without MFA, if the password is stolen, the account is compromised.

### 3. Predictable Credentials

Default or predictable usernames and passwords.

**Examples:**
- admin/admin
- test/test123
- User ID as password
- Pattern-based passwords (user1/pass1, user2/pass2)

### 4. Insecure Token Handling

Authentication tokens are stored or transmitted insecurely.

**Issues:**
- Tokens stored in plain text
- Tokens sent over HTTP
- Tokens with long expiry
- Tokens not invalidated on logout
- Predictable token generation

### 5. Session Management Issues

Problems with how sessions are managed.

**Issues:**
- Session does not expire
- Session not invalidated on logout
- Multiple concurrent sessions allowed without limit
- Session fixation

### 6. Biometric Authentication Weaknesses

Biometric authentication (fingerprint, face) can be bypassed.

**Issues:**
- Fallback to PIN/password without proper security
- Biometric data stored insecurely
- No liveness detection (photo can bypass face recognition)
- Spoofing with fake fingerprints

### 7. OAuth/SSO Implementation Flaws

Problems with third-party authentication.

**Issues:**
- CSRF in OAuth flow
- Redirect URI validation issues
- Token interception
- Authorization code interception

### 8. Local Authentication Bypass

The app stores authentication state locally. Attackers modify it.

**Example:**
```java
SharedPreferences prefs = getSharedPreferences("auth", MODE_PRIVATE);
boolean isLoggedIn = prefs.getBoolean("isLoggedIn", false);
if (isLoggedIn) {
    // Show main screen (bypasses login)
}
```

If an attacker modifies the SharedPreferences, they bypass login.

## Mobile vs Web Authentication

| Aspect | Web | Mobile |
|--------|-----|--------|
| Login | Browser-based | App-based |
| Token storage | Cookies | SharedPreferences/Keychain |
| Biometrics | Rare | Common |
| Offline access | Limited | Often needed |
| Device binding | No | Possible |
| Push auth | Possible | Common |

## Why Insecure Authentication Happens

1. **Convenience over Security** - Easy login is prioritized over secure login
2. **Lack of Understanding** - Developers do not understand authentication security
3. **Complexity** - Proper authentication is complex to implement
4. **Budget Constraints** - MFA and proper auth require resources
5. **Legacy Systems** - Old authentication systems that are not updated

## Summary

Insecure Authentication (M4) covers weak password policies, lack of MFA, predictable credentials, insecure token handling, session issues, biometric weaknesses, and OAuth flaws. Mobile apps face unique authentication challenges like offline access and device storage of tokens. Proper authentication is essential for app security.