# Module 23: iOS Penetration Basics - Introduction to iOS Penetration Testing

## What is iOS Penetration Testing?

iOS penetration testing is similar to Android testing but with some differences due to iOS's different security model. iOS is generally more locked down than Android.

## iOS vs Android Security Differences

| Aspect | Android | iOS |
|--------|---------|-----|
| App installation | Side-loading allowed | App Store only (mostly) |
| Root/Jailbreak | Easy to root | Harder to jailbreak |
| Sandbox | Linux-based | XNU-based |
| Code signing | Optional | Required |
| App review | Minimal | Strict review process |
| File system access | Easy with root | Difficult even with jailbreak |

## iOS Security Model

1. **Sandbox** - Each app runs in its own sandbox
2. **Code Signing** - All apps must be signed by Apple
3. **App Store Review** - Apps are reviewed before publication
4. **Data Protection** - File-level encryption
5. **Entitlements** - Permissions are granted through entitlements

## Testing iOS Apps

### Jailbroken Device Testing

A jailbroken device gives more access for testing.

**Tools on jailbroken device:**
- Cydia (package manager)
- Frida
- OpenSSH

### Non-Jailbroken Testing

Limited but possible with:
- Objection
- Frida (with limitations)
- Burp Suite for network

## Tools for iOS Testing

| Tool | Purpose |
|------|---------|
| Objection | Runtime exploration |
| Frida | Dynamic instrumentation |
| idb | iOS debugging tool |
| class-dump | Extract class info |
| Hopper | Binary disassembler |
| Burp Suite | Network interception |

## Summary

iOS penetration testing is similar to Android but with additional constraints due to iOS's stricter security model. Jailbroken devices give more access. Key tools include Objection, Frida, and Burp Suite.