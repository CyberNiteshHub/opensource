# Module 21: Dynamic Analysis - Introduction

## What is Dynamic Analysis?

Dynamic analysis means testing the app while it is running. Instead of just reading the code, you observe and interact with the app's behavior.

## Static vs Dynamic Analysis

| Aspect | Static Analysis | Dynamic Analysis |
|--------|----------------|-----------------|
| App running? | No | Yes |
| What is analyzed | Code, resources | Behavior, runtime data |
| Finds | Code issues, hardcoded secrets | Runtime issues, network behavior |
| Tools | MobSF, jadx, apktool | Frida, Drozer, Burp Suite |

## Why Dynamic Analysis is Important

1. **Finds runtime vulnerabilities** - Issues that only appear when the app runs
2. **Tests authentication** - How the app handles login at runtime
3. **Observes network behavior** - What data is sent and received
4. **Tests security controls** - Root detection, SSL pinning at runtime
5. **Finds logic flaws** - Business logic issues are visible during runtime

## Types of Dynamic Analysis

| Type | Description |
|------|-------------|
| Runtime analysis | Observe app behavior |
| Network analysis | Capture and analyze traffic |
| Memory analysis | Dump and analyze app memory |
| File system analysis | Monitor file changes |
| Debugging | Step through code execution |

## Summary

Dynamic analysis tests the app while it is running. It finds runtime vulnerabilities, tests authentication, observes network behavior, and tests security controls. Both static and dynamic analysis are needed for complete testing.