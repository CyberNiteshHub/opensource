# Module 11: Insecure Communication - Missing or Misconfigured SSL/TLS

## Overview

SSL/TLS is the technology that encrypts internet communication. When it is missing or misconfigured, the communication is not secure even if HTTPS is used.

## What is SSL/TLS?

SSL (Secure Sockets Layer) and TLS (Transport Layer Security) are protocols that encrypt data between two points. TLS is the modern version of SSL.

**How TLS works:**
```
1. Client connects to server
2. Server sends its certificate
3. Client validates the certificate
4. Client and server agree on encryption keys
5. All subsequent data is encrypted
```

## Missing SSL/TLS

The app does not use SSL/TLS at all. All data is sent in plain text.

**Example:**
```java
URL url = new URL("http://api.example.com/login");
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
```

**Detection:** Look for `http://` URLs in the code.

## Misconfigured SSL/TLS

The app uses HTTPS but the configuration is wrong. This makes the encryption useless.

### 1. Trusting All Certificates

The most dangerous misconfiguration. The app accepts any certificate, including fake ones from attackers.

**Vulnerable code:**
```java
TrustManager[] trustAllCerts = new TrustManager[] {
    new X509TrustManager() {
        @Override
        public void checkClientTrusted(X509Certificate[] chain,
                                       String authType) {}
        @Override
        public void checkServerTrusted(X509Certificate[] chain,
                                       String authType) {}
        @Override
        public X509Certificate[] getAcceptedIssuers() {
            return new X509Certificate[0];
        }
    }
};

SSLContext sc = SSLContext.getInstance("TLS");
sc.init(null, trustAllCerts, new SecureRandom());
HttpsURLConnection.setDefaultSSLSocketFactory(sc.getSocketFactory());
```

**What this does:** The `checkServerTrusted` method does nothing. Any certificate is accepted.

**How to detect:**
- Search for `checkServerTrusted` in the code
- Search for `TrustManager` class
- Check for custom SSLContext initialization

### 2. Disabling Hostname Verification

The app does not check if the server's hostname matches the certificate.

**Vulnerable code:**
```java
HostnameVerifier allHostsValid = new HostnameVerifier() {
    @Override
    public boolean verify(String hostname, SSLSession session) {
        return true; // Accepts any hostname!
    }
};
HttpsURLConnection.setDefaultHostnameVerifier(allHostsValid);
```

**What this does:** An attacker can use a certificate for "evil.com" to intercept traffic for "bank.com".

### 3. Using Weak TLS Versions

Older TLS versions have known vulnerabilities.

| Version | Status | Vulnerability |
|---------|--------|---------------|
| SSL 2.0 | Broken | Multiple cryptographic weaknesses |
| SSL 3.0 | Broken | POODLE attack |
| TLS 1.0 | Weak | BEAST attack |
| TLS 1.1 | Weak | PCI DSS non-compliant |
| TLS 1.2 | Secure | Recommended |
| TLS 1.3 | Most Secure | Latest standard |

**Vulnerable code:**
```java
SSLContext sc = SSLContext.getInstance("TLSv1.1"); // Too old!
```

### 4. Using Weak Cipher Suites

Cipher suites are the encryption algorithms used by TLS. Some are weak.

**Weak cipher suites:**
- RC4 (broken)
- DES (too weak)
- 3DES (slow, weak)
- CBC mode ciphers (vulnerable to padding oracle attacks)
- NULL cipher (no encryption!)
- Export-grade ciphers (deliberately weakened)

**Example of weak configuration:**
```java
sc.init(km, trustAllCerts, new SecureRandom());
// Uses default cipher suites, may include weak ones
```

### 5. Network Security Config Issues (Android 7+)

Android allows configuring network security in XML. Misconfiguration can make communication insecure.

**Vulnerable config:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="user" />
        </trust-anchors>
    </base-config>
</network-security-config>
```

**Problems:**
- `cleartextTrafficPermitted="true"` allows HTTP
- `certificates src="user"` allows user-installed certificates (including Burp Suite CA)

## Common Vulnerable Patterns

### Pattern 1: Testing Code in Production

Developers add code to bypass SSL for testing and forget to remove it.

```java
if (BuildConfig.DEBUG) {
    // Accept all certificates for testing
    enableCleartextTraffic();
    trustAllCertificates();
}
```

**Problem:** If `BuildConfig.DEBUG` is not properly configured, or if a debug build is released, SSL is bypassed.

### Pattern 2: WebView SSL Error Handling

WebView shows SSL errors. Developers make the WebView ignore these errors.

```java
webView.setWebViewClient(new WebViewClient() {
    @Override
    public void onReceivedSslError(WebView view,
                                   SslErrorHandler handler,
                                   SslError error) {
        handler.proceed(); // Ignores SSL errors!
    }
});
```

**What this does:** Even if the server has an invalid certificate, the WebView loads the page.

### Pattern 3: All Schemes in Intent-Filter

Apps that use intent-filters for URLs may accept both HTTP and HTTPS.

```xml
<data android:scheme="http" />
<data android:scheme="https" />
```

**Problem:** The app accepts both HTTP and HTTPS URLs.

## Testing for SSL/TLS Issues

### Test 1: Check Certificate Validation

```
1. Set up Burp Suite as a proxy
2. Install Burp CA certificate on device
3. Try to intercept traffic
   - If the app works, it accepts user-installed certificates
   - This means no SSL pinning and user certificates are trusted
```

### Test 2: Test with Invalid Certificate

```
1. In Burp Suite, generate a self-signed certificate
2. Intercept traffic with the self-signed cert
3. If the app still connects, SSL validation is broken
```

### Test 3: Check for Cleartext Traffic

```
1. Route traffic through Burp
2. Look for any "http://" URLs
3. Check Network Security Config
```

### Test 4: Check TLS Version Support

```
1. Use SSL Labs test (for server-side)
2. Use nmap to check TLS versions:
   nmap --script ssl-enum-ciphers -p 443 example.com
```

## Summary

Missing or misconfigured SSL/TLS makes encryption useless. The most common issues are trusting all certificates, disabling hostname verification, using weak TLS versions, accepting user-installed certificates, and allowing cleartext traffic. Always properly validate certificates and use strong TLS configurations.