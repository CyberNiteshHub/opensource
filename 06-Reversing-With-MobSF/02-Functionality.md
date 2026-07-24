# Module 06: Reversing App with MobSF - Functionality

## Overview of MobSF Functionality

MobSF has three main functional areas: Static Analysis, Dynamic Analysis, and API Testing. Each area checks different aspects of the app.

## 1. Static Analysis Features

Static analysis means analyzing the app without running it. MobSF reads the APK files and checks them for security problems.

### Android Manifest Analysis

MobSF examines the AndroidManifest.xml file and checks:

- **Exported Components** - Activities, services, receivers, and providers that are exported. Exported components can be accessed by other apps.
- **Backup Flag** - If android:allowBackup is true, app data can be backed up via ADB.
- **Debuggable Flag** - If the app is debuggable, it is easier to attack.
- **Permissions** - Dangerous permissions requested by the app.
- **Custom Permissions** - Permissions defined by the app itself.
- **Deep Links** - URLs that can launch the app.
- **Network Security Config** - If cleartext traffic is allowed.

### Code Analysis

MobSF searches the DEX bytecode for security issues:

- **Hardcoded Secrets** - Passwords, API keys, tokens, encryption keys in the code.
- **Weak Cryptography** - Use of MD5, SHA1, DES, RC4, or custom encryption.
- **Insecure WebView** - JavaScript enabled, file access enabled.
- **SSL/TLS Issues** - TrustManager that accepts all certificates.
- **SQL Injection** - Unsanitized database queries.
- **Dynamic Code Loading** - Loading code from external sources.
- **Logging** - Logging sensitive information.

### Resource Analysis

MobSF checks the app resources:

- **Hardcoded URLs** - API endpoints, server addresses.
- **Hardcoded Email/Phone** - Contact information exposure.
- **Database Files** - SQLite databases in assets folder.
- **Configuration Files** - JSON, XML config files with secrets.
- **Binary Files** - Hidden data in resource files.

### Permission Analysis

MobSF checks if the app uses:

- **Over-permissive permissions** - More permissions than needed.
- **Dangerous permission combinations** - SMS + Internet (SMS theft).
- **Custom permissions** - Weak custom permission definitions.

### Network Security Analysis

MobSF checks:

- **Cleartext traffic** - HTTP instead of HTTPS.
- **SSL Pinning** - If pinning is implemented.
- **Certificate validation** - If certificates are properly validated.
- **Network Security Config** - domain configuration.

## 2. Dynamic Analysis Features

Dynamic analysis means running the app and watching what it does. MobSF uses an Android emulator or device for this.

### Runtime Behavior Monitoring

- **File System Changes** - What files the app creates, modifies, or deletes.
- **Process Information** - What processes the app starts.
- **Activity Monitoring** - What activities are launched.
- **Service Monitoring** - What services are started.

### Network Traffic Capture

MobSF captures all network traffic from the app:

- **HTTP/HTTPS Requests** - All API calls.
- **Request Parameters** - What data is sent.
- **Response Data** - What data is received.
- **Headers** - Authentication tokens, cookies.
- **Non-HTTP Traffic** - WebSocket, custom protocols.

### Screenshot Capture

MobSF takes screenshots of the app during testing:

- **On Launch** - What the app shows when it starts.
- **On Login** - Login screen capture.
- **On Error** - Error messages that might leak information.

### Logcat Analysis

MobSF collects Android logcat output and checks for:

- **Sensitive Data in Logs** - Passwords, tokens written to log.
- **Error Messages** - Stack traces that reveal code structure.
- **Debug Output** - Debug information left in production.

## 3. API Testing Features

MobSF can test the APIs that the app communicates with:

- **Endpoint Discovery** - Finding all API endpoints from the app.
- **Fuzzing** - Sending unexpected data to APIs.
- **Authentication Testing** - Testing for weak auth.
- **Authorization Testing** - Testing for access control issues.

## Report Generation

After analysis, MobSF generates comprehensive reports:

### Report Sections

| Section | Content |
|---------|---------|
| App Information | Package name, version, platform |
| Summary | Overall security score, risk count |
| Manifest Analysis | Component exposure, permissions |
| Code Analysis | Hardcoded secrets, insecure code |
| Network Analysis | Traffic issues, SSL problems |
| Storage Analysis | Data storage vulnerabilities |
| Dynamic Analysis | Runtime behavior, network capture |
| API Analysis | Endpoint issues |
| Screenshots | App screenshots during testing |
| Malware Analysis | Malware detection results |

### Report Formats

- **HTML Report** - View in browser, interactive.
- **PDF Report** - For printing and sharing.
- **JSON Report** - For integration with other tools.

## MobSF Scoring System

MobSF assigns severity levels to findings:

| Severity | Description |
|----------|-------------|
| Critical | Immediate risk, must fix |
| High | Significant risk, should fix |
| Medium | Moderate risk, consider fixing |
| Low | Minor risk, nice to fix |
| Info | Informational, no direct risk |

## Summary

MobSF functionality covers static analysis, dynamic analysis, and API testing. Static analysis checks code and resources without running the app. Dynamic analysis runs the app and monitors behavior. API testing checks backend endpoints. All findings are compiled into a detailed report with severity ratings.