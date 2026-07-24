# Module 03: Android Architecture - Security Model

## Overview

Android has a strong security model. It was designed with security in mind from the beginning. The security model has multiple layers. Even if one layer fails, other layers still protect the device.

## Key Principles of Android Security

1. **Sandboxing** - Each app runs in its own sandbox
2. **Permissions** - Apps must ask for access
3. **Application Signing** - Apps must be signed to be installed
4. **Encryption** - Data is encrypted at rest
5. **Verified Boot** - The system checks for tampering at boot

## 1. Android Sandbox

The sandbox is the most important security feature. Each app runs in its own isolated environment.

**How it works:**
- Each app gets its own Linux user ID (UID)
- Each app runs in its own process
- Each app has its own private directory: `/data/data/com.example.app/`
- One app cannot access another app's files by default

```
+------------------------------------------+
|            Android System                |
|                                           |
|  +------------------+  +----------------+|
|  | App A (UID=1001) |  | App B (UID=1002)||
|  | /data/data/app.a/|  |/data/data/app.b/||
|  +------------------+  +----------------+|
|                                           |
|  No access between apps (by default)     |
+------------------------------------------+
```

**What the sandbox protects against:**
- One app reading another app's files
- One app accessing another app's memory
- One app interfering with another app's processes

**Bypassing the sandbox:** Root access breaks the sandbox. A rooted device allows any app to read any file.

## 2. Permissions Model

Apps need permission to access sensitive data or features. Permissions are declared in the AndroidManifest.xml file.

### Types of Permissions

| Type | Description | Example |
|------|-------------|---------|
| Normal | Auto-granted, low risk | INTERNET, VIBRATE |
| Dangerous | User must approve | CAMERA, LOCATION, READ_CONTACTS |
| Signature | Same signing key required | System-level permissions |
| Special | Requires special approval | SYSTEM_ALERT_WINDOW |

### Permission Groups

Dangerous permissions are grouped. If the user grants one permission in a group, the whole group is granted.

| Group | Permissions |
|-------|-------------|
| CALENDAR | READ_CALENDAR, WRITE_CALENDAR |
| CAMERA | CAMERA |
| CONTACTS | READ_CONTACTS, WRITE_CONTACTS |
| LOCATION | ACCESS_FINE_LOCATION, ACCESS_COARSE_LOCATION |
| MICROPHONE | RECORD_AUDIO |
| PHONE | READ_PHONE_STATE, CALL_PHONE |
| SMS | READ_SMS, SEND_SMS |
| STORAGE | READ_EXTERNAL_STORAGE, WRITE_EXTERNAL_STORAGE |

### Runtime Permissions (Android 6+)

Since Android 6 (API 23), dangerous permissions must be requested at runtime, not just at install time.

```
App requests permission --> System shows dialog --> User grants/denies
```

**Security concern:** Many apps request more permissions than they need. This is called "permission creep." A calculator app does not need access to your contacts.

### Testing Permissions

Check the permissions an app requests:

```
aapt d permissions app.apk
```

Or use MobSF to analyze permissions.

## 3. Application Signing

Every Android app must be signed with a certificate before installation.

**Why signing is important:**
- Identifies the app developer
- Ensures the app has not been tampered with
- Prevents app impersonation
- Allows app updates (same signature required)

**Signature types:**

| Type | Use Case |
|------|----------|
| Debug Signature | Used during development (not secure) |
| Release Signature | Used for publishing (secure) |
| V1 (JAR) | Old signature scheme |
| V2 (APK) | Full APK signature (Android 7+) |
| V3 (APK) | Allows key rotation (Android 9+) |

**How attackers bypass signing:**
- Removing the signature and re-signing with their own key
- User must enable "Install from unknown sources"
- The modified app no longer gets updates from Play Store

## 4. SELinux in Android

SELinux (Security-Enhanced Linux) enforces mandatory access control. It defines what each process can and cannot do.

**Enforcing mode (default):** All restrictions are active.
**Permissive mode:** Violations are logged but not blocked.
**Disabled mode:** No SELinux protection.

SELinux policies define rules like:
- "The camera app can access the camera hardware"
- "The SMS app can send text messages"
- "A downloaded app cannot access system settings"

## 5. Android Keystore

The Android Keystore stores cryptographic keys securely. Keys in the Keystore cannot be extracted, even with root access.

**What can be stored:**
- Private keys (RSA, EC)
- Secret keys (AES)
- Certificates

**Security features:**
- Keys are hardware-backed (on devices with TEE)
- Keys can be set to require user authentication
- Keys can be bound to the device

## 6. Verified Boot

Verified Boot checks the device integrity at startup. It ensures the system has not been tampered with.

**How it works:**
```
Boot ROM --> Bootloader --> Boot Partition --> System Partition
    |            |              |                  |
    v            v              v                  v
  Verify     Verify          Verify             Verify
```

If any partition has been modified, the device warns the user or refuses to boot.

## Common Security Model Weaknesses

| Weakness | Explanation |
|----------|-------------|
| Rooted devices | Sandbox is broken, all data is accessible |
| Backup data | ADB backup can extract app data |
| Debuggable apps | Apps with debuggable flag leak data |
| Permission abuse | Apps requesting unnecessary permissions |
| Weak signing | Apps signed with debug keys |

## Summary

Android's security model has multiple layers: sandboxing, permissions, signing, SELinux, Keystore, and Verified Boot. The sandbox isolates apps from each other. Permissions control access to sensitive features. Signing ensures app integrity. SELinux enforces system-wide policies. The Keystore protects cryptographic keys. Verified Boot ensures the system has not been tampered with.