# Module 03: Android Architecture - Application Lifecycle

## What is the Application Lifecycle?

Every Android app goes through different states during its lifetime. The app starts, runs, pauses, stops, and eventually ends. This is called the lifecycle. Understanding the lifecycle is important for security because sensitive data can leak when apps move between states.

## Activity Lifecycle

An Activity (a screen) has six main states:

```
         +-------+
         | Start |
         +---+---+
             |
             v
       +-----+------+
       |  onCreate() |
       +-----+------+
             |
             v
       +-----+------+
       | onStart()  |
       +-----+------+
             |
             v
       +-----+------+
       | onResume() |
       +-----+------+
             |
         +---+---+
         |       |
         v       v
   +----+--+  +--+----+
   |onPause|  |Running|
   +----+--+  +--+----+
         |       |
         v       |
   +----+--+    |
   |onStop |<---+
   +----+--+
         |
         v
   +-----+------+
   |onDestroy() |
   +------------+
```

### State 1: onCreate()

This is called when the Activity is first created. It happens only once in the Activity's life.

**What happens:**
- The layout is loaded
- Variables are initialized
- Click listeners are set up

**Security concern:** If sensitive data is initialized here and not cleaned up, it stays in memory.

### State 2: onStart()

This is called when the Activity becomes visible to the user. The Activity is not yet interactive.

**What happens:**
- The UI becomes visible
- Animations start

**Security concern:** If the app shows sensitive data, it is now visible. Other apps on the screen (like overlay apps) could capture this data.

### State 3: onResume()

This is called when the Activity is ready for user interaction. The user can now tap buttons and enter text.

**What happens:**
- The Activity is in the foreground
- User can interact with it
- This is where the app spends most of its time

### State 4: onPause()

This is called when the Activity is about to go into the background. Another Activity is coming to the foreground.

**What happens:**
- The app saves its current state
- Animations are paused
- The app should release resources

**Security concern:** If the app does not clear sensitive data (like passwords from text fields) in onPause(), the next Activity can see it.

### State 5: onStop()

This is called when the Activity is no longer visible. The app is in the background.

**What happens:**
- The UI is not visible
- The app may be killed by the system if memory is low

**Security concern:** Screenshots of the app may be saved by the system. Android takes a screenshot when the app goes to the background. This screenshot can contain sensitive data.

### State 6: onDestroy()

This is called before the Activity is destroyed. This is the final cleanup step.

**What happens:**
- All resources are released
- The Activity object is removed from memory

**Security concern:** If sensitive data was not cleared before onDestroy(), it remains in memory until garbage collection runs.

## Why Lifecycle Matters for Security

### Problem 1: Task Switching and Screenshots

When you switch apps, Android takes a screenshot of the current screen. This screenshot appears in the recent apps list.

**The risk:** If your app shows sensitive data (bank balance, medical records), that data appears in the recent apps list.

**The fix:**
```java
getWindow().setFlags(
    WindowManager.LayoutParams.FLAG_SECURE,
    WindowManager.LayoutParams.FLAG_SECURE
);
```
This flag prevents Android from taking screenshots of your app.

### Problem 2: Data in Background

When an app goes to the background, it might still hold sensitive data in memory.

**The risk:** Another app with root access can read the first app's memory and steal data.

**The fix:** Clear sensitive data in onPause() or onStop().

### Problem 3: Activity State Saved

Android saves the Activity state before destroying it. This state is saved in a Bundle.

**The risk:** If sensitive data is saved in the state Bundle, it can be recovered by attackers.

**The fix:** Do not save sensitive data in onSaveInstanceState().

## Service Lifecycle

Services have a simpler lifecycle:

```
         +-------+
         | Start |
         +---+---+
             |
             v
       +-----+------+
       | onCreate() |
       +-----+------+
             |
             v
       +-----+------+
       | onStart()  |
       +-----+------+
             |
             v
      +------+------+
      | Running     |
      | (Background)|
      +------+------+
             |
             v
       +-----+------+
       | onDestroy() |
       +------------+
```

**Security concern:** Services that run too long can drain battery. Services that hold sensitive data in memory are a risk.

## Application Class Lifecycle

The Application class runs before any Activity:

```
onCreate() -> Activities start -> onTerminate()
```

**Security concern:** If the Application class initializes sensitive data (like API keys), they are in memory for the entire app lifetime.

## Testing Lifecycle Issues

| Test | How to Do It |
|------|-------------|
| Screenshot leak | Switch to recent apps, check for sensitive data |
| Data in onPause | Set breakpoint in onPause, check what data is retained |
| State leak | Rotate the phone (causes destroy/create cycle) |
| Background data | Use Frida to dump app memory after going to background |
| Task snapshot | Check the recent apps list for sensitive screens |

## Summary

The Android app lifecycle has multiple states. Each state transition can leak data if not handled properly. Clear sensitive data when the app goes to the background. Use FLAG_SECURE to prevent screenshots. Do not save sensitive data in saved instance state. Test all lifecycle transitions for data leakage.