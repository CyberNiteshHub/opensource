# Module 10: Insecure Data Storage - Storing Passwords in Plain Text

## The Most Common Mistake

Storing passwords in plain text is the most common and most dangerous data storage mistake. It means saving passwords exactly as the user typed them, without any encryption or hashing.

## How Passwords Are Insecurely Stored

### 1. SharedPreferences (Most Common)

**Vulnerable code:**
```java
SharedPreferences prefs = getSharedPreferences("my_prefs", MODE_PRIVATE);
SharedPreferences.Editor editor = prefs.edit();
editor.putString("password", userPassword);  // Plain text!
editor.apply();
```

**What is stored:**
```xml
<!-- /data/data/com.example.app/shared_prefs/my_prefs.xml -->
<map>
    <string name="password">MyP@ssw0rd123</string>
</map>
```

**How to extract:**
```
adb shell
su
cat /data/data/com.example.app/shared_prefs/my_prefs.xml
```

Or without root, via backup:
```
adb backup -f backup.ab com.example.app
# Extract and read the backup
```

### 2. SQLite Database

**Vulnerable code:**
```java
SQLiteDatabase db = openOrCreateDatabase("users.db", MODE_PRIVATE, null);
db.execSQL("CREATE TABLE IF NOT EXISTS users (username TEXT, password TEXT)");
db.execSQL("INSERT INTO users VALUES ('admin', 'pass123')");  // Plain text!
```

**How to extract:**
```
adb shell
su
sqlite3 /data/data/com.example.app/databases/users.db
SELECT * FROM users;
```

### 3. Internal Files

**Vulnerable code:**
```java
File file = new File(getFilesDir(), "credentials.txt");
FileOutputStream fos = new FileOutputStream(file);
fos.write(password.getBytes());  // Plain text!
fos.close();
```

### 4. Logcat

**Vulnerable code:**
```java
Log.d("Login", "User password: " + password);  // In log!
```

**How to extract:**
```
adb logcat -d | grep "password"
```

### 5. Cache Files

**Vulnerable code:**
```java
File cacheDir = getCacheDir();
File cacheFile = new File(cacheDir, "temp_data");
// Writes sensitive data to cache
```

## How Attackers Extract Passwords

### Method 1: Root Access

If the device is rooted:
```
adb shell
su
cd /data/data/com.example.app/
cat shared_prefs/*.xml
cat databases/*.db
cat files/*
```

### Method 2: ADB Backup

If `android:allowBackup="true"`:
```
adb backup -f backup.ab com.example.app
# Convert and extract
dd if=backup.ab bs=24 skip=1 | openssl zlib -d > backup.tar
tar -xvf backup.tar
cat apps/com.example.app/sp/*.xml
```

### Method 3: Debugging

If the app is debuggable:
```
adb shell
run-as com.example.app
cat /data/data/com.example.app/shared_prefs/*.xml
```

### Method 4: Malicious Apps

A malicious app on the same device can:
- Read logcat output (prior to Android 8)
- Read external storage
- Access clipboard data
- Exploit vulnerabilities for root access

## Real Examples

### Example 1: Facebook (2019)

Facebook stored hundreds of millions of passwords in plain text in internal logs. Employees could read them. The passwords were not hashed or encrypted.

### Example 2: Twitter (2018)

Twitter disclosed that a bug caused passwords to be stored in plain text in an internal log before hashing.

### Example 3: GitHub (2018)

GitHub discovered plain text passwords in internal application logs due to a logging misconfiguration.

## Why Storing Passwords in Plain Text is Dangerous

**1. Immediate Account Takeover**
Anyone who accesses the stored password can log in as the user.

**2. Credential Stuffing**
Users often reuse passwords across sites. Stealing one password gives access to many accounts.

**3. No Recovery**
Once a plain text password is exposed, it cannot be "un-exposed". The user must change their password everywhere.

**4. Compliance Violations**
Storing passwords in plain text violates:
- GDPR (Europe)
- PCI DSS (Payment data)
- HIPAA (Health data)
- Various state laws

## Best Practices for Password Storage

### For Authentication Passwords (Server-Side)

Passwords should never be stored on the device. Store them on the server using proper hashing:

```java
// Server-side: Hash the password with bcrypt
String hash = BCrypt.hashpw(password, BCrypt.gensalt(12));
```

### For App-Specific Passwords (Device-Side)

If you must store a password on the device:

**Option 1: Android Keystore (Most Secure)**
```java
KeyStore keyStore = KeyStore.getInstance("AndroidKeyStore");
keyStore.load(null);

KeyGenParameterSpec spec = new KeyGenParameterSpec.Builder(
    "my_key", KeyProperties.PURPOSE_ENCRYPT | KeyProperties.PURPOSE_DECRYPT)
    .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
    .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
    .build();

KeyGenerator generator = KeyGenerator.getInstance(
    KeyProperties.KEY_ALGORITHM_AES, "AndroidKeyStore");
generator.init(spec);
SecretKey key = generator.generateKey();
```

**Option 2: EncryptedSharedPreferences**
```java
MasterKey masterKey = new MasterKey.Builder(context)
    .setKeyScheme(MasterKey.KeyScheme.AES256_GCM)
    .build();

SharedPreferences sharedPreferences = EncryptedSharedPreferences.create(
    context, "secure_prefs",
    masterKey,
    EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
    EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
);

sharedPreferences.edit().putString("password", password).apply();
```

## Testing for Plain Text Passwords

### Manual Testing

```
1. Install the app
2. Log in with a test account
3. Check SharedPreferences files
4. Check SQLite databases
5. Check logcat output
6. Check cache directory
7. Check backup data
```

### Using MobSF

MobSF automatically checks for hardcoded passwords and insecure storage. Look for findings related to:
- Hardcoded credentials
- Plain text storage
- Insecure SharedPreferences
- Unencrypted databases

## Summary

Storing passwords in plain text is a critical vulnerability. Passwords are stored in SharedPreferences, SQLite databases, files, logs, and caches. Attackers can extract them via root access, backup, debugging, or malicious apps. Use Android Keystore or EncryptedSharedPreferences for secure password storage.