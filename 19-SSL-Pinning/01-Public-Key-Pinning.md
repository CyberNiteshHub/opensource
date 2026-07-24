# Module 19: SSL Pinning - Public Key Pinning

## What is Public Key Pinning?

Public key pinning (HPKP - HTTP Public Key Pinning) means the app stores the public key of the server's certificate. When connecting, the app checks that the server's public key matches the stored key.

## How Public Key Pinning Works

```
1. App stores the server's public key hash
2. App connects to server
3. Server presents its certificate
4. App extracts the public key from the certificate
5. App compares the public key to the stored hash
6. If they match: connection is allowed
7. If they don't match: connection is rejected
```

## Public Key vs Certificate Pinning

| Aspect | Public Key Pinning | Certificate Pinning |
|--------|-------------------|---------------------|
| What is stored | Public key hash | Full certificate |
| Key rotation | New key pair | New certificate from CA |
| Certificate renewal | Same key, no issue | New pin needed |
| Flexibility | More flexible | Less flexible |

## Implementation

### Using OkHttp

```java
OkHttpClient client = new OkHttpClient.Builder()
    .certificatePinner(
        new CertificatePinner.Builder()
            .add("api.example.com",
                 "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAA=")
            .add("api.example.com",
                 "sha256/BBBBBBBBBBBBBBBBBBBBBBBBBBB=")
            .build()
    )
    .build();
```

### Using Android Network Security Config

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="false">
        <domain includeSubdomains="true">api.example.com</domain>
        <pin-set expiration="2025-12-31">
            <pin digest="SHA-256">base64encodedPublicKeyHash1=</pin>
            <pin digest="SHA-256">base64encodedPublicKeyHash2=</pin>
        </pin-set>
    </domain-config>
</network-security-config>
```

## Best Practices

- Pin at least 2 keys (primary and backup)
- Set an expiration date for pins
- Include a backup pin with different key pair
- Test pinning thoroughly before release
- Monitor for pin failures

## Summary

Public key pinning stores the server's public key hash in the app. The app only connects if the server's public key matches. It is more flexible than certificate pinning because the certificate can be renewed without changing the public key. Always have a backup pin for emergencies.