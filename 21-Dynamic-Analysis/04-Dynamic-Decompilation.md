# Module 21: Dynamic Analysis - Dynamic Decompilation

## Overview

Dynamic decompilation means extracting code from a running app. This is useful when the app uses runtime code loading or obfuscation.

## Why Dynamic Decompilation is Needed

Some apps protect their code through:
- Runtime code loading (DexClassLoader)
- Code encryption (decrypted at runtime)
- Obfuscation (meaningless names)
- Packer/protector tools

Static analysis cannot see the code until it is decrypted and loaded at runtime.

## Tools for Dynamic Decompilation

### Frida-Dump

Dumps DEX files from memory using Frida.

**Usage:**
```
frida -U -l dump.js com.example.app
```

**What it captures:**
- All loaded DEX files
- Decrypted DEX files
- Dynamically loaded classes

### Objection

Has built-in memory dumping:

```
objection -g com.example.app explore
android heap print
```

### DumpDex

A tool specifically for dumping DEX from memory.

## How It Works

```
1. App starts and decrypts code in memory
2. Frida hooks the DEX loading process
3. The decrypted DEX is dumped from memory
4. The dumped DEX is saved to a file
5. jadx can decompile the dumped DEX
```

## When to Use Dynamic Decompilation

- The app crashes when you try static analysis
- The code has meaningless class names
- Strings appear encrypted in the APK
- The app uses packers (like Tencent, Baidu, 360)
- The app loads code from remote servers

## Summary

Dynamic decompilation extracts code from a running app's memory. Use Frida, Objection, or DumpDex to dump DEX files from memory. This is essential for analyzing obfuscated, packed, or runtime-loaded code that static analysis cannot handle.