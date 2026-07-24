# Module 11: Insecure Communication - Definition

## What is Insecure Communication?

Insecure Communication is the number three risk in the OWASP Mobile Top 10 (M3). It means the app sends or receives data over a network without proper security. Attackers can intercept, read, or modify this data.

## OWASP Mobile Top 10 - M3

M3 covers all types of insecure communication:
- Using HTTP instead of HTTPS
- Weak SSL/TLS configuration
- Missing certificate validation
- Accepting all certificates
- No SSL pinning
- Using insecure protocols (FTP, Telnet, SMTP without TLS)
- Insecure WebSocket connections

## How Mobile Apps Communicate

Mobile apps communicate with servers in many ways:

```
Mobile App
    |
    +---> HTTP/HTTPS APIs
    +---> WebSocket connections
    +---> Custom TCP/UDP protocols
    +---> Push notifications
    +---> File uploads/downloads
    +---> Database connections
    +---> Third-party SDK communications
```

Each type of communication can be insecure.

## Man-in-the-Middle (MITM) Attack

The main threat to network communication is the Man-in-the-Middle (MITM) attack.

### How MITM Works

```
Normal Communication:
  App -------------------> Server
       Secure connection

MITM Attack:
  App -----> Attacker -----> Server
       Fake cert      Real cert
```

### What an Attacker Can Do in MITM

| Action | Description |
|--------|-------------|
| Eavesdrop | Read all data sent between app and server |
| Modify | Change data before it reaches the server |
| Inject | Add malicious data to the communication |
| Replay | Resend captured data (e.g., repeat a payment) |
| Redirect | Send the app to a different server |

### How Attackers Perform MITM

1. **Same Wi-Fi Network** - Attacker connects to the same public Wi-Fi as the victim
2. **Fake Access Point** - Attacker creates a fake Wi-Fi hotspot
3. **ARP Spoofing** - Attacker intercepts traffic on the local network
4. **DNS Spoofing** - Attacker redirects the app to a fake server
5. **Compromised Router** - Attacker controls the network router

## Common Communication Vulnerabilities

### 1. No Encryption (HTTP)

Data is sent in plain text. Anyone on the network can read it.

**Example:**
```
POST http://api.example.com/login
username=admin&password=secret123
```

Anyone on the network can see the username and password.

### 2. Weak Encryption (Old SSL/TLS)

Using old versions of SSL or TLS that have known vulnerabilities.

**Vulnerable protocols:**
- SSL 2.0 (broken)
- SSL 3.0 (broken)
- TLS 1.0 (weak)
- TLS 1.1 (weak)

### 3. Missing Certificate Validation

The app does not check if the server's certificate is valid.

**Vulnerable code:**
```java
HostnameVerifier allHostsValid = new HostnameVerifier() {
    public boolean verify(String hostname, SSLSession session) {
        return true; // Accepts any certificate!
    }
};
HttpsURLConnection.setDefaultHostnameVerifier(allHostsValid);
```

### 4. Accepting All Certificates

The app trusts any certificate, including fake ones.

**Vulnerable code:**
```java
TrustManager[] trustAllCerts = new TrustManager[] {
    new X509TrustManager() {
        public void checkClientTrusted(...) {}
        public void checkServerTrusted(...) {} // Does nothing!
        public X509Certificate[] getAcceptedIssuers() {
            return new X509Certificate[0];
        }
    }
};
```

### 5. No SSL Pinning

The app does not verify the server's certificate against a known copy.

Without pinning, any valid certificate (even from a compromised CA) can be used to intercept traffic.

### 6. Insecure Third-Party SDKs

Third-party libraries may communicate insecurely even if the main app is secure.

### 7. WebSocket Without WSS

WebSocket connections without encryption (ws:// instead of wss://) are vulnerable to MITM.

### 8. Mixed Content

Loading secure HTTPS pages with insecure HTTP resources (images, scripts).

## Why Insecure Communication Happens

**1. Development Convenience**
HTTP is easier to set up than HTTPS during development. Developers forget to switch to HTTPS for production.

**2. Performance Concerns**
Encryption adds overhead. This is usually negligible with modern devices.

**3. Legacy Systems**
Old servers may not support modern TLS versions.

**4. Third-Party Dependencies**
SDKs and libraries used by the app may communicate insecurely.

**5. Testing Leftovers**
Code that accepts all certificates (for testing) is left in production.

## Summary

Insecure Communication (M3) means the app's network communication is not properly secured. Attackers can perform MITM attacks to intercept, read, and modify data. Common vulnerabilities include using HTTP, weak SSL/TLS, missing certificate validation, accepting all certificates, and no SSL pinning. Proper encryption and certificate validation are essential for secure communication.