# Module 08: Scanning Vulnerability with Drozer - Overview

## What is Drozer?

Drozer (formerly Mercury) is a security testing tool for Android. It helps you find and exploit vulnerabilities in Android apps. Drozer works by installing a small agent app on the Android device. You control the agent from your computer using a command-line interface.

Think of Drozer as a remote control for Android security testing. You tell it what to do from your computer, and it performs actions on the Android device.

## How Drozer Works

```
+------------------+          +------------------+
|  Your Computer   |   TCP    |  Android Device  |
|  (Drozer Console)|<-------->|  (Drozer Agent) |
+------------------+          +------------------+
        |                              |
        | Commands                     | Actions
        v                              v
  app.activity.info              Actually starts
  app.provider.query             activities, queries
  app.service.start              providers, starts
                                 services on device
```

### Architecture

1. **Drozer Console** - Runs on your computer. This is the command-line interface you use to send commands.
2. **Drozer Agent** - Runs on the Android device. It receives commands from the console and executes them on the device.
3. **Communication** - The console connects to the agent over TCP (network connection).

## Why Drozer is Useful

**1. Component Testing**
Drozer can directly interact with Android components (activities, services, providers, receivers). It can start activities, query providers, and start services without the app's own UI.

**2. Attack Surface Discovery**
Drozer maps all the exported components of an app. This shows you the attack surface - what parts of the app other apps can access.

**3. Exploitation**
Drozer can exploit common vulnerabilities. It can perform SQL injection on content providers, launch exported activities, and access protected data.

**4. No Root Required (Most Cases)**
Many Drozer attacks work on non-rooted devices. This is useful for testing real user devices.

**5. Scriptable**
Drozer commands can be scripted. You can automate repetitive testing tasks.

## What Drozer Can Test

| Component | What Drozer Can Do |
|-----------|-------------------|
| Activities | List, start, find exported activities |
| Services | List, start, bind to services |
| Content Providers | List, query, insert, update, delete data |
| Broadcast Receivers | List, send broadcasts |
| Permissions | Check permission usage |
| Intents | Test intent injection |

## Installing Drozer

### Install Drozer on Kali

```
pip3 install drozer
```

### Install Drozer Agent on Android

1. Download the Drozer agent APK from GitHub
2. Install it on the device:
```
adb install drozer-agent.apk
```

## Starting Drozer

### Step 1: Start the Agent on Android

1. Open the Drozer Agent app on the device
2. Click **Enable** to start the server
3. Note the IP address and port (default: 31415)

### Step 2: Connect Using ADB (Alternative)

Forward the port using ADB:
```
adb forward tcp:31415 tcp:31415
```

### Step 3: Connect Console to Agent

```
drozer console connect
```

If using ADB forwarding:
```
drozer console connect --server 127.0.0.1
```

### Step 4: Verify Connection

```
dz> version
```

You should see the Drozer version number.

## Basic Drozer Commands

### Find Attack Surface

```
dz> run app.package.attacksurface com.example.app
```

This shows all exported components and potential attack vectors.

### List Activities

```
dz> run app.activity.info -a com.example.app
```

### List Services

```
dz> run app.service.info -a com.example.app
```

### List Content Providers

```
dz> run app.provider.info -a com.example.app
```

### List Broadcast Receivers

```
dz> run app.broadcast.info -a com.example.app
```

## Common Drozer Modules

| Module | Purpose |
|--------|---------|
| app.package.info | Get app package information |
| app.package.attacksurface | Find attack surface |
| app.activity.info | List activities |
| app.activity.start | Start an activity |
| app.service.info | List services |
| app.service.start | Start a service |
| app.provider.info | List content providers |
| app.provider.query | Query a content provider |
| app.provider.insert | Insert data into provider |
| app.provider.update | Update data in provider |
| app.provider.delete | Delete data from provider |
| app.broadcast.info | List broadcast receivers |
| app.broadcast.send | Send a broadcast |
| scanner.provider.injection | Test for SQL injection |
| scanner.provider.traversal | Test for directory traversal |
| scanner.activity.browser | Check browser activities |

## Example Drozer Session

```
dz> run app.package.list -f browser
com.android.browser
com.example.browserapp

dz> run app.package.info -a com.example.browserapp
Package: com.example.browserapp
  Application Label: BrowserApp
  Process Name: com.example.browserapp
  Version: 1.0
  Data Directory: /data/data/com.example.browserapp
  APK Path: /data/app/com.example.browserapp/base.apk
  UID: 10042
  GIDS: [3003]
  Shared Libraries: null
  SharedUserID: null
  Uses Permissions:
  - android.permission.INTERNET

dz> run app.package.attacksurface com.example.browserapp
Attack Surface:
  2 activities exported
  0 broadcast receivers exported
  1 content providers exported
  0 services exported
    is debuggable? true
    is backup enabled? true
```

This shows the app has exported activities, an exported content provider, and is debuggable with backup enabled.

## Summary

Drozer is a powerful tool for testing Android app security. It uses an agent on the device and a console on your computer. It can explore activities, services, providers, and receivers. It finds the attack surface and helps you exploit vulnerabilities. Most attacks work without root access on the device.