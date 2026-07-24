# Module 04: APK File Structure - File Structure Example

## Real APK Walkthrough

In this lesson, we will look at a sample APK file and walk through each part. We will use a simple app called "SecureNote" (a note-taking app). This will show you what a real APK looks like and where to find important information.

## Step 1: Get the APK Information

First, we use aapt to get basic information about the APK:

```
aapt dump badging SecureNote.apk
```

**Output:**
```
package: name='com.example.securenote' versionCode='2' versionName='1.1'
sdkVersion:'24' targetSdkVersion:'30'
uses-permission: name='android.permission.INTERNET'
uses-permission: name='android.permission.READ_EXTERNAL_STORAGE'
uses-permission: name='android.permission.WRITE_EXTERNAL_STORAGE'
uses-permission: name='android.permission.ACCESS_NETWORK_STATE'
application-label:'SecureNote'
application-icon-120:'res/mipmap-anydpi-v26/ic_launcher.xml'
application-icon-160:'res/mipmap-anydpi-v26/ic_launcher.xml'
application: label='SecureNote' icon='res/mipmap-anydpi-v26/ic_launcher.xml'
launchable-activity: name='com.example.securenote.MainActivity'
```

**What we learn:**
- Package name: com.example.securenote
- This is version 1.1
- Requires Android 7.0 (API 24) or higher
- Requests INTERNET and STORAGE permissions
- Main activity is MainActivity

## Step 2: Decode the APK with Apktool

```
apktool d SecureNote.apk -o SecureNote/
```

This creates a folder with all decoded files.

## Step 3: Examine the AndroidManifest.xml

```
cat SecureNote/AndroidManifest.xml
```

**What we find:**

```xml
<manifest package="com.example.securenote">
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="SecureNote"
        android:supportsRtl="true"
        android:theme="@style/Theme.SecureNote">

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>

        <activity
            android:name=".NoteDetailActivity"
            android:exported="true"/>

        <provider
            android:name=".NoteProvider"
            android:authorities="com.example.securenote.notes"
            android:exported="true"/>

        <service
            android:name=".SyncService"
            android:exported="false"/>
    </application>
</manifest>
```

**Security issues found:**
1. `android:allowBackup="true"` - App data can be backed up via ADB
2. `NoteDetailActivity` is exported - Any app can open it
3. `NoteProvider` is exported - Any app can read/write notes
4. No custom permissions defined for the provider

## Step 4: Examine the Resources

### Check strings.xml

```
cat SecureNote/res/values/strings.xml
```

**What we find:**
```xml
<resources>
    <string name="app_name">SecureNote</string>
    <string name="api_endpoint">https://api.securenote.com/v1/</string>
    <string name="api_key">THIS_IS_A_FAKE_KEY_FOR_EXAMPLE</string>
    <string name="encryption_key">ThisIsASecretKey123!</string>
</resources>
```

**Security issues found:**
1. API endpoint is hardcoded
2. API key is hardcoded in plain text
3. Encryption key is hardcoded in plain text

## Step 5: Examine the Code with jadx

```
jadx SecureNote.apk
```

**What we find in MainActivity.java:**
```java
public class MainActivity extends AppCompatActivity {
    private String userPassword;
    private SQLiteDatabase database;

    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Open database without encryption
        this.database = openOrCreateDatabase("notes.db", MODE_PRIVATE, null);

        // Store password in SharedPreferences (plain text)
        SharedPreferences prefs = getSharedPreferences("secure_prefs", MODE_PRIVATE);
        prefs.edit().putString("password", this.userPassword).apply();
    }
}
```

**Security issues found:**
1. Database is not encrypted
2. Password is stored in SharedPreferences (plain text)
3. Database is created with MODE_PRIVATE but content provider is exported

## Step 6: Check Native Libraries

```
ls -la SecureNote/lib/
```

**Output:**
```
lib/
  arm64-v8a/
    libcrypto.so
  armeabi-v7a/
    libcrypto.so
```

Use strings command to check for hardcoded data:
```
strings SecureNote/lib/arm64-v8a/libcrypto.so | grep -i "key\|secret\|password"
```

**What we find:**
```
aes_decrypt_key_128
master_key_2024
```

## Step 7: Check Assets Folder

```
ls -la SecureNote/assets/
```

**Output:**
```
assets/
  databases/
    initial_data.db
  config.json
```

**Check config.json:**
```json
{
    "server_url": "https://dev-api.securenote.com",
    "debug_mode": true,
    "admin_token": "admin_token_12345"
}
```

**Security issues found:**
1. Debug mode is enabled in release build
2. Admin token is exposed
3. Development server URL exposed

## Step 8: Check META-INF

```
keytool -printcert -jarfile SecureNote.apk
```

**Output:**
```
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 1
Valid from: Mon Jan 01 12:00:00 UTC 2024 until: Tue Jan 01 12:00:00 UTC 2025
Certificate fingerprints:
         MD5:  A1:B2:C3:D4:E5:F6:...
         SHA1: 12:34:56:78:90:AB:...
         SHA256: 98:76:54:32:10:FE:...
```

**Security issue found:** The app is signed with a debug certificate (Android Debug), not a release certificate.

## Summary of All Findings

| Location | Issue | Severity |
|----------|-------|----------|
| AndroidManifest.xml | allowBackup=true | Medium |
| AndroidManifest.xml | Exported NoteDetailActivity | High |
| AndroidManifest.xml | Exported NoteProvider | High |
| strings.xml | Hardcoded API key | Critical |
| strings.xml | Hardcoded encryption key | Critical |
| MainActivity.java | Unencrypted database | High |
| MainActivity.java | Plain text password storage | Critical |
| libcrypto.so | Master key exposed | Critical |
| config.json | Debug mode enabled | Medium |
| config.json | Admin token exposed | Critical |
| META-INF | Debug certificate used | High |

This example shows how a real APK can have many security problems. A penetration tester would examine each part of the APK and document these findings.