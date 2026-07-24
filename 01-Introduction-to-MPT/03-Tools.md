# Module 01: Introduction to MPT - Tools

## Why Do We Need Tools?

Mobile penetration testing requires many tools. No single tool can do everything. Each tool has a specific purpose. Some tools help you read the code. Some tools help you watch network traffic. Some tools help you modify the app behavior at runtime. Knowing which tool to use and when is an important skill.

## Tool Categories

We can group tools into categories based on what they do:

```
Mobile Pentesting Tools
     |
     +-- Static Analysis Tools (Analyze code without running)
     |       - MobSF, jadx, APKTool, QARK
     |
     +-- Dynamic Analysis Tools (Analyze app while running)
     |       - Frida, Xposed, Drozer
     |
     +-- Network Tools (Intercept and analyze traffic)
     |       - Burp Suite, mitmproxy, Wireshark
     |
     +-- Reverse Engineering Tools (Decompile and debug)
     |       - Ghidra, IDA Pro, Bytecode Viewer
     |
     +-- Utility Tools (Support tools)
             - ADB, Android Studio, Genymotion
```

## Static Analysis Tools

### MobSF (Mobile Security Framework)

MobSF is an all-in-one tool. It can analyze both Android and iOS apps. It checks the code, the manifest, and the resources. It gives you a report with all vulnerabilities found.

**When to use:** When you want a quick and complete analysis of an app.

### jadx

jadx converts DEX files (Android bytecode) into readable Java code. It is one of the best decompilers for Android.

**When to use:** When you want to read the app source code in Java format.

### APKTool

APKTool decodes APK files into smali code and resources. Smali is a human-readable version of the DEX bytecode. You can modify the smali code and rebuild the APK.

**When to use:** When you want to modify the app and rebuild it.

### QARK (Quick Android Review Kit)

QARK is a static analysis tool from LinkedIn. It finds security vulnerabilities in Android apps.

**When to use:** When you want automated vulnerability detection.

## Dynamic Analysis Tools

### Frida

Frida lets you inject JavaScript code into running apps. You can modify app behavior, bypass security checks, and monitor function calls - all without modifying the APK.

**When to use:** When you want to bypass SSL pinning, bypass root detection, or trace function calls at runtime.

### Xposed Framework

Xposed lets you write modules that modify app behavior. Unlike Frida, it requires the device to be rooted and the Xposed framework installed.

**When to use:** When you want persistent modifications to app behavior.

### Drozer

Drozer is a security testing tool for Android. It helps you find and exploit vulnerabilities in Android apps. It works by installing a small agent on the device and connecting to it from your computer.

**When to use:** When you want to test exported components (activities, services, content providers).

## Network Tools

### Burp Suite

Burp Suite is a proxy that sits between your phone and the internet. It captures all traffic sent and received by the app. You can see the data, modify it, and replay requests.

**When to use:** For almost every test. It is the most important tool for mobile testing.

### mitmproxy

mitmproxy is similar to Burp Suite but is open-source and scriptable. You can write Python scripts to automatically modify traffic.

**When to use:** When you need to automate traffic modification.

### Wireshark

Wireshark captures all network packets. It can analyze many protocols. It is useful for low-level network analysis.

**When to use:** When you need to analyze raw network traffic or non-HTTP protocols.

## Reverse Engineering Tools

### Ghidra

Ghidra is a reverse engineering tool from the NSA. It is used for analyzing native code (C/C++ libraries).

**When to use:** When you need to analyze native libraries (.so files) in Android apps.

### IDA Pro

IDA Pro is a professional disassembler and debugger. It is very powerful but expensive.

**When to use:** For advanced reverse engineering of native code.

### Bytecode Viewer

Bytecode Viewer is a multi-tool. It combines multiple decompilers and disassemblers in one interface.

**When to use:** When you want to try multiple decompilers on the same file.

## Utility Tools

| Tool | Purpose |
|------|---------|
| ADB (Android Debug Bridge) | Connect to Android devices, install apps, run commands |
| Android Studio | Create emulators, debug apps |
| Genymotion | Fast Android emulator for testing |
| Objection | Runtime exploration of iOS/Android apps |
| APK Signer | Sign modified APK files |

## Summary

Each tool has a purpose. Burp Suite is essential for network testing. MobSF is great for quick analysis. Frida is powerful for runtime modification. ADB is needed for device interaction. Learn to use multiple tools together for the best results. No single tool can do everything.