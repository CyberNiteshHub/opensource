# Module 24: Report Writing - Technical Report for IT and Security Department

## Overview

The technical report is for developers, IT teams, and security engineers. It provides detailed information needed to fix vulnerabilities.

## Who Reads Technical Reports

| Role | What They Need |
|------|---------------|
| Developers | Exact file, line numbers, fix code |
| Security engineers | Reproduction steps, impact analysis |
| IT operations | Configuration changes needed |
| QA testers | Test cases to verify fixes |

## Technical Report Structure

### 1. Finding ID and Title

```
F-001: Hardcoded API Key in MainActivity.java
```

### 2. Severity Rating

Use CVSS (Common Vulnerability Scoring System):
```
CVSS Score: 8.6 (HIGH)
Vector: AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:N/A:N
```

### 3. Affected Component

```
File: app/src/main/java/com/example/app/MainActivity.java
Line: 45
API Endpoint: N/A (Code-level issue)
```

### 4. Vulnerability Description

Technical explanation of the issue.

```
The application contains a hardcoded Stripe API key
(sk_live_3f8a2b1c9d0e) in the MainActivity.java source file.
This key is used for payment processing and is visible to
anyone who decompiles the APK.
```

### 5. Steps to Reproduce

```
1. Download the APK from the production environment
2. Decompile using jadx:
   $ jadx app.apk
3. Navigate to com/example/app/MainActivity.java
4. Observe the hardcoded API key on line 45
```

### 6. Proof of Concept

```java
// Vulnerable code in MainActivity.java
public void initializePayment() {
    // API key is hardcoded here
    String stripeApiKey = "sk_live_3f8a2b1c9d0e";
    Stripe stripe = new Stripe(stripeApiKey);
}
```

### 7. Remediation

Code-level fix instructions.

```java
// Fixed: Remove hardcoded key, fetch from server
public void initializePayment() {
    // Fetch API key from backend server
    apiService.getStripeKey(new Callback<String>() {
        @Override
        public void onSuccess(String key) {
            Stripe stripe = new Stripe(key);
        }
    });
}
```

Also requires:
- Remove the hardcoded key from code
- Add a secure API endpoint to serve the key
- Implement authentication for the key endpoint
- Use HTTPS for all communication

### 8. References

```
- OWASP Mobile Top 10 - M2: Insecure Data Storage
- CWE-798: Use of Hardcoded Credentials
- Stripe Security Best Practices
```

## Technical Report Template

```
Finding ID: [ID]
Title: [Title]
Severity: [CVSS Score and Rating]
Affected Component: [File, Line, Endpoint]
Category: [OWASP Category]

Description:
[Detailed technical description]

Steps to Reproduce:
1. [Step 1]
2. [Step 2]
3. [Step 3]

Proof of Concept:
[Code snippet or request/response]

Remediation:
[Step-by-step fix instructions]
[Code examples]

References:
[Links to OWASP, CWE, documentation]
```

## Best Practices for Technical Writing

| Practice | Description |
|----------|-------------|
| Be precise | Exact file paths and line numbers |
| Include code | Vulnerable and fixed code |
| Use CVSS | Standard severity scoring |
| Prioritize | Critical findings first |
| Verify fixes | Suggest how to test the fix |

## Summary

Technical reports provide developers with exact details needed to fix vulnerabilities. Include file paths, line numbers, vulnerable code, fix code, and reproduction steps. Use CVSS for severity ratings. The technical report is essential for the remediation process.