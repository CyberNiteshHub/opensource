# Module 11: Insecure Communication - Mitigation

## Overview

Mitigation means securing the app's network communication. This lesson covers how to encrypt and validate all network traffic.

## General Principles

1. **Always use HTTPS** - Never use HTTP
2. **Validate certificates** - Always check server certificates
3. **Use SSL pinning** - Pin certificates for critical apps
4. **Use strong TLS** - TLS 1.2 or higher
5. **Disable cleartext** - Do not allow HTTP traffic

## 1. Always Use HTTPS

The simplest and most important mitigation. Never use HTTP.

**Bad:**
```java
URL url = new URL("http://api.example.com/data");
```

**Good:**
```java
URL url = new URL("https://api.example.com/data");
```

### Enforce HTTPS in AndroidManifest

```xml
<application
    android:networkSecurityConfig="@xml/network_security_config"
    ...>
```

In `res/xml/network_security_config.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="false">
        <trust-anchors>
            <certificates src="system" />
        </trust-anchors>
    </base-config>
</network-security-config>
```

This ensures:
- HTTP traffic is blocked
- Only system-installed certificates are trusted
- User-installed certificates (like Burp CA) are not trusted

## 2. Proper Certificate Validation

Validate server certificates correctly.

**Correct implementation (default):**
```java
// URL.openConnection() validates certificates by default
URL url = new URL("https://api.example.com");
HttpsURLConnection conn = (HttpsURLConnection) url.openConnection();
conn.connect(); // Validates certificate automatically
```

**Using OkHttp (default):**
```java
OkHttpClient client = new OkHttpClient.Builder()
    .build(); // Default validates certificates properly
```

**Do NOT do this:**
```java
// NEVER trust all certificates
TrustManager[] trustAll = new TrustManager[] { ... };
```

## 3. SSL Pinning

SSL pinning ensures the app only accepts a specific certificate or public key.

### Certificate Pinning (Android Network Security Config)

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="false">
        <domain includeSubdomains="true">api.example.com</domain>
        <pin-set expiration="2025-12-31">
            <pin digest="SHA-256">base64encodedCertHash1=</pin>
            <pin digest="SHA-256">base64encodedCertHash2=</pin>
        </pin-set>
    </domain-config>
</network-security-config>
```

### Certificate Pinning (OkHttp)

```java
OkHttpClient client = new OkHttpClient.Builder()
    .certificatePinner(
        new CertificatePinner.Builder()
            .add("api.example.com",
                 "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAA=")
            .build()
    )
    .build();
```

### Best Practices for Pinning

- Pin at least 2 keys (primary and backup)
- Set an expiration date for pins
- Include a backup pin for key rotation
- Update pins before they expire
- Test pinning thoroughly before release

## 4. Use Strong TLS Versions

Use only TLS 1.2 or TLS 1.3.

**Android default:**
Since Android 10 (API 29), TLS 1.3 is supported by default.

**For older Android versions:**
```java
SSLContext sslContext = SSLContext.getInstance("TLSv1.2");
```

**Server-side configuration:**
```
Only allow:
- TLS 1.2
- TLS 1.3
Disable:
- SSLv2, SSLv3
- TLSv1.0, TLSv1.1
```

## 5. Use Strong Cipher Suites

Enable only strong cipher suites.

**Strong cipher suites:**
```
TLS_AES_256_GCM_SHA384 (TLS 1.3)
TLS_AES_128_GCM_SHA256 (TLS 1.3)
TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
```

## 6. Secure WebView Communication

WebView can load web pages. Ensure it uses secure connections.

```java
webView.setWebViewClient(new WebViewClient() {
    @Override
    public void onReceivedSslError(WebView view,
                                   SslErrorHandler handler,
                                   SslError error) {
        // DO NOT call handler.proceed()
        // Instead, show an error to the user
        handler.cancel();
        Toast.makeText(context,
            "SSL Error: Connection not secure",
            Toast.LENGTH_LONG).show();
    }
});
```

## 7. Secure WebSocket

Use WSS instead of WS.

**Bad:**
```java
WebSocket ws = new WebSocket("ws://chat.example.com");
```

**Good:**
```java
WebSocket ws = new WebSocket("wss://chat.example.com");
```

## 8. Security Checklist

```
[ ] All URLs use HTTPS, not HTTP
[ ] No custom TrustManager that trusts all certificates
[ ] No custom HostnameVerifier that accepts all
[ ] SSL pinning implemented for critical endpoints
[ ] Network Security Config disables cleartext traffic
[ ] Network Security Config uses only system certificates
[ ] TLS 1.2 or higher enforced
[ ] Strong cipher suites only
[ ] WebView SSL errors handled securely
[ ] WebSocket uses WSS
[ ] No cleartext traffic in debug builds
```

## 9. Testing Your Mitigation

After implementing mitigations, test them:

### Test 1: HTTP should fail

```
1. Set proxy to Burp Suite
2. Try to access http://example.com
3. App should show error or refuse connection
```

### Test 2: User certificates should not work

```
1. Install Burp CA certificate as user certificate
2. Try to intercept HTTPS traffic
3. App should refuse connection (if pinning is enabled)
```

### Test 3: Invalid certificate should fail

```
1. Point app to a server with expired certificate
2. App should show SSL error
```

### Test 4: Pinning test

```
1. Use a proxy with a different certificate
2. App should refuse the connection
```

## Summary

Mitigate insecure communication by always using HTTPS, validating certificates, implementing SSL pinning, using strong TLS versions, and disabling cleartext traffic. Use Android Network Security Config for easy configuration. Test all mitigations to ensure they work correctly.