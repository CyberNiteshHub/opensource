# Module 16: Code Tampering - Detection and Prevention

## Overview

This lesson covers how to detect if your app has been tampered with and how to prevent tampering.

## Detection Mechanisms

### 1. Signature Verification

The app checks its own signature at runtime.

```java
public boolean isSignatureValid() {
    try {
        PackageInfo packageInfo = getPackageManager()
            .getPackageInfo(getPackageName(),
                PackageManager.GET_SIGNATURES);
        byte[] signature = packageInfo.signatures[0].toByteArray();

        // Compare with known signature
        byte[] expectedSignature = getExpectedSignature();
        return Arrays.equals(signature, expectedSignature);
    } catch (Exception e) {
        return false;
    }
}
```

**How it detects tampering:**
When the app is repackaged, it is signed with a different key. The signature check fails.

### 2. Integrity Checks

The app calculates checksums of its files and compares them with expected values.

```java
public boolean isIntegrityValid() {
    try {
        // Calculate checksum of classes.dex
        MessageDigest md = MessageDigest.getInstance("SHA-256");
        FileInputStream fis = new FileInputStream(
            getApplicationInfo().sourceDir);
        byte[] buffer = new byte[8192];
        int read;
        while ((read = fis.read(buffer)) != -1) {
            md.update(buffer, 0, read);
        }
        byte[] currentHash = md.digest();

        // Compare with expected hash
        byte[] expectedHash = getExpectedHash();
        return Arrays.equals(currentHash, expectedHash);
    } catch (Exception e) {
        return false;
    }
}
```

### 3. Root/Jailbreak Detection

Tampering often happens on rooted devices.

```java
public boolean isDeviceRooted() {
    // Check for common root binaries
    String[] rootPaths = {
        "/system/app/Superuser.apk",
        "/sbin/su",
        "/system/bin/su",
        "/system/xbin/su",
        "/data/local/xbin/su",
        "/data/local/bin/su",
        "/system/sd/xbin/su",
        "/system/bin/failsafe/su",
        "/data/local/su"
    };

    for (String path : rootPaths) {
        if (new File(path).exists()) return true;
    }
    return false;
}
```

### 4. Emulator Detection

Tampering often involves testing in emulators.

```java
public boolean isEmulator() {
    return Build.FINGERPRINT.startsWith("generic")
        || Build.FINGERPRINT.startsWith("unknown")
        || Build.MODEL.contains("google_sdk")
        || Build.MODEL.contains("Emulator")
        || Build.MODEL.contains("Android SDK")
        || Build.MANUFACTURER.contains("Genymotion");
}
```

### 5. Debug Detection

The app checks if it is being debugged.

```java
public boolean isDebuggable() {
    return (getApplicationInfo().flags &
        ApplicationInfo.FLAG_DEBUGGABLE) != 0;
}

public boolean isDebuggerConnected() {
    return Debug.isDebuggerConnected();
}
```

## Prevention Mechanisms

### 1. Code Obfuscation

Make the code harder to understand and modify.

**ProGuard (built into Android):**
```groovy
android {
    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt')
        }
    }
}
```

**DexGuard (commercial):**
- Stronger obfuscation
- String encryption
- Class encryption
- Tamper detection built-in

### 2. Google Play Integrity API

Formerly SafetyNet. Verifies the device's integrity.

```java
public void checkIntegrity() {
    IntegrityManager integrityManager = IntegrityManagerFactory.create(this);

    IntegrityTokenRequest request = IntegrityTokenRequest.builder()
        .setCloudProjectNumber(projectNumber)
        .build();

    integrityManager.requestIntegrityToken(request)
        .addOnSuccessListener(response -> {
            String token = response.getToken();
            // Send token to server for verification
            verifyTokenOnServer(token);
        });
}
```

**What it checks:**
- Device is not rooted
- Bootloader is locked
- System is not tampered with
- App is from Play Store

### 3. Server-Side Verification

The server verifies the app's integrity.

```java
// Server-side (pseudo-code)
public boolean verifyAppIntegrity(String token, String packageName) {
    // Verify Play Integrity token
    if (!playIntegrityService.verify(token)) {
        return false;
    }

    // Verify package name matches
    if (!expectedPackageName.equals(packageName)) {
        return false;
    }

    return true;
}
```

### 4. Anti-Tampering Libraries

Third-party libraries that implement tamper detection.

| Library | Features |
|---------|----------|
| DexGuard | Obfuscation + tamper detection |
| Promon Shield | Runtime protection |
| Arxan | Code hardening |
| WhiteCryption | Code protection |

## Prevention Checklist

```
[ ] Signature verification at runtime
[ ] Integrity checks on critical files
[ ] Root/jailbreak detection
[ ] Emulator detection
[ ] Debug detection
[ ] Code obfuscation (ProGuard/R8)
[ ] String encryption
[ ] Play Integrity API integration
[ ] Server-side integrity verification
[ ] Anti-tampering library (if needed)
```

## Summary

Detect tampering through signature verification, integrity checks, root detection, emulator detection, and debug detection. Prevent tampering through code obfuscation, Play Integrity API, server-side verification, and anti-tampering libraries. Multiple layers of protection are more effective than a single mechanism.