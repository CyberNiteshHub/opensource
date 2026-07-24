# Module 10: Insecure Data Storage - Tools and Resources

## Overview

This lesson covers tools to find insecure data storage vulnerabilities and resources to learn about secure storage practices.

## Tools for Detecting Insecure Data Storage

### 1. MobSF

MobSF checks for many data storage issues.

**What it finds:**
- Hardcoded passwords and secrets
- Plain text SharedPreferences
- Unencrypted databases
- Insecure file permissions
- External storage usage
- Logging of sensitive data
- Cache data exposure

**How to use:**
Upload APK and check "Code Analysis" and "Storage Analysis" sections.

### 2. Drozer

Drozer can check for data exposure through content providers.

**What it finds:**
- Exported content providers with sensitive data
- Accessible databases through providers
- File read/write via providers

**Key commands:**
```
dz> run app.provider.info -a com.example.app
dz> run app.provider.query content://com.example.app/database
dz> run app.provider.read content://com.example.app/files
```

### 3. ADB (Android Debug Bridge)

ADB is essential for manual data storage testing.

**Commands for data inspection:**
```
# Check app data directory
adb shell run-as com.example.app ls -la

# Read SharedPreferences
adb shell run-as com.example.app cat shared_prefs/*.xml

# Check databases
adb shell run-as com.example.app ls databases/
adb shell run-as com.example.app
sqlite3 databases/data.db
.tables
SELECT * FROM users;

# Check files
adb shell run-as com.example.app ls files/
adb shell run-as com.example.app cat files/config.txt

# Check cache
adb shell run-as com.example.app ls cache/

# Check external storage
adb shell ls /sdcard/Android/data/com.example.app/
```

### 4. Objection

Objection is a runtime mobile exploration tool.

**What it does:**
- Dumps SharedPreferences
- Dumps SQLite databases
- Searches for sensitive data in memory
- Bypasses SSL pinning for traffic inspection

**How to use:**
```
objection -g com.example.app explore
android sharedpreferences dump
android sqlite dump databases/data.db
```

### 5. Frida

Frida can hook into app functions and inspect data at runtime.

**Script to dump SharedPreferences:**
```javascript
Java.perform(function() {
    var prefs = Java.use('android.content.SharedPreferences');
    // Hook getAll method to dump stored values
});
```

**How to use:**
```
frida -U -l dump_data.js com.example.app
```

### 6. APKTool

APKTool can extract resources and check for hardcoded data.

**Commands:**
```
apktool d app.apk -o app
grep -r "password\|secret\|key\|token" app/res/values/strings.xml
grep -r "password\|secret\|key\|token" app/smali/
```

### 7. strings Command

The `strings` command extracts text strings from binary files.

```
strings app.apk | grep -i "password\|secret\|key\|token\|api_key"
strings app.apk | grep -E '[A-Za-z0-9]{20,}'  # Find potential keys
```

## Tool Comparison

| Tool | Data Storage Detection | Dynamic | Static | Ease |
|------|----------------------|---------|--------|------|
| MobSF | Yes | No | Yes | Easy |
| Drozer | Yes (providers) | Yes | No | Medium |
| ADB | Yes | Yes | No | Medium |
| Objection | Yes | Yes | No | Medium |
| Frida | Yes | Yes | No | Hard |
| APKTool | Yes (resources) | No | Yes | Easy |
| strings | Yes (basic) | No | Yes | Easy |

## Manual Testing Checklist

### Step-by-Step Testing

```
[ ] 1. Install the app on a rooted device/emulator
[ ] 2. Use the app, log in, perform sensitive actions
[ ] 3. Check SharedPreferences files:
       adb shell run-as com.example.app cat shared_prefs/*.xml
[ ] 4. Check SQLite databases:
       adb shell run-as com.example.app
       sqlite3 databases/*.db
[ ] 5. Check app files:
       adb shell run-as com.example.app ls files/
       adb shell run-as com.example.app cat files/*
[ ] 6. Check cache:
       adb shell run-as com.example.app ls cache/
[ ] 7. Check external storage:
       adb shell ls /sdcard/Android/data/com.example.app/
[ ] 8. Perform ADB backup and check for data:
       adb backup -f backup.ab com.example.app
[ ] 9. Check logcat for sensitive data:
       adb logcat -d | grep com.example.app
[ ] 10. Check screenshots (recent apps screen)
```

## Resources

### Official Android Documentation

| Resource | URL |
|----------|-----|
| Data and File Storage Overview | developer.android.com/training/data-storage |
| Android Keystore | developer.android.com/training/articles/keystore |
| EncryptedSharedPreferences | developer.android.com/reference/androidx/security/crypto/EncryptedSharedPreferences |
| Security Best Practices | developer.android.com/topic/security/best-practices |
| Network Security Config | developer.android.com/training/articles/security-config |

### OWASP Resources

| Resource | URL |
|----------|-----|
| OWASP Mobile Top 10 (M2) | owasp.org/www-project-mobile-top-10 |
| Mobile Security Testing Guide | owasp.org/www-project-mobile-security-testing-guide |
| OWASP Cheat Sheet Series | cheatsheetseries.owasp.org |

### SQLCipher Resources

- SQLCipher for Android: www.zetetic.net/sqlcipher
- SQLCipher GitHub: github.com/sqlcipher/android-database-sqlcipher

## Summary

Several tools help detect insecure data storage: MobSF for automated scanning, Drozer for provider testing, ADB for manual inspection, Objection and Frida for runtime analysis. Always check SharedPreferences, SQLite databases, files, cache, external storage, backups, and logs for sensitive data. Use official Android documentation and OWASP guides for secure storage best practices.