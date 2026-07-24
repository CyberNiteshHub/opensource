# Module 04: APK File Structure - Core Components

## What is an APK?

APK stands for Android Package Kit. It is the file format that Android uses to install apps. An APK is like a ZIP file. It contains all the files that the app needs to run.

When you download an app from the Play Store, you are downloading an APK. The APK contains code, resources, certificates, and configuration files.

## APK File Structure Overview

```
app.apk
  |
  +-- AndroidManifest.xml (App configuration)
  +-- classes.dex (App code in bytecode)
  +-- resources.arsc (Compiled resources)
  +-- META-INF/ (Certificates and signatures)
  |     +-- MANIFEST.MF
  |     +-- CERT.RSA
  |     +-- CERT.SF
  +-- lib/ (Native libraries)
  |     +-- armeabi-v7a/
  |     +-- arm64-v8a/
  |     +-- x86/
  |     +-- x86_64/
  +-- res/ (Resources)
  |     +-- layout/ (Screen layouts)
  |     +-- drawable/ (Images)
  |     +-- values/ (Strings, colors)
  |     +-- mipmap/ (App icons)
  +-- assets/ (Raw assets)
  +-- kotlin/ (Kotlin metadata)
```

## 1. AndroidManifest.xml

This is the most important file in the APK. It tells Android everything about the app.

**What it contains:**
- App package name (unique identifier)
- App version code and version name
- All components (activities, services, receivers, providers)
- Permissions the app requires
- Hardware and software features needed
- Minimum and target Android version
- App icon and theme

**Example manifest entries:**

```xml
<manifest package="com.example.app">
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.CAMERA"/>

    <application>
        <activity android:name=".MainActivity"
                  android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>

        <service android:name=".MyService"
                 android:exported="false"/>

        <provider android:name=".MyProvider"
                  android:authorities="com.example.app.provider"
                  android:exported="false"/>
    </application>
</manifest>
```

**What a penetration tester looks for in the manifest:**
- Exported components (should be minimized)
- Over-permissive permissions
- Debuggable flag set to true
- Backup flag set to true (allows data extraction)
- Custom permissions defined
- Content provider authorities

## 2. classes.dex

This file contains the app's compiled code. DEX stands for Dalvik Executable. It is the bytecode that Android runs.

**What a penetration tester looks for:**
- Hardcoded secrets (passwords, API keys, tokens)
- Weak encryption implementations
- Hardcoded IP addresses and URLs
- Debug code left in production
- Obfuscation quality

## 3. resources.arsc

This is a compiled resource table. It contains all the app's resources in a binary format.

**What it contains:**
- String values
- Colors
- Styles and themes
- Layout references

**What a penetration tester looks for:**
- Hardcoded strings (API endpoints, secret keys)
- Hidden URLs or IP addresses
- Debug endpoints
- Comments left by developers

## 4. META-INF/

This folder contains the app's signature and certificate information. Android uses this to verify the app's integrity.

**Files in META-INF:**

| File | Purpose |
|------|---------|
| MANIFEST.MF | List of all files with their SHA-1 hashes |
| CERT.RSA | The certificate used to sign the app |
| CERT.SF | Signature of the manifest file |

**What a penetration tester looks for:**
- The signature scheme used (V1, V2, V3)
- Whether the app is signed with a debug key
- Whether the signature can be removed

## 5. lib/

This folder contains native libraries (.so files). These are written in C or C++ and compiled for different processor architectures.

**Architecture folders:**

| Folder | Architecture |
|--------|-------------|
| armeabi-v7a | Older ARM devices |
| arm64-v8a | Modern ARM devices |
| x86 | Older Intel-based devices |
| x86_64 | Modern Intel-based devices |

**What a penetration tester looks for:**
- Native code can hide malicious behavior
- Native libraries can bypass static analysis
- Hardcoded secrets in native code

## 6. res/

This folder contains app resources like layouts, images, and strings.

**Subfolders:**

| Folder | Content |
|--------|---------|
| layout/ | XML files defining screen layouts |
| drawable/ | Images (PNG, JPG, SVG) |
| values/ | Strings, colors, dimensions |
| mipmap/ | App icons at different sizes |
| raw/ | Raw files (audio, video) |

**What a penetration tester looks for:**
- Hardcoded strings in values/strings.xml
- Hidden buttons or views in layout files
- Developer comments in XML files

## 7. assets/

This folder contains raw asset files. Unlike res/, these files are not compiled.

**What a penetration tester looks for:**
- Database files (SQLite)
- Configuration files
- Encryption keys
- Certificate files
- Hidden APKs or ZIP files

## Summary

The APK file contains everything the app needs to run. The AndroidManifest.xml is the configuration file. classes.dex contains the code. resources.arsc contains compiled resources. META-INF has the signature. lib/ has native code. res/ has resources. assets/ has raw files. A penetration tester examines each part for security issues.