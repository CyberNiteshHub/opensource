# Module 06: Reversing App with MobSF - Features and Capabilities

## Complete Feature Overview

MobSF has many features that make it a powerful mobile security testing tool. This lesson covers all major features and capabilities.

## 1. Code Analysis Capabilities

### Finding Hardcoded Secrets

MobSF searches the code for:

**Passwords and Credentials:**
- Hardcoded passwords in strings
- Database credentials
- Server login credentials

**API Keys and Tokens:**
- Third-party API keys (Google, Facebook, AWS)
- Authentication tokens
- Session tokens

**Encryption Keys:**
- AES/RSA private keys
- Initialization vectors (IV)
- Salt values

**How MobSF detects them:**
It uses regex patterns to find common key formats:
```
api_key\s*=\s*["'][A-Za-z0-9_-]{20,}["']
password\s*=\s*["'][^"']+["']
secret\s*=\s*["'][^"']+["']
```

### Finding Insecure API Usage

MobSF checks for:

| API | Risk |
|-----|------|
| WebView.setJavaScriptEnabled(true) | XSS attacks |
| WebView.setAllowFileAccess(true) | File theft |
| HttpURLConnection (HTTP) | Cleartext traffic |
| TrustManager (accept all) | MITM attacks |
| SQLiteDatabase.rawQuery() | SQL injection |
| Runtime.exec() | Command injection |
| DexClassLoader | Dynamic code loading |
| getSharedPreferences() | Insecure data storage |

### Finding Weak Cryptography

MobSF identifies:

- **MD2, MD4, MD5** - Broken hash algorithms
- **SHA-0, SHA-1** - Weak hash algorithms
- **DES, RC2, RC4** - Broken encryption algorithms
- **ECB Mode** - Predictable encryption
- **Static IV** - Predictable initialization vectors
- **Custom encryption** - Developer-written cryptography

## 2. Manifest Analysis Capabilities

### Component Exposure

MobSF lists all exported components:

| Component | Risk |
|-----------|------|
| Exported Activity | Can be launched by any app |
| Exported Service | Can be started by any app |
| Exported Receiver | Can receive broadcasts from any app |
| Exported Provider | Can read/write data by any app |

### Permission Analysis

MobSF checks:

- **Dangerous permissions** - READ_CONTACTS, CAMERA, LOCATION
- **Signature permissions** - System-level access
- **Over-permission** - Calculator app requesting SMS access
- **Custom permissions** - Weak protection levels

### Network Configuration

MobSF analyzes the Network Security Config:

```
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">example.com</domain>
    </domain-config>
</network-security-config>
```

**Flagged as insecure if:**
- `cleartextTrafficPermitted="true"`
- `usesCleartextTraffic="true"` in manifest
- No Network Security Config defined

## 3. File Analysis Capabilities

### SQLite Database Detection

MobSF finds database files in the APK:

- Pre-loaded SQLite databases
- Database files in assets/
- Database file references in code

### Configuration File Analysis

MobSF examines:

- **JSON files** - API configs, server URLs
- **XML files** - Hidden settings
- **YAML files** - Application configuration
- **Properties files** - Key-value pairs

### Binary File Analysis

MobSF can analyze:

- **Native libraries (.so)** - Strings, function names
- **Image files** - Steganography detection (basic)
- **Certificate files** - Expired or self-signed certificates

## 4. Network Security Capabilities

### SSL/TLS Analysis

MobSF checks for:

- **Cleartext HTTP** - URLs using http:// instead of https://
- **Weak protocols** - SSLv3, TLSv1.0, TLSv1.1
- **Insecure cipher suites** - RC4, DES, NULL ciphers
- **Certificate validation issues** - TrustManager that trusts all

### SSL Pinning Detection

MobSF identifies:

- Certificate pinning implementation
- Public key pinning
- Pin validation logic
- Missing pinning in network calls

## 5. Malware Detection Capabilities

### Known Malware Signatures

MobSF checks for:

- Known malware package names
- Known malicious code patterns
- Suspicious permission combinations

### Suspicious Behavior Detection

**Red flags MobSF looks for:**

```
+----------------------------------+
| READ_SMS + INTERNET              |  SMS Stealer
| READ_CONTACTS + INTERNET         |  Contact Stealer
| RECORD_AUDIO + INTERNET          |  Audio Spy
| CAMERA + INTERNET                |  Camera Spy
| BIND_ACCESSIBILITY_SERVICE       |  Keylogger
| SYSTEM_ALERT_WINDOW              |  Overlay Attack
| INSTALL_PACKAGES                 |  App Installer
| READ_LOGS                        |  Log Reader
+----------------------------------+
```

### Code Obfuscation Detection

MobSF detects:

- ProGuard/DexGuard obfuscation
- String encryption
- Reflection-heavy code
- Native code hiding

## 6. Dynamic Analysis Capabilities

### Runtime Monitoring

During dynamic analysis, MobSF captures:

- **File operations** - Create, read, write, delete
- **Network connections** - All outgoing connections
- **Process creation** - New processes started
- **Activity launches** - Screens shown to user
- **Service starts** - Background services

### Network Capture

- **HTTP/HTTPS requests** - Full request/response capture
- **WebSocket traffic** - Real-time communication
- **DNS queries** - Server lookups
- **SSL handshake** - Certificate details

### Screenshot Automation

- **Activity screenshots** - Every screen change
- **Keyboard capture** - Input field content
- **Dialog capture** - Alert dialog content

## 7. API Testing Capabilities

### Endpoint Discovery

MobSF finds API endpoints from:

- Hardcoded URLs in code
- Network traffic capture
- Manifest file deep links
- String resources

### Fuzzing

MobSF can send unexpected data to:

- API endpoints
- Query parameters
- Request bodies
- HTTP headers

## Feature Comparison Table

| Feature | Android | iOS |
|---------|---------|-----|
| Static Analysis | Yes | Yes |
| Dynamic Analysis | Yes | No |
| Code Analysis | Yes | Yes |
| Manifest Analysis | Yes | Yes (Info.plist) |
| Network Analysis | Yes | Yes |
| Malware Detection | Yes | Limited |
| API Testing | Yes | No |
| Report Generation | Yes | Yes |
| Screenshots | Yes | No |

## Summary

MobSF has extensive features for mobile security testing. It analyzes code, manifest, resources, network configuration, and behavior. It detects hardcoded secrets, weak cryptography, insecure APIs, and malware. It supports both static and dynamic analysis. Use MobSF for comprehensive automated security testing.