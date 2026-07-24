# Module 11: Insecure Communication - Tools and Resources

## Overview

This lesson covers tools to test for insecure communication vulnerabilities and resources for learning about secure communication.

## Tools for Testing Communication Security

### 1. Burp Suite

Burp Suite is the primary tool for testing network communication.

**What it does:**
- Intercepts HTTP/HTTPS traffic
- Allows modifying requests and responses
- Replays requests
- Scans for vulnerabilities
- Decrypts HTTPS (with CA certificate)

**How to use for communication testing:**
```
1. Set up Burp proxy
2. Install Burp CA certificate on test device
3. Route app traffic through Burp
4. Check all traffic for:
   - HTTP URLs (unencrypted)
   - Weak TLS versions
   - Missing headers
   - Sensitive data in requests
```

### 2. MITMProxy

mitmproxy is an open-source alternative to Burp Suite.

**Features:**
- Intercept and modify traffic
- Scriptable with Python
- Supports HTTP/2
- Web-based interface (mitmweb)

**Commands:**
```
mitmproxy -p 8080           # Interactive mode
mitmweb -p 8080              # Web interface mode
mitmdump -p 8080             # Non-interactive mode
```

### 3. Wireshark

Wireshark captures all network packets, not just HTTP.

**What it does:**
- Captures raw network packets
- Analyzes many protocols
- Decrypts SSL/TLS (if keys are available)
- Shows detailed packet information

**Useful filters:**
```
http.request           # Show HTTP requests
tls.handshake.type == 1   # Show TLS client hellos
ip.addr == 192.168.1.100  # Filter by IP address
```

### 4. nmap

nmap scans servers and checks for open ports and TLS versions.

**Commands for TLS testing:**
```
nmap --script ssl-enum-ciphers -p 443 example.com
nmap --script ssl-cert -p 443 example.com
nmap --script tls-nextprotoneg -p 443 example.com
```

### 5. SSL Labs Test (Online)

A free online tool to test server TLS configuration.

**URL:** ssllabs.com/ssltest

**What it checks:**
- TLS version support
- Cipher suite strength
- Certificate validity
- Protocol vulnerabilities
- Overall grade (A+ to F)

### 6. testssl.sh

A command-line tool for testing TLS/SSL on servers.

**Installation:**
```
git clone https://github.com/drwetter/testssl.sh.git
cd testssl.sh
```

**Usage:**
```
./testssl.sh example.com:443
```

**What it checks:**
- Supported TLS versions
- Weak cipher suites
- Certificate issues
- Known vulnerabilities (Heartbleed, POODLE, etc.)

### 7. Android Studio Network Inspector

Android Studio has a built-in network inspector.

**How to use:**
```
1. Run the app in debug mode
2. Open View -> Tool Windows -> App Inspection
3. Select Network Inspector
4. See all network requests in real-time
```

### 8. Frida

Frida can bypass SSL pinning and inspect encrypted traffic.

**Script to bypass pinning:**
```javascript
Java.perform(function() {
    var TrustManager = Java.registerClass({
        name: 'javax.net.ssl.X509TrustManager'
    });
    // Override certificate checking
});
```

**Usage:**
```
frida -U -l bypass-pinning.js com.example.app
```

## Tool Comparison

| Tool | HTTP Inspection | SSL/TLS Testing | Protocol Analysis | Ease of Use |
|------|----------------|----------------|-------------------|-------------|
| Burp Suite | Yes | Yes | Limited | Easy |
| MITMProxy | Yes | Limited | Limited | Medium |
| Wireshark | Limited | Limited | Yes | Hard |
| nmap | No | Yes | No | Medium |
| SSL Labs | No | Yes | No | Easy |
| testssl.sh | No | Yes | No | Medium |
| Android Studio | Yes | No | Limited | Easy |
| Frida | No | Pinning bypass | No | Hard |

## Manual Testing Steps

### Step 1: Check for HTTP

```
1. Route all traffic through Burp Suite
2. Check the "HTTP History" tab
3. Look for "http://" URLs (not https)
4. If found, this is a vulnerability
```

### Step 2: Check Certificate Validation

```
1. Install Burp CA certificate on the device
2. Try to intercept HTTPS traffic
3. If the app works, it trusts user certificates
4. This means no SSL pinning
```

### Step 3: Check for Weak TLS

```
1. Use nmap or testssl.sh against the server
2. Check if TLS 1.0 or TLS 1.1 are supported
3. Check if weak cipher suites are supported
```

### Step 4: Check for Cleartext Traffic

```
1. In the app, perform actions that send data
2. Check Burp Suite for any HTTP (not HTTPS) requests
3. Check Network Security Config in the APK
```

### Step 5: Check for Pinning

```
1. Use a proxy with a self-signed certificate
2. If the app connects, there is no pinning
3. If the app refuses, pinning is implemented
```

## Resources

### Official Documentation

| Resource | URL |
|----------|-----|
| Android Network Security Config | developer.android.com/training/articles/security-config |
| Android SSL Best Practices | developer.android.com/training/articles/security-ssl |
| OkHttp Certificate Pinner | square.github.io/okhttp/4.x/okhttp/okhttp3/-certificate-pinner/ |
| Android Security Tips | developer.android.com/training/articles/security-tips |

### OWASP Resources

| Resource | URL |
|----------|-----|
| OWASP Mobile Top 10 (M3) | owasp.org/www-project-mobile-top-10 |
| Mobile Security Testing Guide | owasp.org/www-project-mobile-security-testing-guide |
| Transport Layer Protection | cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html |

### Testing Tools

| Tool | URL |
|------|-----|
| SSL Labs Test | ssllabs.com/ssltest |
| testssl.sh | github.com/drwetter/testssl.sh |
| MITMProxy | mitmproxy.org |
| Wireshark | wireshark.org |

## Summary

Test communication security using Burp Suite or MITMProxy for HTTP/HTTPS interception, nmap or testssl.sh for TLS configuration testing, and Wireshark for detailed protocol analysis. Use Android Studio Network Inspector for development testing. Always check for cleartext traffic, weak certificate validation, and missing pinning.