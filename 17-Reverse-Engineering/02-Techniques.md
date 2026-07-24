# Module 17: Reverse Engineering - Techniques

## Overview

Reverse engineering techniques help you understand how an app works. This lesson covers the main techniques used in mobile reverse engineering.

## Static Analysis

Static analysis means studying the app without running it.

### Decompilation

Converting binary code back to readable code.

**DEX to Java (jadx):**
```
jadx app.apk
```
Produces Java code that is close to the original source.

**DEX to Smali (apktool):**
```
apktool d app.apk
```
Produces smali assembly code that can be modified and reassembled.

### Code Review

Reading the decompiled code to understand app logic.

**What to look for:**
- Hardcoded secrets
- Authentication logic
- Encryption algorithms
- API endpoints
- Backdoors

## Dynamic Analysis

Dynamic analysis means running the app and observing its behavior.

### Runtime Monitoring

Watch what the app does while running.

**Tools:** Frida, Xposed, Objection

**What to monitor:**
- Function calls
- Network requests
- File operations
- Database queries

### Network Traffic Analysis

Intercept and analyze network communication.

**Tools:** Burp Suite, Wireshark, mitmproxy

**What to check:**
- API endpoints
- Request/response data
- Authentication tokens
- Encryption

### Memory Analysis

Dump and analyze app memory.

**Tools:** Frida, Fridump

**What to find:**
- Decrypted data in memory
- Authentication tokens
- Encryption keys
- Decompressed code

## Debugging

Step through the code to understand execution flow.

**Tools:**
- Android Studio Debugger
- jdb (Java Debugger)
- smalidea (Smali debugging plugin)

**What debugging reveals:**
- Variable values at runtime
- Code paths taken
- Conditional logic outcomes
- Exception handling

## Binary Analysis

Analyzing native libraries (.so files).

**Tools:** Ghidra, IDA Pro, radare2

**What to look for:**
- Hardcoded strings in native code
- Custom encryption implementations
- Anti-debugging techniques
- Obfuscated code

## Summary

Reverse engineering techniques include static analysis (decompilation, code review), dynamic analysis (runtime monitoring, network traffic, memory analysis), debugging, and binary analysis. Each technique reveals different aspects of the app's behavior.