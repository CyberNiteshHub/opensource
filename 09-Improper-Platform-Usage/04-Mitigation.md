# Module 09: Improper Platform Usage - Mitigation

## Overview

Mitigation means preventing or fixing the vulnerability. This lesson covers how to prevent improper platform usage in Android apps.

## General Mitigation Principles

1. **Follow Platform Best Practices** - Use APIs as intended by the platform
2. **Minimize Exported Components** - Only export what is necessary
3. **Validate All Inputs** - Never trust data from other apps
4. **Use Platform Security Features** - Android provides security tools; use them
5. **Test Regularly** - Security testing should be part of development

## Mitigation by Component

### Mitigation for Activities

**Problem:** Activities are exported when they should not be.

**Fix 1: Do not export unless necessary**
```xml
<activity android:name=".PrivateActivity"
          android:exported="false" />
```

**Fix 2: Add permission check**
```xml
<activity android:name=".SensitiveActivity"
          android:exported="true"
          android:permission="com.example.permission.ADMIN" />
```

**Fix 3: Verify caller identity**
```java
protected void onCreate(Bundle savedInstanceState) {
    if (!isCallerAuthorized()) {
        finish();
        return;
    }
}

private boolean isCallerAuthorized() {
    int callingUid = Binder.getCallingUid();
    String callingPackage = getPackageManager()
        .getNameForUid(callingUid);
    return allowedPackages.contains(callingPackage);
}
```

### Mitigation for Services

**Problem:** Services are exported and can be started by any app.

**Fix 1: Use local services only**
```xml
<service android:name=".LocalService"
         android:exported="false" />
```

**Fix 2: Add permission**
```xml
<service android:name=".SecureService"
         android:exported="true"
         android:permission="com.example.permission.USE_SERVICE" />
```

### Mitigation for Content Providers

**Problem:** Content providers are exported without permissions.

**Fix 1: Add read/write permissions**
```xml
<provider android:name=".UserProvider"
          android:authorities="com.example.users"
          android:exported="true"
          android:readPermission="com.example.permission.READ_USERS"
          android:writePermission="com.example.permission.WRITE_USERS" />
```

**Fix 2: Use signature permission (same developer only)**
```xml
<permission android:name="com.example.permission.READ_USERS"
            android:protectionLevel="signature" />
```

**Fix 3: Validate URIs in code**
```java
public Cursor query(Uri uri, ...) {
    if (!isValidUri(uri)) {
        throw new IllegalArgumentException("Invalid URI");
    }
    // Process query
}
```

### Mitigation for Broadcast Receivers

**Problem:** Receivers accept broadcasts from any app.

**Fix 1: Do not export**
```xml
<receiver android:name=".LocalReceiver"
          android:exported="false" />
```

**Fix 2: Use ordered broadcasts with permissions**

```java
sendOrderedBroadcast(intent, "com.example.permission.SEND");
```

**Fix 3: Validate broadcast data**
```java
public void onReceive(Context context, Intent intent) {
    if (!intent.hasExtra("expected_action")) {
        return; // Ignore invalid broadcasts
    }
}
```

## Mitigation for WebView

**Problem:** WebView is misconfigured, allowing XSS and file theft.

**Fix 1: Disable JavaScript if not needed**
```java
webView.getSettings().setJavaScriptEnabled(false);
```

**Fix 2: Disable file access**
```java
webView.getSettings().setAllowFileAccess(false);
webView.getSettings().setAllowFileAccessFromFileURLs(false);
webView.getSettings().setAllowContentAccess(false);
```

**Fix 3: Use HTTPS only**
```java
webView.loadUrl("https://secure.example.com");
```

**Fix 4: Validate URLs before loading**
```java
private boolean isValidUrl(String url) {
    return url != null &&
           url.startsWith("https://trusted-domain.com/");
}
```

## Mitigation for Backups

**Problem:** App data is exposed through ADB backup.

**Fix: Disable backup for sensitive apps**
```xml
<application android:allowBackup="false" ...>
```

**Or specify what can be backed up:**
```xml
<application android:fullBackupContent="@xml/backup_rules" ...>
```

In `res/xml/backup_rules.xml`:
```xml
<full-backup-content>
    <exclude domain="sharedpref" path="tokens.xml"/>
    <exclude domain="database" path="secret.db"/>
</full-backup-content>
```

## Mitigation for Intents

**Problem:** Intents expose data to other apps.

**Fix 1: Use explicit intents**
```java
// Bad - implicit intent
Intent intent = new Intent("com.example.SEND_DATA");
intent.putExtra("token", token);
startActivity(intent);

// Good - explicit intent
Intent intent = new Intent(this, TargetActivity.class);
intent.putExtra("token", token);
startActivity(intent);
```

**Fix 2: Do not put sensitive data in extras**
Pass a reference (like a content URI) instead of the actual data.

**Fix 3: Validate incoming intents**
```java
protected void onCreate(Bundle savedInstanceState) {
    Intent intent = getIntent();
    if (intent.hasExtra("admin_action")) {
        if (!isCallerAuthorized()) {
            finish();
            return;
        }
    }
}
```

## Mitigation for Deep Links

**Problem:** Deep links can be hijacked by malicious apps.

**Fix 1: Use Android App Links (verified links)**
```xml
<intent-filter android:autoVerify="true">
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="https"
          android:host="example.com" />
</intent-filter>
```

**Fix 2: Check caller package**
```java
private boolean isTrustedCaller() {
    String packageName = getCallingPackage();
    return "com.example.trustedapp".equals(packageName);
}
```

## Mitigation for Clipboard

**Problem:** Sensitive data copied to clipboard can be read by other apps.

**Fix: Do not copy sensitive data to clipboard**

```java
// Bad
ClipboardManager clipboard = getSystemService(CLIPBOARD_SERVICE);
clipboard.setPrimaryClip(ClipData.newPlainText("password", password));

// Good - Copy only if user explicitly requests
// And show a warning about clipboard sharing
```

## Security Checklist for Developers

| Check | Action |
|-------|--------|
| Exported components | Set exported=false unless required |
| Permissions | Use proper protection levels |
| WebView | Disable JavaScript, file access |
| Backups | Disable or limit backup content |
| Intents | Use explicit intents, validate data |
| Deep links | Use verified App Links |
| Clipboard | Avoid copying sensitive data |
| Notifications | Do not include sensitive data |
| FileProvider | Restrict paths in FileProvider XML |

## Summary

Mitigation involves following platform best practices. Do not export components unless necessary. Add permissions to exported components. Disable unnecessary WebView features. Disable backups for sensitive apps. Use explicit intents. Validate all incoming data. Use Android App Links for deep links. Do not put sensitive data in clipboard or notifications.