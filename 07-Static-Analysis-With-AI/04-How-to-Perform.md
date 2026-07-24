# Module 07: Static Analysis with AI - How to Perform Static Analysis

## Step-by-Step Guide

This lesson walks through the complete process of performing static analysis with AI assistance. Follow these steps for effective results.

## Step 1: Extract and Decompile the APK

First, get the app code in a readable format.

### If you have the APK:

```
# Decode with apktool
apktool d app.apk -o app_decoded

# Decompile to Java with jadx
jadx -d app_java app.apk
```

### If you have the source code:

The source code is already readable. You can start analysis directly.

### If you only have an installed app:

```
adb shell pm path com.example.app
adb pull /data/app/com.example.app/base.apk
```

## Step 2: Identify Areas to Analyze

Not all code is equally important. Focus on high-risk areas first.

### High Priority Areas

| Area | Why Important |
|------|---------------|
| Authentication code | Login, password handling |
| Network code | API calls, data transmission |
| Storage code | Database, SharedPreferences |
| WebView code | Browser-like functionality |
| Crypto code | Encryption, hashing |
| Third-party SDKs | Known vulnerabilities |

### Files to Check First

```
MainActivity.java
LoginActivity.java
ApiClient.java
DatabaseHelper.java
CryptoUtils.java
WebViewActivity.java
AndroidManifest.xml
```

## Step 3: Run Automated Scanning

Run MobSF or another automated tool first.

```
# Using MobSF
Upload APK to http://localhost:8000
Click "Upload" and wait for results
Export findings as JSON
```

## Step 4: Use AI to Analyze Code

Take the decompiled code and use AI for analysis.

### Method A: Copy-Paste Analysis

Copy specific files and ask AI to analyze them.

**Prompt template:**
```
You are a mobile security expert. Analyze this Android code
for security vulnerabilities. For each finding, provide:
1. The vulnerability name
2. The severity (Critical/High/Medium/Low)
3. The exact line number
4. Why it is a problem
5. How to fix it

Code:
[paste code here]
```

### Method B: Targeted Question Analysis

Ask specific questions about the code.

**Example prompts:**

| Goal | Prompt |
|------|--------|
| Find secrets | Find all hardcoded passwords, API keys, and tokens in this code. |
| Check auth | Analyze the authentication logic. Is it secure? |
| Check crypto | Review the encryption implementation. Is it using strong algorithms? |
| Check WebView | Is the WebView configured securely? |
| Find injections | Look for SQL injection and command injection risks. |

### Method C: Compare with Best Practices

Ask AI to check if the code follows security best practices.

```
Check if this Android code follows OWASP Mobile Top 10
best practices. List any violations found.
```

## Step 5: Cross-Verify AI Findings

AI can make mistakes. Verify all findings manually.

**Verification checklist:**

```
1. Is the finding real? (Try to confirm it)
2. Is the severity accurate? (Critical vs High?)
3. Is the fix suggestion correct? (Will it work?)
4. Are there false positives? (AI hallucinating?)
5. Is the finding in scope? (Part of testing scope?)
```

## Step 6: Document Findings

Create a structured record of all findings.

### Finding Template

```
Finding ID: F-001
Title: Hardcoded API Key
Severity: CRITICAL
Location: ApiClient.java:25
Category: Insecure Data Storage

Description:
The app contains a hardcoded Stripe API key
(sk_live_xxxxxxxxx). This key can be extracted
by anyone who downloads the APK.

Impact:
Attackers can use this key to make unauthorized
payments or access customer payment data.

Steps to Reproduce:
1. Decompile the APK with jadx
2. Open ApiClient.java
3. Line 25 shows the hardcoded key

Recommendation:
1. Remove the key from source code
2. Store keys on a backend server
3. Use the server as proxy for API calls

Code Snippet (Vulnerable):
  String apiKey = "sk_live_xxxxxxxxx";

Code Snippet (Fixed):
  // Remove hardcoded key
  // Fetch from backend server at runtime
```

## Step 7: Generate Report

Compile all findings into a professional report.

**Report sections:**
1. Executive Summary
2. Scope of Analysis
3. Methodology
4. Findings Summary
5. Detailed Findings
6. Risk Ratings
7. Recommendations
8. Appendix

## Complete Example Walkthrough

Let us analyze a sample app step by step.

### The App: SimpleChat.apk

**Step 1:** Decompile the app
```
jadx -d simplechat SimpleChat.apk
```

**Step 2:** Identify key areas
- LoginActivity.java (authentication)
- ChatService.java (network communication)
- DatabaseHelper.java (data storage)

**Step 3:** Ask AI to analyze LoginActivity

**Prompt:**
```
Analyze this Android LoginActivity for security issues.
Check for: hardcoded credentials, weak password handling,
insecure intent usage, and logging of sensitive data.

[code for LoginActivity.java]
```

**AI response:**
```
Finding 1: Hardcoded admin credentials
- Location: LoginActivity.java:15
- Severity: Critical
- Issue: Admin password "admin123" is hardcoded
- Fix: Use server-side authentication

Finding 2: Password in SharedPreferences
- Location: LoginActivity.java:42
- Severity: High
- Issue: Password saved in SharedPreferences (plain text)
- Fix: Use AccountManager or EncryptedSharedPreferences

Finding 3: Sensitive data in log
- Location: LoginActivity.java:50
- Severity: Medium
- Issue: Log.d("Login", "Password: " + password)
- Fix: Remove logging of sensitive data
```

**Step 4:** Verify findings manually
- Check LoginActivity.java:15 - confirmed, password is hardcoded
- Check LoginActivity.java:42 - confirmed, SharedPreferences used
- Check LoginActivity.java:50 - confirmed, password logged

**Step 5:** Document in report
- Create findings for each issue
- Add screenshots of the code
- Write remediation steps

## Best Practices for AI-Powered Analysis

**DO:**
- Use specific, detailed prompts
- Provide context about the app
- Verify all AI findings manually
- Use AI as an assistant, not a replacement
- Combine AI with automated tools

**DO NOT:**
- Trust AI blindly
- Share sensitive code with public AI tools
- Skip manual verification
- Rely only on AI for critical apps

## Summary

Performing static analysis with AI involves extracting code, identifying key areas, running automated scans, using AI for deep analysis, verifying findings, and documenting results. AI enhances the process but does not replace human expertise. Always verify AI findings manually before including them in the final report.