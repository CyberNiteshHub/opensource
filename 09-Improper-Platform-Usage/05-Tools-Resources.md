# Module 09: Improper Platform Usage - Tools and Resources

## Overview

This lesson covers tools to find improper platform usage vulnerabilities and resources to learn more about preventing them.

## Tools for Finding Improper Platform Usage

### 1. MobSF

MobSF can detect many improper platform usage issues.

**What it finds:**
- Exported components
- WebView misconfigurations
- Backup settings
- Debug mode enabled
- Permission issues
- Insecure intent usage

**How to use:**
Upload the APK to MobSF and check the "Manifest Analysis" and "Code Analysis" sections.

### 2. Drozer

Drozer is excellent for testing exported components dynamically.

**What it finds:**
- Exported activities, services, providers, receivers
- Intent injection vulnerabilities
- Content provider data exposure

**Key commands:**
```
dz> run app.package.attacksurface com.example.app
dz> run app.activity.info -a com.example.app
dz> run app.provider.info -a com.example.app
```

### 3. QARK (Quick Android Review Kit)

QARK specifically checks for platform misuse.

**What it finds:**
- Exported components
- WebView issues
- Intent-related vulnerabilities
- Backup and debug configuration

**How to use:**
```
git clone https://github.com/linkedin/qark
cd qark
pip install -r requirements.txt
python qark.py --apk app.apk
```

### 4. Android Studio Lint

Android Studio includes a linter that finds platform usage issues.

**What it finds:**
- Exported components without permissions
- WebView JavaScript enabled
- Hardcoded values
- Permission issues

**How to use:**
In Android Studio: Analyze -> Inspect Code

### 5. AndroBugs

AndroBugs is a static analysis tool for Android security.

**What it finds:**
- Component exposure
- WebView configuration issues
- Backup and debug flags
- Permission problems

**How to use:**
```
git clone https://github.com/AndroBugs/AndroBugs
cd AndroBugs
python androbugs.py -f app.apk
```

## Tool Comparison

| Tool | Component Check | WebView Check | Backup Check | Permission Check | Ease of Use |
|------|----------------|---------------|--------------|------------------|-------------|
| MobSF | Yes | Yes | Yes | Yes | Easy |
| Drozer | Yes | No | No | Partial | Medium |
| QARK | Yes | Yes | Yes | Yes | Medium |
| Android Lint | Yes | Yes | Yes | Yes | Easy |
| AndroBugs | Yes | Yes | Yes | Yes | Medium |

## Official Documentation

### Android Security Documentation

1. **Android Security Overview**
   https://developer.android.com/privacy-and-security

2. **Android Manifest Security**
   https://developer.android.com/guide/topics/manifest/manifest-intro

3. **App Security Best Practices**
   https://developer.android.com/topic/security/best-practices

4. **Permissions Overview**
   https://developer.android.com/guide/topics/permissions/overview

5. **WebView Security**
   https://developer.android.com/training/articles/security-tips#WebView

6. **Network Security Config**
   https://developer.android.com/training/articles/security-config

7. **Backup Security**
   https://developer.android.com/guide/topics/data/autobackup

### OWASP Resources

1. **OWASP Mobile Top 10**
   https://owasp.org/www-project-mobile-top-10/

2. **OWASP Mobile Security Testing Guide**
   https://owasp.org/www-project-mobile-security-testing-guide/

3. **OWASP Mobile Application Security Verification Standard (MASVS)**
   https://mas.owasp.org/

## Security Checklists

### Pre-Release Checklist

```
[ ] All components reviewed for export necessity
[ ] WebView JavaScript disabled if not needed
[ ] File access disabled in WebView
[ ] allowBackup set to false or restricted
[ ] Debug mode disabled
[ ] All intents use explicit targeting
[ ] Content providers have proper permissions
[ ] Deep links use autoVerify
[ ] Clipboard does not contain sensitive data
[ ] Notifications do not contain sensitive data
```

### Testing Checklist

```
[ ] Scan with MobSF - review all findings
[ ] Test exported activities with Drozer
[ ] Test exported providers with Drozer
[ ] Test exported services with Drozer
[ ] Test WebView for XSS
[ ] Test backup for data leakage
[ ] Test deep links for hijacking
[ ] Test clipboard for sensitive data
[ ] Test notifications for data leakage
```

## Training Resources

### Online Courses

| Course | Platform | Focus |
|--------|----------|-------|
| Android Security Essentials | Coursera | Platform security |
| Mobile Security Testing | Udemy | Practical testing |
| OWASP Mobile Testing Guide | Free | Comprehensive guide |

### Books

| Title | Author | Focus |
|-------|--------|-------|
| Android Security Internals | Nikolay Elenkov | Deep Android security |
| The Mobile Application Hacker's Handbook | Chell et al. | Mobile pentesting |
| Android Hacker's Handbook | Drake et al. | Android exploitation |

## Summary

Several tools can detect improper platform usage: MobSF, Drozer, QARK, Android Studio Lint, and AndroBugs. Official Android documentation and OWASP guides provide best practices. Use pre-release and testing checklists to ensure all platform usage issues are addressed before release.