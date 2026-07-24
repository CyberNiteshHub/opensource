# Module 10: Insecure Data Storage - Unprotected Databases

## Overview

Android apps commonly use SQLite databases for local storage. These databases often contain sensitive data. When the database is not encrypted, anyone with access to the device can read it.

## Types of Database Storage

### 1. App-Internal SQLite Database

**Location:** `/data/data/com.example.app/databases/`

**How it is created:**
```java
SQLiteDatabase db = openOrCreateDatabase("app_data.db", MODE_PRIVATE, null);
db.execSQL("CREATE TABLE IF NOT EXISTS users (id INT, name TEXT, password TEXT)");
db.execSQL("INSERT INTO users VALUES (1, 'admin', 'secret123')");
```

**Access without root:** Cannot access directly.
**Access with root:** Full access to all data.

### 2. External Database Files

**Location:** `/sdcard/Android/data/com.example.app/databases/` or anywhere on SD card.

**How it is created:**
```java
String path = Environment.getExternalStorageDirectory() + "/myapp/data.db";
SQLiteDatabase db = SQLiteDatabase.openOrCreateDatabase(path, null, null);
```

**Access without root:** Any app with READ_EXTERNAL_STORAGE can read it.

### 3. Pre-Packaged Databases

Some apps ship with pre-populated databases in their assets folder.

**Location:** APK file `assets/` directory -> extracted to app's data directory.

**How it is packaged:**
```
APK
  +-- assets/
       +-- initial_data.db
       +-- dictionary.db
```

**Access:** Anyone can extract these from the APK file.

## How to Extract Database Files

### Using ADB (Root Required)

```
adb shell
su
cd /data/data/com.example.app/databases/
ls -la
sqlite3 database_name.db
.tables
SELECT * FROM users;
```

### Using ADB (Without Root)

```
adb exec-out run-as com.example.app cat databases/database.db > database.db
sqlite3 database.db
.tables
```

### Using Backup (No Root)

```
adb backup -f backup.ab com.example.app
dd if=backup.ab bs=24 skip=1 | openssl zlib -d > backup.tar
tar -xvf backup.tar
ls apps/com.example.app/db/
```

### Using File Managers (Root)

On a rooted device, use any root file manager to browse to `/data/data/com.example.app/databases/` and copy the files.

## Common Database Vulnerabilities

### 1. No Encryption

**The problem:** The database stores data in plain text.

**What you find:**
```
sqlite> SELECT * FROM credit_cards;
1|4111111111111111|123|12/25|John Doe
2|5500000000000004|456|08/24|Jane Smith
```

### 2. Weak Database Permissions

**The problem:** Database created with MODE_WORLD_READABLE (deprecated but still used).

```java
SQLiteDatabase db = openOrCreateDatabase("data.db", MODE_WORLD_READABLE, null);
```

### 3. SQL Injection Vulnerable

**The problem:** User input is concatenated into SQL queries.

```java
String query = "SELECT * FROM users WHERE name = '" + userName + "'";
Cursor cursor = db.rawQuery(query, null);
```

### 4. Database in External Storage

**The problem:** Database is on SD card where other apps can read it.

### 5. Backup Contains Database

**The problem:** ADB backup includes the unencrypted database.

## Real-World Database Leaks

### Case 1: Health App

A fitness tracking app stored user health data in an unencrypted SQLite database. The database contained:
- User name and email
- Height, weight, age
- Medical conditions
- Daily activity logs

**Impact:** Anyone with root access could read all health data of any user.

### Case 2: Messaging App

A messaging app stored chat history in an unencrypted database. Including:
- Contact names and phone numbers
- Message content (including private conversations)
- Shared photos and files (file paths)

**Impact:** Private conversations were exposed.

### Case 3: Banking App

A banking app stored transaction history in an unencrypted database. Including:
- Account numbers
- Transaction amounts
- Merchant names
- Account balance

**Impact:** Financial information was exposed.

## Testing for Unprotected Databases

### Step 1: Find Database Files

```
adb shell
run-as com.example.app
find . -name "*.db" -o -name "*.sqlite"
```

### Step 2: Check Database Contents

```
sqlite3 databases/main.db
.tables
SELECT sql FROM sqlite_master WHERE type='table';
```

### Step 3: Dump Sensitive Tables

```
SELECT * FROM users;
SELECT * FROM tokens;
SELECT * FROM credit_cards;
```

### Step 4: Check for External Storage Databases

```
adb shell
ls -la /sdcard/
find /sdcard/ -name "*.db"
```

## Prevention

### 1. Use Encrypted Databases (SQLCipher)

SQLCipher encrypts the entire database file.

```java
import net.sqlcipher.database.SQLiteDatabase;

SQLiteDatabase.loadLibs(context);
String password = "strong_database_password";
SQLiteDatabase db = SQLiteDatabase.openOrCreateDatabase(
    databaseFile, password, null);
```

### 2. Use Room with Encryption

Android's Room database can be used with SQLCipher:

```groovy
// build.gradle
implementation 'net.zetetic:android-database-sqlcipher:4.5.0'
implementation 'androidx.sqlite:sqlite-ktx:2.3.0'
```

### 3. Use EncryptedSharedPreferences

For key-value data instead of full databases:

```java
MasterKey masterKey = new MasterKey.Builder(context)
    .setKeyScheme(MasterKey.KeyScheme.AES256_GCM)
    .build();

SharedPreferences prefs = EncryptedSharedPreferences.create(
    context, "secure_prefs", masterKey,
    EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
    EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
);
```

### 4. Database Encryption Checklist

```
[ ] All databases encrypted (SQLCipher or similar)
[ ] Encryption key stored in Android Keystore
[ ] No databases on external storage
[ ] Backup excludes database files
[ ] Database password not hardcoded
[ ] Cache directory does not contain database copies
```

## Summary

Unprotected databases are a common source of data leakage. SQLite databases in the app's private directory, external storage, or pre-packaged in assets often contain sensitive data in plain text. Use SQLCipher for database encryption. Store encryption keys in Android Keystore. Do not store databases on external storage.