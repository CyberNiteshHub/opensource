# Module 16: Code Tampering - Objective

## What is Code Tampering?

Code Tampering is the number eight risk in the OWASP Mobile Top 10 (M8). It means modifying the app's code or resources after it has been released. Attackers tamper with apps to change their behavior.

## OWASP Mobile Top 10 - M8

M8 covers code tampering:
- Modifying app code (smali injection)
- Repackaging apps
- Bypassing license checks
- Removing ads
- Injecting malware into legitimate apps
- Modifying app resources

## Why Attackers Tamper with Mobile Apps

### 1. Removing License Checks

Paid apps check if the user has purchased the app. Attackers remove this check.

**Objective:** Use the app without paying.

**How:**
```smali
# Original - returns 0 (not licensed)
const/4 v0, 0x0
return v0

# Modified - returns 1 (licensed)
const/4 v0, 0x1
return v0
```

### 2. Bypassing Payment Verification

In-app purchases are verified with the app store. Attackers bypass this verification.

**Objective:** Get premium features or virtual goods for free.

### 3. Removing Advertisements

Free apps show ads to generate revenue. Attackers remove the ads.

**Objective:** Use the app without seeing ads.

### 4. Injecting Malware

Attackers take a legitimate app and add malicious code to it.

**Objective:** Distribute malware that looks like a real app.

**How:**
1. Download the original APK
2. Decode with Apktool
3. Add malicious smali code
4. Rebuild and sign with a different key
5. Distribute the modified APK

### 5. Modifying App Behavior

Attackers change how the app works.

**Examples:**
- Change the app's colors and branding (phishing)
- Modify network requests to send data to a different server
- Remove security checks (root detection, SSL pinning)
- Add new features (cheats in games)

### 6. Bypassing Security Controls

Apps implement security controls to protect themselves. Attackers bypass them.

**Common targets:**
- Root detection (app refuses to run on rooted devices)
- SSL pinning (app verifies server certificate)
- Emulator detection (app refuses to run in emulator)
- Debug detection (app detects debugger)

## The Tampering Process

```
Original APK
     |
     v
Decode with Apktool (apktool d app.apk)
     |
     v
Modify the Code
  - Edit smali files
  - Modify resources
  - Change AndroidManifest.xml
     |
     v
Rebuild with Apktool (apktool b app_folder)
     |
     v
Sign with Custom Key (apksigner sign)
     |
     v
Modified APK (Installed like any app)
```

## What Makes Tampering Possible

### 1. APK Structure is Open

APK files are ZIP files. Anyone can open them.

### 2. Code Can Be Decompiled

DEX bytecode can be converted to smali (readable) and modified.

### 3. No Integrity Protection

Android does not verify the app's integrity at runtime by default. A modified app runs normally.

### 4. Side-Loading is Allowed

Users can install apps from outside the app store. This allows tampered apps to be installed.

## Legitimate Uses of Code Tampering

Code tampering is not always malicious. Security researchers use it for:
- Testing app security
- Finding vulnerabilities
- Understanding malware
- Educational purposes

## Summary

Code Tampering (M8) means modifying an app after release. Attackers tamper with apps to remove license checks, bypass payments, remove ads, inject malware, and modify behavior. The tampering process involves decoding, modifying, rebuilding, and signing the APK. Understanding how tampering works helps you protect your app.