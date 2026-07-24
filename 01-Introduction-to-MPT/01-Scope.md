# Module 01: Introduction to Mobile Penetration Testing - Scope

## What is Mobile Penetration Testing?

Mobile Penetration Testing (MPT) is the process of testing a mobile app to find security problems. Think of it like hiring a security expert to check your house. The expert tries to break in, find weak points, and tell you how to fix them. In MPT, the expert is a penetration tester. The "house" is a mobile app running on Android or iOS.

Mobile apps store and send a lot of sensitive data. They handle passwords, credit card numbers, personal messages, and location data. If a hacker breaks into the app, they can steal all this information. MPT helps find these risks before hackers do.

## Why is MPT Important?

There are many reasons why mobile penetration testing is important:

- **Data Protection** - Mobile apps store personal data. If the app is not secure, hackers can steal this data.
- **Financial Security** - Many apps handle payments. A security flaw can lead to money theft.
- **User Trust** - Users stop using apps that are not safe. Good security builds trust.
- **Compliance** - Laws like GDPR and HIPAA require apps to be secure. Companies must test their apps.
- **Reputation** - A security breach can damage a company's reputation badly.

## Scope of Mobile Penetration Testing

The scope means what is included in the test. Not everything is tested every time. The scope is decided before testing starts.

### What is Included in MPT Scope?

| Area | What We Test |
|------|-------------|
| Android App | The APK file, its code, its resources |
| iOS App | The IPA file, its code, its configuration |
| API Endpoints | How the app talks to the server |
| Backend | Server-side logic and databases |
| Network Traffic | Data sent between app and server |
| Local Storage | Data stored on the device |
| Authentication | Login, sessions, tokens |
| Authorization | What users can and cannot do |

### What is Usually Out of Scope?

- Physical security of the device
- Social engineering attacks
- Denial of Service (DoS) attacks on servers
- Third-party services (unless agreed)

## Types of Mobile Apps

There are three main types of mobile apps. Each type has different security considerations.

### 1. Native Apps

Native apps are built specifically for one platform. Android apps use Java or Kotlin. iOS apps use Swift or Objective-C.

**Security concerns:**
- Code can be reverse engineered
- Local storage can be accessed on rooted/jailbroken devices
- API keys in code can be extracted

### 2. Web Apps (Mobile Web)

These are websites that run in the phone browser. They are not installed on the device.

**Security concerns:**
- Same as web application security (XSS, CSRF, SQLi)
- Browser security depends on the device browser
- No access to device hardware

### 3. Hybrid Apps

Hybrid apps are a mix of native and web. They run inside a WebView. Examples: apps built with Ionic, Cordova, React Native.

**Security concerns:**
- Both native and web vulnerabilities apply
- JavaScript bridge can be exploited
- WebView misconfigurations are common

## What Gets Tested in MPT?

### Android Apps

- APK file analysis
- Manifest file review
- Code decompilation
- Local storage inspection
- Network traffic interception
- Runtime analysis

### iOS Apps

- IPA file analysis
- Info.plist review
- Binary analysis
- Keychain inspection
- Network traffic interception
- Runtime analysis with Frida

### APIs and Backend

- Authentication testing
- Authorization testing
- Input validation
- Rate limiting
- Session management
- Data exposure

## Who Needs MPT?

| Role | Why They Need MPT |
|------|------------------|
| App Developers | To find bugs before release |
| Companies | To protect user data and reputation |
| Banks | To secure financial transactions |
| Healthcare Apps | To comply with HIPAA regulations |
| E-commerce | To protect payment information |
| Government | To protect citizen data |

## Summary

The scope of mobile penetration testing covers the app, its backend, and the network communication. It includes both Android and iOS platforms. Testing finds security problems so they can be fixed before hackers find them. A clear scope helps both the tester and the client know exactly what will be tested.