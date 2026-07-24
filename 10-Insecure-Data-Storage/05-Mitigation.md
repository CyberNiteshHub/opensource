# Module 10: Insecure Data Storage - Mitigation

## Overview

Mitigation means preventing insecure data storage. This lesson covers how to securely store data on Android devices.

## General Principles

1. **Minimize stored data** - Do not store sensitive data if you do not need it
2. **Use platform security features** - Android Keystore, EncryptedSharedPreferences
3. **Encrypt at rest** - All sensitive data on device must be encrypted
4. **Protect encryption keys** - Keys must be stored in Android Keystore
5. **Do not store on external storage** - External storage can be read by any app

## 1. Android Keystore

Android Keystore stores cryptographic keys in a secure container. Keys cannot be extracted, even with root access.

### When to use:
- Storing encryption keys
- Storing signing keys
- Storing authentication keys

### How to use:

```java
// Generate a key in Android Keystore
KeyGenParameterSpec spec = new KeyGenParameterSpec.Builder(
        "my_secure_key",
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

### Benefits:
- Keys are hardware-backed on devices with TEE
- Keys cannot be extracted from the device
- Keys can require user authentication to use
- Supports key use limitations

## 2. EncryptedSharedPreferences

EncryptedSharedPreferences automatically encrypts both keys and values in SharedPreferences.

### When to use:
- Storing small amounts of sensitive data
- Storing authentication tokens
- Storing user preferences with sensitive values

### How to use:

```java
// Create MasterKey
MasterKey masterKey = new MasterKey.Builder(context)
    .setKeyScheme(MasterKey.KeyScheme.AES256_GCM)
    .build();

// Create EncryptedSharedPreferences
SharedPreferences sharedPreferences = EncryptedSharedPreferences.create(
    context,
    "secure_prefs_file",
    masterKey,
    EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
    EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
);

// Store and retrieve data (transparently encrypted)
sharedPreferences.edit()
    .putString("auth_token", token)
    .apply();

String token = sharedPreferences.getString("auth_token", null);
```

## 3. SQLCipher (Encrypted Databases)

SQLCipher encrypts the entire SQLite database file.

### When to use:
- Storing structured data
- Storing large amounts of data
- Storing sensitive data that needs to be queried

### How to use:

```groovy
// build.gradle
implementation 'net.zetetic:android-database-sqlcipher:4.5.4'
```

```java
// Initialize SQLCipher
SQLiteDatabase.loadLibs(context);

// Open encrypted database
String passphrase = "your-strong-passphrase";
File databaseFile = new File(context.getFilesDir(), "secure.db");
SQLiteDatabase database = SQLiteDatabase.openOrCreateDatabase(
    databaseFile, passphrase, null);

// Use database normally (encryption is transparent)
database.execSQL("CREATE TABLE IF NOT EXISTS secrets (id INT, data TEXT)");
database.execSQL("INSERT INTO secrets VALUES (1, 'sensitive data')");
```

### Important:
- Store the passphrase in Android Keystore
- Do not hardcode the passphrase in code
- Consider using a passphrase derived from user credentials

## 4. Android Storage Options Comparison

| Storage Type | Encrypted by Default | Key Storage | Best For |
|-------------|---------------------|-------------|----------|
| SharedPreferences | No | No | Non-sensitive preferences |
| EncryptedSharedPreferences | Yes | Keystore | Small sensitive data |
| SQLite Database | No | No | Structured data |
| SQLCipher Database | Yes | Keystore | Sensitive structured data |
| Internal Files | No | No | Non-sensitive files |
| Encrypted Files | Yes | Keystore | Sensitive files |
| External Storage | No | No | Public files only |

## 5. File Encryption

For storing encrypted files:

```java
// Encrypt a file
FileInputStream fileIn = new FileInputStream(plainFile);
FileOutputStream fileOut = new FileOutputStream(encryptedFile, key);

// Use CipherOutputStream for automatic encryption
Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
cipher.init(Cipher.ENCRYPT_MODE, secretKey);
CipherOutputStream cipherOut = new CipherOutputStream(fileOut, cipher);

byte[] buffer = new byte[8192];
int count;
while ((count = fileIn.read(buffer)) > 0) {
    cipherOut.write(buffer, 0, count);
}
cipherOut.close();
fileIn.close();
```

## 6. Network Security

Store network-related data securely:

**Tokens:**
```java
// Store auth tokens in EncryptedSharedPreferences
EncryptedSharedPreferences.create(...)
    .edit().putString("auth_token", token).apply();
```

**API Keys:**
```java
// Do NOT store API keys in the app
// Fetch them from your server at runtime over HTTPS
```

## 7. Cache Management

Clear sensitive data from cache:

```java
// Clear app cache
context.getCacheDir().delete();

// Or clear specific cached files
File cacheDir = context.getCacheDir();
File[] files = cacheDir.listFiles();
for (File file : files) {
    if (file.getName().contains("sensitive")) {
        file.delete();
    }
}
```

## 8. Log Management

Never log sensitive data:

```java
// Bad
Log.d("Login", "Password: " + password);

// Good
Log.d("Login", "Login attempt: " + username);
```

## 9. Backup Protection

Prevent sensitive data from being backed up:

```xml
<!-- Option 1: Disable backup completely -->
<application android:allowBackup="false" ...>

<!-- Option 2: Exclude sensitive files -->
<application android:fullBackupContent="@xml/backup_rules" ...>
```

In `res/xml/backup_rules.xml`:
```xml
<full-backup-content>
    <exclude domain="sharedpref" path="secure_prefs.xml"/>
    <exclude domain="database" path="encrypted_data.db"/>
    <exclude domain="file" path="private/"/>
</full-backup-content>
```

## Security Checklist

```
[ ] Use Android Keystore for all cryptographic keys
[ ] Use EncryptedSharedPreferences for sensitive key-value data
[ ] Use SQLCipher for encrypted database storage
[ ] Never store sensitive data in plain text
[ ] Never store sensitive data on external storage
[ ] Clear cache regularly, especially sensitive data
[ ] Do not log sensitive data
[ ] Restrict backup to non-sensitive data only
[ ] Use ProGuard/R8 to obfuscate the code
[ ] Use network security config to prevent cleartext traffic
```

## Summary

Mitigate insecure data storage by using Android Keystore for keys, EncryptedSharedPreferences for key-value data, and SQLCipher for databases. Never store data on external storage. Clear cache regularly. Do not log sensitive data. Restrict backups to non-sensitive data. Always encrypt sensitive data at rest.