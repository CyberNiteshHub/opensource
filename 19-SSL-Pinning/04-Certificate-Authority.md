# Module 19: SSL Pinning - Certificate Authority (CA)

## What is a Certificate Authority?

A Certificate Authority (CA) is a trusted organization that issues digital certificates. When an app connects to a server over HTTPS, the server presents a certificate signed by a CA. The app trusts the certificate because it trusts the CA.

## How CAs Work

```
1. Server generates a key pair (public + private)
2. Server sends public key to CA for signing
3. CA verifies the server's identity
4. CA signs the certificate
5. Server uses the signed certificate for HTTPS
6. Client (app) receives the certificate
7. Client checks if a trusted CA signed it
8. If yes, connection proceeds
```

## Trusted CA List

Android devices have a list of trusted CAs. These include:
- DigiCert
- Let's Encrypt
- GlobalSign
- Comodo
- Sectigo
- GoDaddy

System certificates are trusted by default. User-installed certificates (like Burp Suite's CA) are not trusted by default (Android 14+).

## When a CA is Compromised

A compromised CA can issue valid certificates for any domain. This is the main reason SSL pinning exists.

### Real CA Compromises

| CA | Year | Impact |
|----|------|--------|
| DigiNotar | 2011 | Issued fake Google certificates |
| Comodo | 2011 | Issued fake certificates for Google, Yahoo, Skype |
| Symantec | 2017 | Issued invalid certificates over many years |

## Root vs Intermediate CA

**Root CA:** The top-level CA. Trusted directly.
**Intermediate CA:** Signed by a root CA. Used for day-to-day certificate issuance.

```
Root CA (self-signed)
    |
    +-- Intermediate CA 1
    |       |
    |       +-- Server Certificate A
    |       +-- Server Certificate B
    |
    +-- Intermediate CA 2
            |
            +-- Server Certificate C
```

## Why Pinning Protects Against CA Compromise

Without pinning: App trusts any certificate signed by a trusted CA.
With pinning: App only trusts the specific pinned certificate/public key.

Even if a CA issues a fake certificate for your domain, pinning rejects it because it does not match the stored pin.

## Summary

Certificate Authorities (CAs) issue and sign certificates that enable HTTPS. Trusted CA lists are built into Android. When a CA is compromised, attackers can issue fake certificates. SSL pinning protects against this by only accepting specific pinned certificates or public keys, regardless of which CA signed them.