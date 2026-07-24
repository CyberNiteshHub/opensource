# Module 22: Report Preparation using AI - Detailing Findings

## Overview

Each finding in the report must be detailed enough for developers to understand and fix. This lesson covers how to write detailed findings.

## Finding Structure

Each finding should include:

```
1. Finding ID: F-001
2. Title: Hardcoded API Key
3. Severity: Critical
4. Location: MainActivity.java:45
5. Category: Insecure Data Storage
6. Description:
   A Stripe API key was found hardcoded in the source code.
7. Impact:
   Anyone who decompiles the app can extract this key and
   make unauthorized API calls.
8. Steps to Reproduce:
   1. Decompile the APK with jadx
   2. Open MainActivity.java
   3. Line 45 shows the hardcoded key
9. Proof of Concept:
   [code snippet showing the key]
10. Remediation:
    1. Remove the key from code
    2. Store keys on a backend server
    3. Use server as proxy for API calls
```

## Using AI to Detail Findings

**Prompt:**
```
I found a hardcoded API key in an Android app. The key is
stored as a string in ApiClient.java. Write a detailed
finding with description, impact, steps to reproduce, and
remediation.
```

**AI output can include:**
- Clear description of the issue
- Why it is a problem
- Step-by-step reproduction
- Code-level fix suggestions
- References to OWASP categories

## Best Practices

| Practice | Description |
|----------|-------------|
| Be specific | Exact file and line numbers |
| Include evidence | Screenshots, code snippets |
| Clear language | Explain technical issues simply |
| Actionable fixes | Developers should know what to change |
| Prioritize | Show which issues to fix first |

## Summary

Detailed findings include ID, title, severity, location, description, impact, reproduction steps, proof of concept, and remediation. AI helps write clear, detailed, and actionable finding descriptions that developers can use to fix vulnerabilities.