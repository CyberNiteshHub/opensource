# Module 21: Dynamic Analysis - How to Perform Dynamic Analysis

## Overview

This lesson covers the step-by-step process of performing dynamic analysis on a mobile app.

## Step 1: Set Up the Environment

**Requirements:**
- Rooted Android device or emulator
- Burp Suite for network interception
- Frida for runtime manipulation
- ADB for device interaction

**Setup checklist:**
```
[ ] Device is rooted
[ ] USB debugging enabled
[ ] ADB connection working
[ ] Burp Suite proxy configured
[ ] Frida installed on device and computer
```

## Step 2: Install the App

```
adb install app.apk
```

## Step 3: Observe App Behavior

**What to observe:**
- File system changes (what files are created?)
- Network traffic (what data is sent?)
- Process information (what services start?)
- Permission usage (what permissions are used?)

**Commands:**
```
# Monitor file changes
adb shell
inotifyd -r /data/data/com.example.app/

# Monitor logcat
adb logcat -s com.example.app

# Monitor network
adb shell tcpdump -i any
```

## Step 4: Interact with the App

- Log in
- Navigate through screens
- Perform sensitive actions (payments, data entry)
- Log out

## Step 5: Monitor Tool Output

Check Burp Suite for network requests. Check Frida for function calls.

## Step 6: Document Findings

Record all observed behavior, vulnerabilities, and issues.

## Summary

Dynamic analysis involves setting up a test environment, installing the app, observing behavior, interacting with the app, monitoring tools, and documenting findings. Key tools include Burp Suite for network, Frida for runtime, and ADB for device interaction.