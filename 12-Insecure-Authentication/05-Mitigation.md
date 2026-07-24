# Module 12: Insecure Authentication - Mitigation

## Overview

Mitigation means implementing proper authentication. This lesson covers how to secure authentication in mobile apps.

## 1. Strong Password Policies

Enforce strong passwords on both client and server.

```java
// Client-side validation (for UX)
public String validatePassword(String password) {
    if (password.length() < 8) {
        return "Password must be at least 8 characters";
    }
    if (password.length() > 64) {
        return "Password must be less than 64 characters";
    }
    if (isCommonPassword(password)) {
        return "This password is too common";
    }
    return null; // Validation passed
}
```

**Server-side (must always validate):**
```java
// Server validates password strength
if (!isStrongPassword(password)) {
    throw new WeakPasswordException();
}
```

### Recommended Password Policy

| Requirement | Value |
|-------------|-------|
| Minimum length | 8 characters |
| Maximum length | 64+ characters |
| Complexity | 3 of 4: upper, lower, number, symbol |
| Common passwords | Block top 10,000 |
| Account lockout | After 5 failed attempts |
| Lockout duration | 15 minutes (increasing) |

## 2. Implement Multi-Factor Authentication

MFA should be required for all users, not optional.

### TOTP Implementation

```java
// Server generates secret
String secret = generateTOTPSecret();
storeSecretForUser(userId, secret);

// Display QR code for authenticator app
displayQRCode(secret);

// Verify code from user
if (verifyTOTP(secret, userCode)) {
    grantAccess();
} else {
    reject("Invalid code");
}
```

### Where MFA Should Be Required

```
[ ] Account login (always)
[ ] Password changes
[ ] Adding new devices
[ ] Making payments
[ ] Changing contact info
[ ] Viewing sensitive data
```

## 3. Secure Token Management

### Token Generation

Use cryptographically secure random tokens.

```java
// Good - secure random
SecureRandom random = new SecureRandom();
byte[] tokenBytes = new byte[32];
random.nextBytes(tokenBytes);
String token = Base64.encodeToString(tokenBytes, Base64.URL_SAFE);
```

### Token Storage on Device

```java
// Store token securely
MasterKey masterKey = new MasterKey.Builder(context)
    .setKeyScheme(MasterKey.KeyScheme.AES256_GCM)
    .build();

SharedPreferences prefs = EncryptedSharedPreferences.create(
    context, "auth_prefs", masterKey,
    EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
    EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
);

prefs.edit().putString("auth_token", token).apply();
```

### Token Expiry

| Token Type | Suggested Expiry |
|------------|-----------------|
| Access token | 15 minutes |
| Refresh token | 7 days |
| Remember me token | 30 days |

## 4. Secure Session Management

### Implement Session Timeout

```java
// Check if session is expired
long lastActivity = prefs.getLong("last_activity", 0);
long currentTime = System.currentTimeMillis();
long timeout = 15 * 60 * 1000; // 15 minutes

if (currentTime - lastActivity > timeout) {
    // Session expired - require re-authentication
    logout();
}
```

### Invalidate Sessions on Logout

```java
public void logout() {
    // Clear local token
    prefs.edit().remove("auth_token").apply();

    // Invalidate server-side token
    api.invalidateToken(token);
}
```

## 5. Biometric Authentication

Implement biometrics as an additional factor, not a replacement.

```java
BiometricPrompt biometricPrompt = new BiometricPrompt.Builder(context)
    .setTitle("Authenticate")
    .setSubtitle("Verify your identity")
    .setAllowedAuthenticators(
        BiometricManager.Authenticators.BIOMETRIC_STRONG |
        BiometricManager.Authenticators.DEVICE_CREDENTIAL
    )
    .build();

biometricPrompt.authenticate(
    new BiometricPrompt.AuthenticationCallback() {
        @Override
        public void onAuthenticationSucceeded(
                BiometricPrompt.AuthenticationResult result) {
            // Biometric verified - now check password or token
            checkMainAuthentication();
        }
    }
);
```

**Important:** Biometrics should be an additional layer, not the only authentication.

## 6. OAuth/SSO Security

### OAuth Best Practices

```
[ ] Use PKCE (Proof Key for Code Exchange)
[ ] Validate redirect URIs strictly
[ ] Use state parameter to prevent CSRF
[ ] Use short-lived authorization codes
[ ] Validate token signatures
[ ] Check token expiry on each request
```

## 7. API Authentication

### Every API request must be authenticated

```java
// Always validate token on server
@PostMapping("/api/data")
public Response getData(@RequestHeader("Authorization") String token) {
    if (!isValidToken(token)) {
        return Response.status(401).body("Unauthorized");
    }
    // Process request
}
```

## 8. Security Checklist

```
[ ] Strong password policy enforced
[ ] Common passwords blocked
[ ] Account lockout after failed attempts
[ ] MFA required for all users
[ ] MFA required for sensitive actions
[ ] Tokens generated with SecureRandom
[ ] Tokens stored in EncryptedSharedPreferences
[ ] Short token expiry
[ ] Session timeout implemented
[ ] Logout invalidates tokens
[ ] Biometrics as additional factor only
[ ] OAuth with PKCE
[ ] Server validates all authentication
[ ] Rate limiting on login endpoints
```

## Summary

Mitigate insecure authentication with strong password policies, MFA, secure token management, proper session handling, and biometric best practices. Enforce policies on both client and server. Always validate authentication on the server side.