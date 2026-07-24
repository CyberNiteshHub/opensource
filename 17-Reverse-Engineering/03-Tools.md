# Module 17: Reverse Engineering - Tools

## Overview

This lesson covers the tools used for reverse engineering Android and iOS apps. Each tool has a specific purpose.

## Android Reverse Engineering Tools

### 1. jadx

jadx converts DEX bytecode to readable Java code.

**Installation:**
```
sudo apt install jadx
```

**Usage:**
```
jadx app.apk                    # Decompile to Java
jadx -d output_dir app.apk      # Specify output directory
jadx-gui app.apk                # GUI version
```

**Best for:** Reading app logic in Java format.

### 2. Apktool

Apktool decodes APK resources and converts DEX to smali.

**Installation:**
```
sudo apt install apktool
```

**Usage:**
```
apktool d app.apk               # Decode APK
apktool b app_folder            # Rebuild APK
```

**Best for:** Modifying and rebuilding APKs.

### 3. Frida

Frida injects JavaScript code into running apps for dynamic analysis.

**Installation:**
```
pip install frida-tools
```

**Usage:**
```
frida -U com.example.app        # Attach to app
frida -U -l script.js app       # Run with script
```

**Best for:** Runtime manipulation and monitoring.

### 4. Objection

Objection is a runtime exploration tool built on Frida.

**Installation:**
```
pip install objection
```

**Usage:**
```
objection -g com.example.app explore
```

**Best for:** Quick runtime exploration without writing scripts.

### 5. Ghidra (NSA)

Ghidra reverses native code (.so files).

**Best for:** Analyzing native libraries.

### 6. Bytecode Viewer

A multi-tool that combines multiple decompilers.

**Best for:** Trying different decompilers on the same file.

## iOS Reverse Engineering Tools

### 1. class-dump

Extracts class information from iOS binaries.

**Usage:**
```
class-dump AppBinary
```

### 2. Hopper

A disassembler for iOS binaries.

### 3. objection (iOS)

Objection also works for iOS.

## Tool Selection Guide

| Task | Tool |
|------|------|
| Decompile to Java | jadx |
| Decode resources | Apktool |
| Modify and rebuild | Apktool |
| Runtime analysis | Frida |
| Quick exploration | Objection |
| Native code analysis | Ghidra, IDA Pro |
| iOS binary analysis | class-dump, Hopper |
| Network analysis | Burp Suite |
| Traffic capture | Wireshark |

## Summary

Different reverse engineering tasks require different tools. jadx for decompilation to Java, Apktool for resource decoding and rebuilding, Frida for runtime analysis, Objection for quick exploration, and Ghidra for native code analysis.