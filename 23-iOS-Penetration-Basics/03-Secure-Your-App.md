# Module 23: iOS Penetration Basics - How to Secure Your App

## Overview

This lesson covers iOS-specific security best practices for developers.

## 1. Secure Data Storage

### Use Keychain for Sensitive Data

```swift
// Store in Keychain
let query: [String: Any] = [
    kSecClass as String: kSecClassGenericPassword,
    kSecAttrAccount as String: "userToken",
    kSecValueData as String: token.data(using: .utf8)!
]
SecItemAdd(query as CFDictionary, nil)
```

### Do Not Use UserDefaults for Secrets

```swift
// BAD - plain text
UserDefaults.standard.set(password, forKey: "userPassword")

// GOOD - use Keychain
```

## 2. Network Security

### App Transport Security (ATS)

ATS enforces HTTPS connections.

```xml
<!-- Info.plist - Do not disable ATS globally -->
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <false/>
</dict>
```

## 3. Authentication and Authorization

- Use Face ID / Touch ID with LocalAuthentication
- Store auth tokens in Keychain
- Implement proper session management

## 4. Code Protection

- Obfuscate code (although limited on iOS)
- Do not include debug symbols in release
- Use compiler optimizations

## 5. Regular Security Testing

- Test on jailbroken devices
- Use Frida and Objection for runtime testing
- Conduct regular penetration tests

## Summary

Secure iOS apps by storing sensitive data in Keychain, not disabling ATS, using biometric authentication properly, protecting code, and conducting regular security testing.