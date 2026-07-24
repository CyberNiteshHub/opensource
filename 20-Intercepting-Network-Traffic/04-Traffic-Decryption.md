# Module 20: Intercepting Network Traffic - Traffic Decryption

## Overview

Traffic decryption means decrypting HTTPS traffic to see the plain text data. This is essential for mobile testing.

## Decrypting with Burp Suite

### Step 1: Install Burp CA Certificate

1. In Burp, export the CA certificate
2. Install on the Android device as described in previous modules

### Step 2: Configure Proxy

Set the device proxy to Burp Suite's IP and port.

### Step 3: Decrypt HTTPS

Burp Suite automatically decrypts HTTPS using its CA certificate. All traffic appears in plain text in Burp.

## Bypassing SSL Pinning for Testing

When an app has SSL pinning, traffic interception does not work by default.

### Method 1: Frida

```javascript
// bypass-pinning.js
Java.perform(function() {
    var TrustManager = Java.use('javax.net.ssl.X509TrustManager');
    TrustManager.checkServerTrusted.implementation = function() {
        // Accept any certificate
    };
});
```

**Run:**
```
frida -U -l bypass-pinning.js com.example.app
```

### Method 2: Objection

```
objection -g com.example.app explore
android sslpinning disable
```

### Method 3: APK Modification

Use Apktool to disable SSL pinning in the code, rebuild, and sign.

## Decrypting with Wireshark

If you have the SSL private key, you can decrypt captured traffic:

1. Capture traffic with tcpdump
2. Open the PCAP in Wireshark
3. Provide the SSL private key (Edit -> Preferences -> Protocols -> TLS -> RSA keys list)

## Legal and Ethical Considerations

- Only test apps you own or have permission to test
- Do not decrypt traffic on production networks without authorization
- Use test environments for security research

## Summary

Traffic decryption allows seeing HTTPS data in plain text. Use Burp Suite with its CA certificate for testing apps without pinning. Use Frida or Objection to bypass SSL pinning for apps that implement it. Always test with proper authorization.