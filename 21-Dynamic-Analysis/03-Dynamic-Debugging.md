# Module 21: Dynamic Analysis - Dynamic Debugging

## Overview

Dynamic debugging means stepping through the app's code while it is running. This helps understand the exact execution flow.

## Setting Up Debugging

### Using Android Studio

**Step 1: Make the app debuggable**

Either use a debug build or modify the manifest:
```xml
<application android:debuggable="true" ...>
```

**Step 2: Attach debugger**

1. Run the app on the device
2. In Android Studio: Run -> Attach Debugger to Android Process
3. Select the app's process

### Using jdb (Command Line)

**Step 1: Find the process ID**
```
adb jdwp
```

**Step 2: Forward the debug port**
```
adb forward tcp:8700 jdwp:<PID>
```

**Step 3: Connect jdb**
```
jdb -attach localhost:8700
```

### Using smalidea (Smali Debugging)

1. Install smalidea plugin in IntelliJ
2. Open the smali project (from apktool decode)
3. Set breakpoints in smali files
4. Attach to running process

## What Debugging Reveals

- Variable values at runtime
- Which code paths are executed
- How conditions are evaluated
- Exception handling flow
- Authentication logic in action

## Debugging for Security Testing

**Bypassing checks:**
Set breakpoints before security checks and modify variable values to bypass them.

**Example:**
Breakpoint at `if (isAdmin)` -> Change variable to `true` -> Admin access granted.

## Summary

Dynamic debugging allows stepping through the app's code at runtime. Use Android Studio or jdb for Java code and smalidea for smali code. Debugging reveals variable values, code paths, and allows bypassing security checks for testing.