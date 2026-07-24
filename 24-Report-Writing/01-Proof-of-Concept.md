# Module 24: Report Writing - Proof of Concept (PoC)

## What is a Proof of Concept?

A Proof of Concept (PoC) demonstrates that a vulnerability is real. It shows step-by-step how an attacker would exploit the issue.

## Why PoCs are Important

- Proves the vulnerability exists (not a false positive)
- Helps developers understand the severity
- Provides evidence for compliance
- Allows developers to verify the fix

## How to Write a Clear PoC

### Step 1: Describe the Vulnerability

```
Vulnerability: Insecure Direct Object Reference (IDOR)
Location: GET /api/user/{id}/profile
```

### Step 2: Show Normal Behavior

```
Normal request:
GET /api/user/12345/profile
Authorization: Bearer <valid_token>

Response: Returns profile for user 12345
```

### Step 3: Show the Attack

```
Modified request:
GET /api/user/12346/profile
Authorization: Bearer <same_token>

Response: Returns profile for user 12346 (should not be accessible!)
```

### Step 4: Explain the Impact

The attacker can view any user's profile by changing the ID in the URL.

## Including Code/Commands

For a SQL injection PoC:

```
Vulnerable endpoint: GET /api/users?id=1

Normal: /api/users?id=1
Result: Returns user with ID 1

Exploit: /api/users?id=1 OR 1=1
Result: Returns all users (SQL injection confirmed)
```

## Screenshots and Evidence

- Take screenshots of the vulnerability
- Capture network requests in Burp Suite
- Save log output
- Record video for complex attacks

## Example PoC

```
Finding: Hardcoded API Key

Steps to Reproduce:
1. Download the APK from Google Play
2. Decompile with jadx:
   $ jadx com.example.app.apk
3. Open MainActivity.java
4. Line 45 contains:
   String apiKey = "sk_live_abc123def456";
5. The key is visible in plain text

Impact: Anyone can extract this API key from the APK
```

## Summary

A PoC proves a vulnerability exists by showing step-by-step exploitation. Include normal behavior, the attack, evidence (screenshots, code), and impact. This helps developers understand and fix the issue.