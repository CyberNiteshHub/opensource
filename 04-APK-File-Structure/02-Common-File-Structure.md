# Module 04: APK File Structure - Common File Structure Patterns

## Standard APK Layout

Most APK files follow a standard layout. Once you understand this layout, you can easily find important files in any APK.

## Full APK Tree Structure

```
app.apk
├── AndroidManifest.xml          # Must have: App configuration
├── classes.dex                  # Must have: App code
├── classes2.dex                 # Optional: More code (multi-dex)
├── classes3.dex                 # Optional: Even more code
├── resources.arsc               # Must have: Compiled resources
│
├── META-INF/                    # Signature and manifest
│   ├── MANIFEST.MF
│   ├── CERT.RSA
│   └── CERT.SF
│
├── lib/                         # Native code
│   ├── armeabi-v7a/
│   │   ├── libnative.so
│   │   └── libcrypto.so
│   ├── arm64-v8a/
│   │   ├── libnative.so
│   │   └── libcrypto.so
│   └── x86/
│       └── libnative.so
│
├── res/                         # Resources
│   ├── layout/
│   │   ├── activity_main.xml
│   │   └── fragment_home.xml
│   ├── drawable/
│   │   ├── ic_launcher.png
│   │   └── background.png
│   ├── drawable-hdpi/
│   ├── drawable-mdpi/
│   ├── drawable-xhdpi/
│   ├── drawable-xxhdpi/
│   ├── drawable-xxxhdpi/
│   ├── values/
│   │   ├── strings.xml
│   │   ├── colors.xml
│   │   └── themes.xml
│   └── mipmap/
│       └── ic_launcher.png
│
├── assets/                      # Raw assets
│   ├── database.db
│   ├── config.json
│   └── certificates/
│
├── kotlin/                      # Kotlin metadata
│   └── kotlin.kotlin_builtins
│
└── unknown/                     # Unrecognized files
```

## Understanding Each Part

### AndroidManifest.xml

**Location:** Root of APK
**Format:** Binary XML (not human-readable without decoding)

**How to read it:**
```
apktool d app.apk
cat app/AndroidManifest.xml
```

Or use aapt:
```
aapt dump badging app.apk
aapt dump permissions app.apk
```

**What to check first:**
1. `android:exported="true"` on components
2. Permissions requested
3. `android:debuggable="true"`
4. `android:allowBackup="true"`
5. Deep link URLs

### classes.dex

**Location:** Root of APK
**Format:** Dalvik bytecode

**How to read it:**
```
jadx app.apk          # Decompile to Java
apktool d app.apk      # Decode to smali
d2j-dex2jar classes.dex  # Convert to JAR
```

**Multi-dex:** Large apps have multiple dex files:
- classes.dex (main)
- classes2.dex (secondary)
- classes3.dex (tertiary)
- etc.

### resources.arsc

**Location:** Root of APK
**Format:** Binary resource table

**How to read it:**
Use apktool to decode it to readable XML files.

### META-INF Folder

**Location:** Root of APK/META-INF/
**Format:** Text and binary

**Key files:**
- MANIFEST.MF - Shows all files and their hashes
- CERT.RSA - Contains the certificate
- CERT.SF - Signed version of manifest

**How to check signature:**
```
keytool -printcert -jarfile app.apk
jarsigner -verify -verbose app.apk
```

## Common Variations

### Pattern 1: Simple App

```
app.apk
├── AndroidManifest.xml
├── classes.dex
├── resources.arsc
├── META-INF/
├── res/
└── assets/ (optional)
```

**Example:** A simple calculator app.

### Pattern 2: App with Native Libraries

```
app.apk
├── AndroidManifest.xml
├── classes.dex
├── resources.arsc
├── META-INF/
├── lib/
│   ├── armeabi-v7a/libgameengine.so
│   └── arm64-v8a/libgameengine.so
├── res/
└── assets/
```

**Example:** A game app using Unity or Unreal Engine.

### Pattern 3: App with Multiple DEX Files

```
app.apk
├── AndroidManifest.xml
├── classes.dex
├── classes2.dex
├── classes3.dex
├── resources.arsc
├── META-INF/
├── res/
└── assets/
```

**Example:** A large enterprise app or social media app.

### Pattern 4: App with Assets

```
app.apk
├── AndroidManifest.xml
├── classes.dex
├── resources.arsc
├── META-INF/
├── res/
└── assets/
    ├── databases/
    │   └── app_data.db
    ├── html/
    │   └── help.html
    └── encryption_keys/
        └── key.bin
```

**Example:** An app that ships with a pre-loaded database.

## How to Extract an APK

### From an Installed App (using ADB)

```
adb shell pm list packages           # List all installed apps
adb shell pm path com.example.app    # Get APK path
adb pull /data/app/com.example.app/base.apk
```

### From a Device Backup

```
adb backup -f backup.ab -noapk com.example.app
```

## Security Implications by Component

| APK Component | What to Check |
|---------------|---------------|
| AndroidManifest.xml | Exported components, permissions, backup flag |
| classes.dex | Hardcoded secrets, weak crypto, debug code |
| resources.arsc | Hidden strings, API endpoints |
| lib/ | Native exploits, hardcoded keys |
| res/ | Developer comments, hidden features |
| assets/ | Embedded databases, config files |
| META-INF | Signature type, debug key usage |

## Summary

The APK file structure is consistent across most apps. The root contains AndroidManifest.xml, classes.dex, resources.arsc, and META-INF. The res/ folder contains resources. The lib/ folder contains native code. The assets/ folder contains raw files. Understanding this structure helps you quickly find the relevant files when testing an app.