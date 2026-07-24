# Module 10: Insecure Data Storage - Definition

## What is Insecure Data Storage?

Insecure Data Storage is the number two risk in the OWASP Mobile Top 10 (M2). It means storing sensitive data on the device in an insecure way. This data can be accessed by attackers.

Sensitive data includes:
- Passwords and PINs
- Authentication tokens
- API keys
- Personal information (name, email, phone)
- Financial data (credit card numbers, bank details)
- Health records
- Private messages

## OWASP Mobile Top 10 - M2

M2 covers all types of insecure data storage:
- Storing data in plain text
- Using weak encryption
- Storing data in easily accessible locations
- Not protecting data at rest
- Exposing data through backups

## Where Data is Stored on Android

```
+-------------------------------------------+
| App Private Storage                        |
| /data/data/com.example.app/               |
|    +-- databases/      (SQLite databases) |
|    +-- shared_prefs/   (SharedPreferences)|
|    +-- files/          (Regular files)    |
|    +-- cache/          (Cache files)      |
+-------------------------------------------+
| External Storage (SD Card)                 |
| /sdcard/Android/data/com.example.app/     |
+-------------------------------------------+
| Internal Storage (Public)                  |
| /storage/emulated/0/                      |
+-------------------------------------------+
```

### 1. SharedPreferences

SharedPreferences stores key-value pairs in an XML file.

**Location:** `/data/data/com.example.app/shared_prefs/`

**How it is stored:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<map>
    <string name="password">mysecretpassword</string>
    <string name="api_key">sk_live_xxxxx</string>
    <boolean name="is_premium" value="true" />
</map>
```

**The problem:** The XML file is stored in plain text. Any app with root access can read it.

### 2. SQLite Databases

SQLite is the built-in database for Android apps.

**Location:** `/data/data/com.example.app/databases/`

**The problem:** Databases are usually unencrypted. Anyone with root access can open them and read the data.

### 3. Internal Storage (File)

Files stored in the app's private directory.

**Location:** `/data/data/com.example.app/files/`

**The problem:** Files are stored in plain text by default.

### 4. External Storage

Files stored on the SD card or shared storage.

**Location:** `/sdcard/Android/data/com.example.app/`

**The problem:** Other apps can read files on external storage if they have the READ_EXTERNAL_STORAGE permission.

### 5. Cache Directory

Temporary files stored by the app.

**Location:** `/data/data/com.example.app/cache/`

**The problem:** Cache files may contain sensitive data that is not cleaned up.

### 6. Logs

Android logging system (logcat).

**The problem:** Developers often log sensitive data for debugging and forget to remove it.

## Why Developers Store Data Insecurely

**1. Convenience**
It is easier to store data in plain text than to implement encryption.

**2. Lack of Awareness**
Developers do not know about Android Keystore or EncryptedSharedPreferences.

**3. Assumption of Security**
Developers think the app's private directory is secure enough.

**4. Legacy Code**
Old code that was written before security best practices were established.

**5. Performance Concerns**
Encryption is slower than plain text storage (the difference is usually negligible).

## Types of Sensitive Data at Risk

| Data Type | Example | Risk |
|-----------|---------|------|
| Authentication | Passwords, tokens | Account takeover |
| Personal | Name, email, phone | Identity theft |
| Financial | Credit card, bank account | Financial fraud |
| Health | Medical records | Privacy violation |
| Business | Trade secrets | Competitive loss |
| Location | GPS history | Stalking |
| Communications | Messages, call logs | Privacy invasion |

## Common Misconceptions

**"The app's private directory is secure."**
False. On a rooted device, any app can access `/data/data/` directories. Even on non-rooted devices, backups can expose data.

**"Users won't root their phones."**
Many users root their phones. Even if they do not, vulnerabilities can give apps root access.

**"We encrypt the data in the database."**
If you implement encryption incorrectly, it provides no real security. Use platform encryption APIs.

**"External storage is fine for non-sensitive data."**
Data can become sensitive due to context. A cached file might contain a draft message with personal information.

## Summary

Insecure Data Storage (M2) is storing sensitive data without proper protection. Data is stored in SharedPreferences, SQLite databases, files, cache, and logs. Common mistakes include plain text storage, weak encryption, and using external storage for sensitive data. Always use Android's built-in security features to protect stored data.