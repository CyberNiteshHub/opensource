# Module 12: Insecure Authentication - Weak Password Policies

## Overview

Weak password policies make it easy for attackers to guess or crack user passwords. This is one of the most common authentication vulnerabilities.

## What Are Weak Password Policies?

Weak password policies mean the app does not enforce strong password requirements. Users can choose passwords that are easy to guess.

## Common Weak Policies

### 1. No Minimum Length

Users can choose very short passwords.

**Example:**
```java
// No length validation
String password = editTextPassword.getText().toString();
// Password could be "a" or "12"
```

**How attackers exploit:**
- Brute force all 4-character passwords
- 26^4 = 456,976 combinations (cracked instantly)

### 2. No Complexity Requirements

Users can use simple passwords without numbers or symbols.

**Examples of weak passwords that pass:**
- password
- 123456
- qwerty
- letmein
- admin

### 3. No Blocked Password List

Common passwords are accepted.

**Top 10 most common passwords:**
```
1. 123456
2. password
3. 12345678
4. qwerty
5. 12345
6. 123456789
7. letmein
8. 1234567
9. football
10. iloveyou
```

### 4. No Account Lockout

Users can try unlimited passwords.

**How attackers exploit:**
- Automated brute force attacks
- Try thousands of passwords per minute
- No rate limiting

### 5. No Password History

Users can reuse old passwords.

**How attackers exploit:**
- If a password is compromised, user changes back to it later
- Attacker can access the account again

## How Attackers Exploit Weak Passwords

### Brute Force Attack

The attacker tries every possible password combination.

```
1. Attacker has username "admin"
2. Tries: a, b, c, ..., aa, ab, ac, ..., aaa, aab, ...
3. Eventually finds the correct password
```

**Time to crack by password length:**
| Length | Only Lowercase | With Numbers | With Symbols |
|--------|---------------|--------------|--------------|
| 4 | Instant | Instant | Instant |
| 6 | 1 minute | 5 minutes | 1 hour |
| 8 | 2 hours | 1 day | 1 week |
| 10 | 1 month | 1 year | 10 years |
| 12 | 10 years | 1000 years | 10,000 years |

### Dictionary Attack

The attacker tries common passwords from a list.

**Common password lists:**
- rockyou.txt (14 million passwords)
- SecLists (various lists)
- Common passwords list (10,000 most common)

### Credential Stuffing

The attacker uses passwords leaked from other sites.

Since users reuse passwords, a breach on one site compromises accounts on other sites.

### Social Engineering

The attacker guesses passwords based on user information.

**Common patterns:**
- Birthdays (John1990)
- Pet names (Fluffy123)
- Favorite sports team (Lakers2024)
- Relationship status (ILoveYou)

## How Apps Enforce Weak Policies

### Example 1: No Validation

```java
// No password validation at all
String password = editText.getText().toString();
if (password.length() > 0) {
    // Accept any password with at least 1 character
}
```

### Example 2: Minimal Validation

```java
// Only checks minimum length
if (password.length() < 4) {
    showError("Password too short");
} else {
    // Accept passwords like "abcd"
}
```

### Example 3: Client-Side Only Validation

```java
// Validation only on the app (not on server)
if (isValidPassword(password)) {
    sendToServer(password);
    // Server does not check password strength
}
```

Attackers can bypass client-side validation by:
- Intercepting and modifying the request
- Using a modified version of the app
- Calling the API directly

## Testing for Weak Password Policies

### Test 1: Register with Weak Password

```
1. Try to register with password "a"
2. If accepted, policy is very weak
3. Try "password", "123456", "admin"
4. If accepted, no common password check
```

### Test 2: Check for Rate Limiting

```
1. Create a script to try many passwords
2. Try 100 incorrect passwords
3. If no lockout, rate limiting is missing
```

### Test 3: Check for Server-Side Validation

```
1. Intercept registration request with Burp Suite
2. Modify the password to "a"
3. Send the modified request
4. If accepted, server has no validation
```

## Strong Password Policy Requirements

A strong password policy should include:

| Requirement | Recommendation |
|-------------|---------------|
| Minimum length | 8 characters minimum |
| Maximum length | 64+ characters (accept long passwords) |
| Complexity | At least 3 of: uppercase, lowercase, numbers, symbols |
| Blocked list | Reject top 10,000 common passwords |
| Account lockout | Lock after 5-10 failed attempts |
| Password history | Remember last 5 passwords |
| Password expiry | 90 days (for sensitive apps) |

## Implementation

### Client-Side Validation (UX, not security)

```java
public boolean isValidPassword(String password) {
    if (password.length() < 8) return false;
    if (password.length() > 64) return false;
    if (isCommonPassword(password)) return false;
    return true;
}
```

### Server-Side Validation (real security)

```java
// Server must always validate
public boolean isValidPassword(String password) {
    if (password.length() < 8) return false;
    if (isCommonPassword(password)) return false;
    return true;
}
```

## Summary

Weak password policies allow attackers to guess or crack passwords. Common issues include no minimum length, no complexity requirements, no blocked password list, and no account lockout. Enforce strong password policies on both client and server. Always validate on the server side.