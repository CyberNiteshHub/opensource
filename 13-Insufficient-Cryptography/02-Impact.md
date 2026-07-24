# Module 13: Insufficient Cryptography - Impact

## Overview

Insufficient cryptography makes encryption useless. Data that appears protected can actually be read by attackers. This lesson covers the impact of cryptography failures.

## Impact on Data Security

### 1. False Sense of Security

The biggest impact of weak cryptography is a false sense of security. Developers think data is protected, but it is not.

**Example:**
A banking app uses AES with a hardcoded key. The developer thinks customer data is encrypted. But anyone who decompiles the app finds the key and decrypts all data.

### 2. Data Decryption by Attackers

Weak encryption can be broken by attackers.

**Time to break different cryptography:**

| Algorithm | Time to Break | Cost |
|-----------|--------------|------|
| MD5 hash | Instant | Free |
| SHA1 hash | Minutes | Free |
| DES | Hours | $100 cloud credits |
| 512-bit RSA | Hours | $1,000 |
| Custom encryption | Minutes | Free |
| AES-256 (proper) | Centuries | Impossibly expensive |

### 3. Key Extraction from APK

Hardcoded keys in APK files are easily extracted.

**How attackers extract keys:**
```
1. Download the APK from Play Store
2. Run: strings app.apk
3. Look for 16/24/32 byte strings (potential AES keys)
4. Or decompile with jadx and search for key variables
```

### 4. Algorithm Cracking

Some algorithms have mathematical weaknesses.

**SHA1 collision attack (SHAttered):**
Two different files producing the same SHA1 hash cost only $110,000 in cloud compute. This means SHA1 cannot be trusted for integrity verification.

## Real-World Impact Examples

### Case 1: Playstation 3 (2010)

**What happened:**
Sony used a fixed ECDSA key (hardcoded) for signing code on PS3. Hackers extracted the key and could run any code on the console.

**Impact:**
- Complete loss of code signing security
- Piracy on PS3
- Sony could not revoke the key
- Long-term damage to PS3 security

### Case 2: Juniper VPN (2015)

**What happened:**
Juniper's VPN used Dual_EC_DRBG (a compromised random number generator) with a hardcoded constant. This allowed attackers to decrypt VPN traffic.

**Impact:**
- All VPN traffic could be decrypted by attackers
- $100M+ in investigation and fixes
- Major reputation damage
- Government customers affected

### Case 3: Hardcoded Keys in Apps (Many)

**What happened:**
Thousands of mobile apps have hardcoded AWS keys, Firebase keys, Stripe keys, etc.

**Impact:**
- Attackers use API keys to access backend services
- Massive bills run up on developer accounts
- User data accessed via the stolen keys

## Compliance Impact

| Regulation | Cryptography Requirement | Penalty for Weak Crypto |
|------------|-------------------------|------------------------|
| PCI DSS | Strong cryptography required | $500,000/month |
| GDPR | Appropriate technical measures | 4% of revenue |
| HIPAA | Encryption at rest and transit | $1.5M/year |
| FIPS 140-2 | Approved algorithms required | Government contract loss |

## Business Impact

### Direct Costs

| Cost Factor | Amount |
|-------------|--------|
| Security audit | $20,000 - $100,000 |
| Key rotation for all users | $50,000 - $500,000 |
| Force update all apps | $100,000 - $1,000,000 |
| Data breach remediation | $200,000 - $5,000,000 |

### Reputation Impact

- Users lose trust in the app
- Negative app store reviews
- Media coverage of "weak encryption"
- Competitive disadvantage

## Summary

Insufficient cryptography gives a false sense of security. Weak algorithms can be cracked, hardcoded keys can be extracted, and broken modes leak data. Real-world examples (PS3, Juniper) show that cryptography failures have severe consequences. Use only strong, standard algorithms with proper key management.