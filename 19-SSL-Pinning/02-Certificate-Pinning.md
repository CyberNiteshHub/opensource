# Module 19: SSL Pinning - Certificate Pinning

## What is Certificate Pinning?

Certificate pinning means the app stores the exact server certificate. When connecting, the app checks that the server presents the exact same certificate.

## How Certificate Pinning Works

```
1. App stores the server's certificate (or its hash)
2. App connects to server
3. Server presents its certificate
4. App compares the presented certificate to the stored one
5. If they match exactly: connection is allowed
6. If they don't match: connection is rejected
```

## Implementation

### Using Android Network Security Config

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="false">
        <domain includeSubdomains="true">api.example.com</domain>
        <pin-set>
            <!-- Pin the exact certificate -->
            <pin digest="SHA-256">base64EncodedCertHash=</pin>
        </pin-set>
    </domain-config>
</network-security-config>
```

### Getting the Certificate Hash

```bash
# Get certificate from server
openssl s_client -connect api.example.com:443 </dev/null 2>/dev/null \
    | openssl x509 -pubkey -noout \
    | openssl pkey -pubin -outform der \
    | openssl dgst -sha256 -binary \
    | openssl enc -base64
```

## Drawbacks of Certificate Pinning

| Issue | Explanation |
|-------|-------------|
| Certificate renewal | New certificate means new pin needed |
| Certificate expiry | Must update app before cert expires |
| Key compromise | If private key leaked, pin is useless |
| App updates needed | Each pin change requires app update |

## When to Use Certificate Pinning

**Use when:**
- You control both the app and the server
- Certificates change infrequently
- You can push app updates quickly

**Do not use when:**
- Certificates change frequently
- You cannot push updates quickly
- You use third-party APIs

## Summary

Certificate pinning stores the exact server certificate in the app. It is stricter than public key pinning but requires app updates when the certificate changes. Use for APIs you control with stable certificates.