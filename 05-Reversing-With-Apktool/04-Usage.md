# Module 05: Reversing App with Apktool - Usage

## Basic Commands

Apktool has a simple command structure. Here are the most common commands you will use.

## 1. Decoding an APK

The most basic command decodes an APK into a folder:

```
apktool d app.apk
```

This creates a folder called `app` with all decoded files.

### Specify Output Folder

```
apktool d app.apk -o myapp
```

### Force Overwrite

If the output folder already exists, use `-f` to overwrite:

```
apktool d app.apk -o myapp -f
```

### Decode Without Resources

Sometimes you only want the code, not the resources:

```
apktool d app.apk -r
```

### Decode Without DEX to Smali

If you only want the resources:

```
apktool d app.apk -s
```

## 2. Building an APK

After modifying the decoded files, rebuild them:

```
apktool b app_folder
```

This creates a new APK at `app_folder/dist/app.apk`.

### Specify Output File

```
apktool b app_folder -o modified.apk
```

### Use aapt2

Some apps require aapt2 for building:

```
apktool b app_folder --use-aapt2
```

## 3. Installing Framework Files

Some APKs reference system framework resources. Install the framework first:

```
apktool if framework-res.apk
```

To see installed frameworks:

```
apktool --list-frameworks
```

## Full Example: Decode, Modify, Rebuild, Sign

Let us walk through a complete example.

### Step 1: Decode the APK

```
apktool d example.apk -o example
```

Output:
```
I: Using Apktool 2.9.3 on example.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
I: Loading resource table from file: /home/kali/.local/share/apktool/framework/1.apk
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Baksmaling classes.dex...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...
```

### Step 2: Examine the Decoded Files

```
cd example
ls -la
```

You will see:
```
AndroidManifest.xml
apktool.yml
original/
res/
smali/
```

### Step 3: Explore Smali Files

```
ls smali/com/example/app/
```

Each Java class becomes a .smali file:
```
MainActivity.smali
LoginActivity.smali
DatabaseHelper.smali
Utils.smali
```

### Step 4: Modify a Smali File

Let us say you want to bypass a license check. Open the smali file:

```
nano smali/com/example/app/LicenseChecker.smali
```

**Original code:**
```smali
.method public isLicensed()Z
    .registers 2
    const/4 v0, 0x0
    return v0
.end method
```

This always returns false (not licensed). Change it to always return true:

```smali
.method public isLicensed()Z
    .registers 2
    const/4 v0, 0x1
    return v0
.end method
```

The change is `const/4 v0, 0x0` to `const/4 v0, 0x1`.

### Step 5: Rebuild the APK

```
cd ..
apktool b example -o modified.apk
```

Output:
```
I: Using Apktool 2.9.3
I: Checking whether sources has changed...
I: Smaling smali folder into classes.dex...
I: Checking whether resources has changed...
I: Building resources...
I: Building apk file...
I: Copying unknown files...
I: Built apk into: modified.apk
```

### Step 6: Sign the APK

The rebuilt APK is not signed. You need to sign it before installing.

**Using apksigner (recommended):**

Create a debug keystore (first time only):
```
keytool -genkey -v -keystore debug.keystore -alias debug -keyalg RSA -keysize 2048 -validity 10000
```

Sign the APK:
```
apksigner sign --ks debug.keystore --ks-key-alias debug modified.apk
```

**Using jarsigner (alternative):**
```
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore debug.keystore modified.apk debug
```

### Step 7: Install and Test

```
adb install modified.apk
```

## Common Smali Modifications

### Change True/False

```
const/4 v0, 0x0  -->  const/4 v0, 0x1  (false to true)
const/4 v0, 0x1  -->  const/4 v0, 0x0  (true to false)
```

### Remove Method Body

Replace the method body with:
```smali
return-void           (for void methods)
const/4 v0, 0x0       (for methods returning int/boolean)
return v0
```

### Skip a Check

Find the if condition and make it always jump:
```
if-eqz v0, :cond_0  -->  goto :cond_0
```

## Command Reference

| Command | Description |
|---------|-------------|
| `apktool d app.apk` | Decode APK |
| `apktool d app.apk -o folder` | Decode to specific folder |
| `apktool d app.apk -r` | Decode without resources |
| `apktool d app.apk -s` | Decode without DEX |
| `apktool b folder` | Build APK from decoded folder |
| `apktool b folder -o app.apk` | Build to specific file |
| `apktool b folder --use-aapt2` | Build using aapt2 |
| `apktool if framework.apk` | Install framework |
| `apktool --version` | Show version |
| `apktool --help` | Show help |

## Summary

Apktool usage is straightforward. Use `apktool d` to decode, modify the smali files, then use `apktool b` to rebuild. Always sign the rebuilt APK before installing. Common modifications include changing boolean values and skipping conditional checks.