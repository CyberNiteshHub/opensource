# Module 13: Insufficient Cryptography - Prevention and Mitigation

## Overview

Prevention means using cryptography correctly from the start. This lesson covers how to implement strong cryptography in mobile apps.

## General Principles

1. **Use standard algorithms** - Do not create custom cryptography
2. **Use strong algorithms** - AES-256, SHA-256, RSA-2048+
3. **Use proper modes** - GCM mode for encryption
4. **Protect keys** - Store keys in Android Keystore
5. **Use platform APIs** - Android provides secure crypto APIs

## 1. Use Strong Algorithms

### Encryption

| Use Case | Strong Algorithm | Key Size |
|----------|-----------------|----------|
| Data at rest | AES-GCM | 256 bits |
| Data in transit | TLS 1.3 | - |
| File encryption | AES-CBC with HMAC | 256 bits |

**Implementation:**
```java
// AES-GCM (authenticated encryption)
Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
gcmlParameterSpec = new GCMParameterSpec(128, iv);
cipher.init(Cipher.ENCRYPT_MODE, secretKey, gcmlParameterSpec);
```

### Hashing

| Use Case | Strong Algorithm |
|----------|-----------------|
| Password storage | bcrypt, scrypt, Argon2 |
| Data integrity | SHA-256, SHA-3 |
| Signing | HMAC-SHA256 |

**Implementation:**
```java
// Password hashing with bcrypt
String hash = BCrypt.hashpw(password, BCrypt.gensalt(12));
```

### Signing

| Use Case | Strong Algorithm | Key Size |
|----------|-----------------|----------|
| Code signing | RSA-PSS | 4096 bits |
| Message signing | ECDSA | 256 bits |

## 2. Use Android Keystore

Android Keystore stores keys securely. Keys cannot be extracted, even with root access.

```java
// Generate a key that cannot be extracted
KeyGenParameterSpec spec = new KeyGenParameterSpec.Builder(
        "app_key",
        KeyProperties.PURPOSE_ENCRYPT | KeyProperties.PURPOSE_DECRYPT)
    .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
    .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
    .setKeySize(256)
    .build();

KeyGenerator keyGenerator = KeyGenerator.getInstance(
    KeyProperties.KEY_ALGORITHM_AES, "AndroidKeyStore");
keyGenerator.init(spec);
SecretKey key = keyGenerator.generateKey();
```

**Benefits:**
- Keys are hardware-backed on supported devices
- Keys cannot be extracted
- Key usage can require user authentication

## 3. Secure Key Management

### Do NOT hardcode keys

```java
// BAD - hardcoded key
String key = "MySecretKey12345";

// GOOD - generate key in Keystore
SecretKey key = generateKeyInKeystore();
```

### Do NOT store keys in SharedPreferences

```java
// BAD - key in SharedPreferences
prefs.edit().putString("encryption_key", key).apply();

// GOOD - key in Keystore
// (Key is automatically stored securely)
```

### Key lifecycle

```
Key Generation -> Key Usage -> Key Rotation -> Key Revocation
```

## 4. Use Proper Initialization Vectors

IVs must be random and unique for each encryption.

```java
// Generate random IV
byte[] iv = new byte[12];
SecureRandom random = new SecureRandom();
random.nextBytes(iv);

// Store IV with ciphertext (it is not secret)
GCMParameterSpec gcmSpec = new GCMParameterSpec(128, iv);
cipher.init(Cipher.ENCRYPT_MODE, key, gcmSpec);
```

## 5. Use High-Level Crypto Libraries

Instead of using crypto APIs directly, use higher-level libraries.

### Jetpack Security (AndroidX Security)

```java
// EncryptedFile
EncryptedFile encryptedFile = new EncryptedFile.Builder(
    context,
    new File(context.getFilesDir(), "secret.txt"),
    masterKey,
    EncryptedFile.FileEncryptionScheme.AES256_GCM_HKDF_4KB
).build();

// Write encrypted data
encryptedFile.openFileOutput().write(data);
```

### Tink (Google)

```java
Aead aead = AeadConfig.create());
byte[] ciphertext = aead.encrypt(plaintext, associatedData);
```

## 6. Cryptography Selection Guide

| Requirement | Algorithm | Mode/Key Size |
|-------------|-----------|---------------|
| Encrypt data | AES | GCM, 256-bit key |
| Hash data | SHA | SHA-256 or SHA-3 |
| Hash passwords | bcrypt | Cost factor 12+ |
| Sign data | RSA | PSS, 2048+ bits |
| Key agreement | ECDH | P-256 or P-384 |
| Random numbers | SecureRandom | - |

## 7. Prevention Checklist

```
[ ] No MD5 or SHA1 used
[ ] No DES or RC4 used
[ ] AES-256 with GCM mode used for encryption
[ ] Keys stored in Android Keystore
[ ] No hardcoded keys in code
[ ] Random IV for each encryption operation
[ ] No custom cryptography implementation
[ ] Passwords hashed with bcrypt/scrypt/Argon2
[ ] TLS 1.2+ used for network communication
[ ] Certificate validation properly implemented
```

## Summary

Prevent insufficient cryptography by using strong standard algorithms (AES-256-GCM, SHA-256, bcrypt), storing keys in Android Keystore, generating random IVs, and using high-level crypto libraries. Never hardcode keys or create custom cryptography. Use the platform's built-in crypto APIs.