# Module 05: Reversing App with Apktool - Overview

## What is Apktool?

Apktool is a tool for reverse engineering Android APK files. It can decode APK files into a human-readable format. You can then modify the code and rebuild the APK.

Think of Apktool as a way to "unwrap" an Android app. The APK is a wrapped package. Apktool unwraps it so you can see what is inside and make changes.

## What is Reverse Engineering in Mobile Context?

Reverse engineering means taking an app and figuring out how it works. When you have an APK file, you do not have the original source code. The code is compiled into DEX bytecode. Reverse engineering converts this bytecode back into something you can read and understand.

**Why reverse engineer an app?**
- Find security vulnerabilities
- Understand how the app works
- Bypass security measures (for testing)
- Analyze malware behavior
- Remove restrictions (for research purposes)

## What Apktool Can Do

| Feature | Description |
|---------|-------------|
| Decode APK | Convert APK to readable format (smali + resources) |
| Build APK | Convert modified files back to APK format |
| Decode resources | Extract and decode all resources |
| Keep/decode multiple dex files | Handle apps with multiple dex files |
| Framework files | Install and use Android framework files |

## What Apktool Cannot Do

| Limitation | Explanation |
|------------|-------------|
| Cannot decompile to Java | Apktool produces smali, not Java code |
| Cannot modify DEX directly | For Java-level changes, use jadx + smali |
| Not a code editor | You need a separate editor to modify smali |
| Cannot fix all rebuild errors | Some APKs are protected against rebuilding |

## Why Use Apktool for Android Reversing?

**1. Complete Access to Resources**

Other tools might only extract code. Apktool extracts everything: layouts, images, strings, and code. You can modify any part of the app.

**2. Smali Code Modification**

Smali is the human-readable version of DEX bytecode. You can modify smali to change app behavior. For example, you can bypass a license check by changing a single instruction.

**3. Easy Rebuilding**

After modifying the app, Apktool can rebuild it into a working APK. You can then sign and install the modified app.

**4. Framework Support**

Apktool can include Android framework resources. This ensures the decoded files are complete and accurate.

## The Reversing Process with Apktool

```
Original APK
     |
     v
Decode with Apktool (apktool d app.apk)
     |
     v
Decoded Files (smali, resources, manifest)
     |
     v
Modify Files (change smali, edit XML, replace resources)
     |
     v
Rebuild with Apktool (apktool b app_folder)
     |
     v
Modified APK
     |
     v
Sign the APK (jarsigner or apksigner)
     |
     v
Install and Test
```

## Real-World Use Cases

**1. Bypassing SSL Pinning**

Many apps implement SSL pinning to prevent traffic interception. By modifying the smali code, you can bypass this protection during testing.

**2. Removing License Verification**

Some apps check if the user has paid for the app. You can modify the code to always return "verified."

**3. Analyzing Android Malware**

Malware tries to hide its behavior. Apktool helps you decode the malware and see what it does.

**4. Extracting Protected Content**

Some apps protect their content with encryption. You can find and remove the encryption in the smali code.

**5. Security Research**

Researchers use Apktool to find vulnerabilities in apps. They can then report these issues to the developers.

## Summary

Apktool is a powerful tool for reversing Android apps. It decodes APK files into smali code and resources. You can modify the files and rebuild the APK. It is essential for mobile penetration testing, malware analysis, and security research.