# Module 06: Reversing App with MobSF - Overview

## What is MobSF?

MobSF stands for Mobile Security Framework. It is an automated tool for testing mobile apps. It can analyze both Android and iOS apps. MobSF performs security analysis and gives you a detailed report.

Think of MobSF as a security scanner for mobile apps. You give it an APK or IPA file. It analyzes the file and tells you about security problems it finds.

## Why MobSF is Useful

**1. Automated Analysis**
MobSF does most of the work automatically. You upload an APK and it scans for vulnerabilities. This saves a lot of time.

**2. Both Static and Dynamic Analysis**
MobSF can analyze the code without running it (static). It can also run the app and watch its behavior (dynamic). Most tools only do one or the other.

**3. Web Interface**
MobSF runs as a web application. You use it through your browser. No command line needed for basic operations.

**4. Detailed Reports**
MobSF generates professional reports. The reports include screenshots, code snippets, and severity ratings.

**5. Open Source**
MobSF is free and open source. You can modify it to add your own checks.

## Supported Platforms

| Platform | Static Analysis | Dynamic Analysis |
|----------|----------------|------------------|
| Android APK | Yes | Yes |
| iOS IPA | Yes | No |
| Windows Phone | Basic | No |

## What MobSF Can Find

MobSF checks for many types of vulnerabilities:

| Category | Examples |
|----------|----------|
| Insecure Data Storage | Hardcoded passwords, exposed databases |
| Insecure Communication | HTTP traffic, weak SSL |
| Authentication Issues | Weak password policies, missing MFA |
| Authorization Flaws | Exported components, IDOR |
| Cryptography Issues | Weak algorithms, hardcoded keys |
| Code Quality | Debug code, logging issues |
| Permissions | Over-permissive permissions |
| Network Security | Cleartext traffic, insecure SSL |
| Manifest Misconfigurations | Exported activities, backup enabled |

## How MobSF Works

```
Upload APK/IPA
     |
     v
MobSF Analyzes
     |
     +---> Extract and Decode
     |         |
     |         v
     +---> Static Analysis
     |         |
     |         +---> Manifest Analysis
     |         +---> Code Analysis
     |         +---> Resource Analysis
     |         +---> Permission Analysis
     |         |
     |         v
     +---> Dynamic Analysis (Optional)
     |         |
     |         +---> Runtime Behavior
     |         +---> Network Traffic
     |         +---> File System Changes
     |         |
     |         v
     +---> Generate Report
               |
               +---> PDF Report
               +---> HTML Report
               +---> JSON Report
```

## MobSF vs Other Tools

| Feature | MobSF | Apktool | jadx | Drozer |
|---------|-------|---------|------|--------|
| Automated Analysis | Yes | No | No | No |
| Report Generation | Yes | No | No | No |
| Web Interface | Yes | No | No | No |
| iOS Support | Yes | No | No | No |
| Dynamic Analysis | Yes | No | No | Yes |
| Ease of Use | Easy | Medium | Medium | Medium |

## When to Use MobSF

**Use MobSF when:**
- You want a quick security assessment
- You need a professional report
- You are testing many apps
- You want automated vulnerability detection
- You need both static and dynamic analysis

**Do not rely only on MobSF when:**
- You need deep manual analysis
- You need to exploit vulnerabilities (MobSF only finds them)
- You are testing custom business logic
- You need runtime modification (use Frida instead)

## MobSF Architecture

```
+------------------+
|   Web Browser    |
+------------------+
        |
        v
+------------------+
|   MobSF Web UI   |  (Python Django)
+------------------+
        |
        v
+------------------+
|   Analysis Engine|
+------------------+
        |
        +---> Static Analyzer
        |       - APK Analyzer
        |       - IPA Analyzer
        |       - Source Code Analyzer
        |
        +---> Dynamic Analyzer
        |       - Runtime Tester
        |       - Network Capture
        |       - Screenshot Engine
        |
        +---> API Tester
                - Endpoint Scanner
                - Fuzzer
```

## Summary

MobSF is a powerful automated security testing tool for mobile apps. It supports both Android and iOS. It performs static and dynamic analysis. It generates professional reports. Use it for quick security assessments and automated vulnerability scanning.