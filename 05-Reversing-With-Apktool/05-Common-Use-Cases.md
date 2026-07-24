# Module 05: Reversing App with Apktool - Common Use Cases

## Real-World Scenarios

Apktool is used in many real-world situations. Here are the most common use cases with step-by-step explanations.

## Use Case 1: Bypassing SSL Pinning

SSL pinning prevents interception of network traffic. Many banking and secure apps use it. Here is how to bypass it with Apktool.

**Step 1: Decode the APK**

```
apktool d app.apk -o app
```

**Step 2: Search for SSL-related smali code**

```
grep -r "SSL\|pinning\|certificate\|TrustManager" app/smali/
```

**Step 3: Find the SSL pinning check**

Look for methods that check certificates. Common class names include:
- `SSLPinningInterceptor`
- `CertificatePinning`
- `TrustManager` that does not accept all certificates

**Step 4: Modify the smali code**

Find the method that validates certificates. Make it accept all certificates by returning true:

```smali
# Change this:
const/4 v0, 0x0
return v0

# To this:
const/4 v0, 0x1
return v0
```

**Step 5: Rebuild and sign**

```
apktool b app -o modified.apk
apksigner sign --ks debug.keystore modified.apk
adb install modified.apk
```

Now you can intercept traffic with Burp Suite.

## Use Case 2: Removing License Verification

Many paid apps check if the user has purchased the app. You can bypass this check.

**Step 1: Decode the APK**

```
apktool d app.apk -o app
```

**Step 2: Find the license check**

Search for keywords:
```
grep -r "licensed\|purchased\|premium\|trial\|expired" app/smali/
```

**Step 3: Analyze the license method**

The method usually returns a boolean. Find a method named something like:
- `isLicensed()`
- `isPurchased()`
- `verifyLicense()`

**Step 4: Modify the return value**

Change the method to always return true:

```smali
.method public isLicensed()Z
    .registers 2
    const/4 v0, 0x1    # Return true
    return v0
.end method
```

**Step 5: Rebuild and install**

```
apktool b app -o modified.apk
apksigner sign --ks debug.keystore modified.apk
adb install modified.apk
```

## Use Case 3: Modifying App Permissions

Some apps request unnecessary permissions. You can remove them.

**Step 1: Decode the APK**

```
apktool d app.apk -o app
```

**Step 2: Edit AndroidManifest.xml**

Open the manifest file and remove unwanted permission lines:

```xml
<!-- Remove this line -->
<uses-permission android:name="android.permission.READ_CONTACTS"/>
```

**Step 3: Rebuild and sign**

```
apktool b app -o modified.apk
```

## Use Case 4: Analyzing Android Malware

Malware tries to hide its behavior. Apktool helps you see what it does.

**Step 1: Decode the malware APK**

```
apktool d malware.apk -o malware
```

**Step 2: Examine the manifest**

Look for suspicious permissions:
```
grep "uses-permission" malware/AndroidManifest.xml
```

Red flags:
- `RECEIVE_SMS` (intercepting SMS)
- `READ_CONTACTS` (stealing contacts)
- `SYSTEM_ALERT_WINDOW` (overlay attacks)
- `BIND_ACCESSIBILITY_SERVICE` (keylogging)

**Step 3: Check for suspicious code**

```
grep -r "http://\|sendTextMessage\|getDeviceId\|C2D" malware/smali/
```

**Step 4: Look for obfuscation**

Malware often uses obfuscated class names like `a.a.a` or `b.c`. This is a sign of malicious code.

**Step 5: Find command and control (C2) servers**

```
grep -r "http" malware/smali/
```

This reveals the servers the malware connects to.

## Use Case 5: Removing Ads

Many free apps show advertisements. You can remove them.

**Step 1: Decode the APK**

```
apktool d app.apk -o app
```

**Step 2: Find ad-related code**

Search for ad SDK names:
```
grep -r "admob\|facebook\|startapp\|unityads" app/smali/
```

**Step 3: Modify or remove ad calls**

Find methods that show ads and either remove the calls or make them return immediately.

## Use Case 6: Recovering Lost Source Code

If you lost your source code but have the APK, Apktool can help recover it.

**Step 1: Decode the APK**

```
apktool d app.apk -o app
```

**Step 2: Use jadx to decompile**

```
jadx app.apk
```

This produces Java code that is close to the original source.

**Step 3: Extract resources**

All resources are in the `res/` folder. Layouts, strings, and images are all recoverable.

## Use Case 7: Changing App Language/Locale

You can add or modify language translations.

**Step 1: Decode the APK**

```
apktool d app.apk -o app
```

**Step 2: Edit strings.xml**

```
nano app/res/values/strings.xml
```

Add your translations:
```xml
<string name="welcome_message">Welcome to the app!</string>
```

**Step 3: Create a new locale folder**

```
mkdir app/res/values-hi  # Hindi
cp app/res/values/strings.xml app/res/values-hi/
nano app/res/values-hi/strings.xml
```

**Step 4: Rebuild**

## Summary Table

| Use Case | What to Modify | Key Files |
|----------|---------------|-----------|
| Bypass SSL Pinning | TrustManager smali | smali/*/TrustManager*.smali |
| Remove License | License check method | smali/*/License*.smali |
| Change Permissions | Manifest | AndroidManifest.xml |
| Analyze Malware | Code and manifest | smali/, AndroidManifest.xml |
| Remove Ads | Ad SDK calls | smali/*/Ad*.smali |
| Recover Code | Full APK | All files |
| Add Language | strings.xml | res/values/strings.xml |

These use cases show the power of Apktool for mobile penetration testing and security research.