# Module 18: Extraneous Functionality - Code Review and Refactoring

## Overview

Code review and refactoring help identify and remove extraneous functionality. This lesson covers how to review code for extra features and how to remove them.

## Code Review for Extraneous Functionality

### What to Look For

**1. Debug Flags**
```java
if (BuildConfig.DEBUG) { ... }
if (isDebugMode()) { ... }
```

**2. Test Code**
```java
// TODO: Remove before release
// Test code
```

**3. Hidden Features**
```java
if (username.equals("admin") && password.equals("test")) { ... }
```

**4. Unused Imports and Methods**
```java
import com.example.test.*; // Test library imported but unused
```

**5. Debug Logging**
```java
Log.d("DEBUG", "User password: " + password);
```

### Code Review Checklist

```
[ ] All debug flags set to false
[ ] No debug/test activities in manifest
[ ] No test API endpoints in code
[ ] No hidden backdoors or admin accounts
[ ] No hardcoded test credentials
[ ] No developer shortcuts or gestures
[ ] No unused AndroidManifest.xml entries
[ ] No test libraries in release build
```

## Refactoring to Remove Extraneous Code

### Remove Debug Code

**Before:**
```java
public class MainActivity {
    public void onCreate() {
        if (BuildConfig.DEBUG) {
            enableDebugMenu();
            showDebugInfo();
        }
        // Normal code
    }
}
```

**After:**
```java
public class MainActivity {
    public void onCreate() {
        // Debug code removed
        // Normal code
    }
}
```

### Remove Test Activities

**Before (AndroidManifest.xml):**
```xml
<activity android:name=".TestActivity"/>
<activity android:name=".DebugSettingsActivity"/>
```

**After:**
```xml
<!-- Test activities removed -->
```

### Use Build Variants Properly

```groovy
android {
    buildTypes {
        debug {
            // Debug code here
            buildConfigField "boolean", "DEBUG_MODE", "true"
        }
        release {
            // No debug code
            buildConfigField "boolean", "DEBUG_MODE", "false"
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt')
        }
    }
}
```

## Summary

Code review and refactoring help find and remove extraneous functionality. Look for debug flags, test code, hidden features, unused imports, and debug logging. Use build variants to separate debug and release code. Always remove test code before releasing the app.