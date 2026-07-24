# Module 03: Android Architecture - Layers of Android

## Introduction

Android is built in layers. Think of it like a cake. Each layer has a different job. The bottom layers talk to the hardware. The top layers talk to the user. Understanding these layers helps you understand where security problems can happen.

## Android Architecture Layers

```
+------------------------------------------+
|         System Apps                        |
|  (Phone, Contacts, Browser, Settings)     |
+------------------------------------------+
|         Java API Framework                |
|  (Activity Manager, Content Providers,    |
|   View System, Notification Manager)      |
+------------------------------------------+
|       Native C/C++ Libraries    | ART     |
|  (WebKit, OpenGL, SSL, SQLite)  |(Android |
|   Media Framework, Libc)        |Runtime) |
+------------------------------------------+
|      Hardware Abstraction Layer (HAL)     |
|  (Camera, Bluetooth, GPS, Audio, Sensors)|
+------------------------------------------+
|           Linux Kernel                    |
|  (Drivers, Memory Management, Security,   |
|   Power Management, Network Stack)        |
+------------------------------------------+
|           Hardware                        |
|  (CPU, RAM, Storage, Camera, Sensors)    |
+------------------------------------------+
```

## Layer 1: Linux Kernel (Bottom Layer)

The Linux Kernel is the heart of Android. It manages everything at the hardware level.

**What it does:**
- Manages memory (RAM)
- Manages processes (running apps)
- Handles device drivers (camera, Wi-Fi, Bluetooth)
- Provides security (user permissions, file access)
- Manages power (battery usage)

**Why it matters for security:**
The kernel enforces app isolation. Each app runs as its own user. One app cannot access another app's files. If someone exploits the kernel, they can control the entire device.

## Layer 2: Hardware Abstraction Layer (HAL)

HAL is a bridge between the hardware and the software. It provides a standard way for apps to talk to hardware.

**What it does:**
- Camera HAL (take photos)
- Audio HAL (play sound)
- Bluetooth HAL (connect to devices)
- Sensors HAL (read accelerometer, gyroscope)

**Why it matters for security:**
If a HAL component has a bug, an app could misuse hardware. For example, an app could turn on the camera without permission.

## Layer 3: Android Runtime (ART) and Native Libraries

### Android Runtime (ART)

ART runs the app code. Each app runs in its own process with its own ART instance.

**What it does:**
- Compiles app code to machine code
- Manages app memory (garbage collection)
- Runs the DEX bytecode

**Security feature:** Each app runs in a separate sandbox. One app cannot interfere with another app.

### Native C/C++ Libraries

These are system libraries written in C and C++. They provide core functionality.

**Important libraries:**
- **WebKit** - Renders web pages in WebView
- **OpenGL** - Renders 3D graphics
- **SQLite** - Manages local databases
- **SSL/TLS** - Handles secure network communication
- **Libc** - Standard C library

**Why it matters for security:** Bugs in native libraries can affect all apps. For example, a WebKit bug could let an attacker execute code through a WebView.

## Layer 4: Java API Framework

This is what app developers use. It provides all the building blocks for creating apps.

**Key Components:**
- **Activity Manager** - Manages app screens
- **Content Providers** - Shares data between apps
- **View System** - Creates user interface
- **Notification Manager** - Shows notifications
- **Package Manager** - Installs and manages apps
- **Telephony Manager** - Handles phone calls
- **Location Manager** - Provides GPS location

**Why it matters for security:** Developers must use these APIs correctly. Misusing them can create security holes. For example, exporting a Content Provider without proper permissions can leak data.

## Layer 5: System Apps

These are apps that come with the Android system. They include:

- Phone dialer
- Contacts
- Browser
- Settings
- Camera
- Gallery
- Messaging

**Why it matters for security:** System apps have special privileges. If a system app has a vulnerability, the impact is severe. For example, a bug in the Settings app could let an attacker change system settings.

## Security Implications of Each Layer

| Layer | Security Risk |
|-------|---------------|
| Linux Kernel | Kernel exploits give full device control |
| HAL | Hardware misuse (camera, microphone) |
| ART | Sandbox escape possible with exploits |
| Native Libraries | Library vulnerabilities affect all apps |
| Java Framework | API misuse by developers |
| System Apps | Privilege escalation through system apps |

## Summary

Android has multiple layers. The Linux Kernel at the bottom manages hardware. HAL provides hardware access. ART runs app code. Native libraries provide core functions. The Java Framework is what developers use. System apps are at the top. Security problems can exist at any layer. Understanding these layers helps you find and fix vulnerabilities.