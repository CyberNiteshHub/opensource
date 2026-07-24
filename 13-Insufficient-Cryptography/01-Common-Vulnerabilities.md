# Module 13: Insufficient Cryptography - Common Vulnerabilities

## What is Insufficient Cryptography?

Insufficient Cryptography is the number five risk in the OWASP Mobile Top 10 (M5). It means the app uses weak or broken cryptographic algorithms. Attackers can decrypt data that should be protected.

## OWASP Mobile Top 10 - M5

M5 covers cryptography issues:
- Using broken algorithms (MD5, SHA1, DES, RC4)
- Using weak key sizes
- Hardcoded encryption keys
- Improper initialization vectors
- Custom cryptography implementations
- Poor key management

## Common Cryptographic Vulnerabilities

### 1. Using Broken Hash Algorithms

MD5 and SHA1 are broken. They should never be used for security.

**Vulnerable code:**
```java
import java.security.MessageDigest;

MessageDigest md = MessageDigest.getInstance("MD5");
byte[] hash = md.digest(password.getBytes());
// MD5 hash can be cracked instantly
```

**Why it is broken:**
- MD5: Collision attacks possible (2004)
- SHA1: Collision attacks possible (2017, SHAttered)
- Both can be brute-forced at billions of hashes per second

**Detection:**
```java
// Search for these in code:
MessageDigest.getInstance("MD5")
MessageDigest.getInstance("SHA-1")
MessageDigest.getInstance("SHA1")
```

### 2. Using Broken Encryption Algorithms

Some encryption algorithms are broken or too weak.

**Vulnerable algorithms:**

| Algorithm | Issue |
|-----------|-------|
| DES | 56-bit key, brute forced in hours |
| 3DES | Slow, deprecated since 2023 |
| RC2 | Broken, very weak |
| RC4 | Broken, biases in output |
| Blowfish | Small 64-bit block size |

**Vulnerable code:**
```java
Cipher cipher = Cipher.getInstance("DES/ECB/PKCS5Padding");
// DES is too weak for any security purpose
```

### 3. Using Weak Key Sizes

Even with good algorithms, small key sizes are insecure.

| Algorithm | Minimum Key Size | Recommended |
|-----------|-----------------|-------------|
| RSA | 2048 bits | 4096 bits |
| AES | 128 bits | 256 bits |
| ECC | 224 bits | 256 bits |

**Vulnerable code:**
```java
KeyPairGenerator keyGen = KeyPairGenerator.getInstance("RSA");
keyGen.initialize(512); // 512-bit RSA can be cracked
KeyPair pair = keyGen.generateKeyPair();
```

### 4. ECB Mode Encryption

ECB (Electronic Codebook) mode encrypts each block independently. Identical plaintext blocks produce identical ciphertext blocks.

**Vulnerable code:**
```java
Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
```

**Why ECB is bad:**
Patterns in plaintext are visible in ciphertext. This is why the famous "ECB penguin" image can still be recognized after encryption.

**Always use:**
- GCM (Galois/Counter Mode) - Authenticated encryption
- CBC (Cipher Block Chaining) with HMAC

### 5. Hardcoded Encryption Keys

Keys hardcoded in the source code can be extracted from the APK.

**Vulnerable code:**
```java
private static final String SECRET_KEY = "ThisIsASecretKey123!";
// Anyone who decompiles the app can see this key

Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
SecretKeySpec keySpec = new SecretKeySpec(SECRET_KEY.getBytes(), "AES");
```

**How attackers extract:**
```
strings app.apk
jadx app.apk
# Find the hardcoded key string
```

### 6. Static or Predictable Initialization Vectors (IV)

IVs must be random and unique for each encryption operation.

**Vulnerable code:**
```java
byte[] iv = new byte[12]; // All zeros!
IvParameterSpec ivSpec = new IvParameterSpec(iv);
cipher.init(Cipher.ENCRYPT_MODE, keySpec, ivSpec);
```

**Why it is bad:**
With a fixed IV, the same plaintext always produces the same ciphertext. This leaks information about the data.

### 7. Custom Cryptography

Developers create their own encryption algorithms. This is always dangerous.

**Example of custom "encryption":**
```java
public String encrypt(String data) {
    StringBuilder result = new StringBuilder();
    for (char c : data.toCharArray()) {
        result.append((char)(c + 5)); // Caesar cipher!
    }
    return result.toString();
}
```

**Why custom crypto is dangerous:**
- Not reviewed by cryptography experts
- Almost always contains flaws
- No standardized testing
- Easily reverse-engineered

### 8. No Encryption at All

Data that should be encrypted is stored or sent in plain text.

**Example:**
```java
// Credit card number stored in plain text
SharedPreferences prefs = getSharedPreferences("payment", MODE_PRIVATE);
prefs.edit().putString("credit_card", "4111-1111-1111-1111").apply();
```

## Real-World Examples

### Example 1: WhatsApp (2011)

WhatsApp used MD5 for message integrity. This was later fixed.

### Example 2: Adobe (2013)

Adobe used 3DES encryption for passwords. They also used the same encryption key for all users (hardcoded).

### Example 3: Various Banking Apps

Many banking apps use hardcoded AES keys in their APK files. Attackers extract the keys and decrypt local data.

## Testing for Cryptography Issues

### Static Analysis

```
1. Decompile the APK with jadx
2. Search for:
   - "MD5", "SHA-1", "SHA1"
   - "DES", "RC4", "RC2"
   - "AES/ECB"
   - "getInstance("
   - Hardcoded strings that look like keys
```

### Dynamic Analysis

```
1. Use Frida to intercept crypto API calls
2. Log the algorithm, key, and IV used
3. Check if ECB mode is used
4. Check if IV is static
```

## Summary

Insufficient cryptography means using weak algorithms (MD5, SHA1, DES, RC4), small key sizes, ECB mode, hardcoded keys, static IVs, or custom encryption. These vulnerabilities make encryption useless because attackers can decrypt the data. Always use strong, standard algorithms with proper key management.