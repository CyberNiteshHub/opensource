# Module 18: Extraneous Functionality - Automated Analysis

## Overview

Automated tools can help detect extraneous functionality. This lesson covers tools and techniques for automated detection.

## Tools for Detecting Extraneous Functionality

### 1. MobSF

MobSF can detect many extraneous functionality issues.

**What it finds:**
- Debuggable flag in manifest
- Backup enabled
- Exported components (potential hidden features)
- Test URLs in code
- Debug code patterns

### 2. QARK

QARK specifically looks for extraneous functionality.

**What it finds:**
- Debug code
- Test activities
- Backdoors in code
- Hidden entry points

### 3. Android Studio Lint

Built into Android Studio. Finds many issues.

**Run lint:**
```
./gradlew lint
```

**What it finds:**
- Unused resources
- Unused code
- Debuggable flag
- Hardcoded debug values

### 4. Static Analysis with Grep

Manual grep searches for suspicious patterns.

```
# Search for debug patterns
grep -r "DEBUG\|debug\|test\|Test" app/src/

# Search for hardcoded credentials
grep -r "password\|admin\|secret" app/src/

# Search for test URLs
grep -r "test\|staging\|dev\." app/src/
```

## Automated Checklist

```
[ ] Lint passes with no errors
[ ] MobSF scan shows no debug issues
[ ] No exported components that should not be
[ ] No test server URLs in release build
[ ] No debug logging in release build
[ ] No hardcoded test credentials
[ ] No unused resources in the APK
```

## Summary

Automated tools like MobSF, QARK, and Android Lint help detect extraneous functionality. Regular scanning and grep searches catch debug code, test URLs, hidden features, and backdoors. Integrate these checks into your CI/CD pipeline.