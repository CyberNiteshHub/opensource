# Module 01: Introduction to MPT - Methodology

## What is a Testing Methodology?

A methodology is a step-by-step plan. It tells you what to do in what order. In mobile penetration testing, the methodology helps you test apps in a complete and organized way. Without a methodology, you might miss important security checks.

## Standard Testing Methodology

The standard methodology has four main phases:

```
Phase 1: Reconnaissance (Gather Info)
     |
     v
Phase 2: Scanning (Find Weaknesses)
     |
     v
Phase 3: Exploitation (Break In)
     |
     v
Phase 4: Reporting (Write Findings)
```

### Phase 1: Reconnaissance (Information Gathering)

In this phase, we collect information about the app. We do not run any attacks yet.

**What we do:**
- Download the APK or IPA file
- Read the app description and documentation
- Check the app permissions
- Look at the app store page
- Identify the backend servers
- Read the privacy policy

**Tools used:** Google search, App Store, APKMirror, VirusTotal

### Phase 2: Scanning (Vulnerability Identification)

In this phase, we scan the app to find security problems. We use tools to check for known vulnerabilities.

**What we do:**
- Decompile the APK to see the code
- Check for hardcoded secrets (passwords, API keys)
- Analyze the manifest file for exported components
- Test network traffic for unencrypted data
- Check for weak encryption algorithms
- Look for insecure data storage

**Tools used:** MobSF, Apktool, jadx, Drozer, Burp Suite

### Phase 3: Exploitation (Making the Attack Work)

In this phase, we try to exploit the vulnerabilities we found. We want to prove that the security problem is real.

**What we do:**
- Try to access protected data without login
- Try to bypass authentication
- Try to intercept and modify network traffic
- Try to execute code on the app
- Try to access other users data

**Tools used:** Burp Suite, Frida, Drozer, custom scripts

### Phase 4: Reporting

In this phase, we write down everything we found. We explain the problems and how to fix them.

**What we do:**
- Write an executive summary
- List all vulnerabilities found
- Explain how to reproduce each finding
- Give risk ratings (Critical, High, Medium, Low)
- Provide remediation steps
- Include screenshots and evidence

## OWASP Mobile Top 10 Overview

OWASP is a community that publishes security standards. Their Mobile Top 10 lists the most common mobile security risks:

| Rank | Risk Name |
|------|-----------|
| M1 | Improper Platform Usage |
| M2 | Insecure Data Storage |
| M3 | Insecure Communication |
| M4 | Insecure Authentication |
| M5 | Insufficient Cryptography |
| M6 | Insecure Authorization |
| M7 | Client Code Quality |
| M8 | Code Tampering |
| M9 | Reverse Engineering |
| M10 | Extraneous Functionality |

We will cover all these risks in this course.

## Types of Testing

### Black Box Testing

The tester has no information about the app. No source code, no credentials, no documentation. This simulates an external attacker.

### White Box Testing

The tester has full information. Source code, credentials, architecture documents. This is the most thorough testing.

### Gray Box Testing

The tester has some information. Maybe they have credentials but no source code. This is the most common type of testing in real projects.

## Step-by-Step Testing Approach

1. **Scope definition** - Decide what to test
2. **Information gathering** - Collect app data
3. **Static analysis** - Analyze code without running it
4. **Dynamic analysis** - Run the app and observe behavior
5. **Network analysis** - Intercept and analyze traffic
6. **Exploitation** - Try to exploit vulnerabilities
7. **Reporting** - Document findings

## Summary

A good methodology helps you test apps completely. Always follow the steps in order. Start with reconnaissance, then scanning, then exploitation, and finally reporting. Use the OWASP Mobile Top 10 as a checklist to make sure you do not miss important vulnerabilities.