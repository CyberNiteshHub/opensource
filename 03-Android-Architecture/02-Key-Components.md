# Module 03: Android Architecture - Key Components

## Overview

Android apps are built from four main components. Each component has a specific job. Understanding these components is essential for security testing. Most vulnerabilities are found in how these components are used.

## The Four Main Components

```
+------------------------------------------+
|           Android App Components          |
|                                           |
|  +-------------+  +-------------+        |
|  |  Activity   |  |   Service   |        |
|  | (UI Screen) |  | (Background) |        |
|  +-------------+  +-------------+        |
|                                           |
|  +-------------+  +-------------+        |
|  |  Broadcast  |  |   Content   |        |
|  |  Receiver   |  |   Provider  |        |
|  | (Events)    |  | (Data Share)|        |
|  +-------------+  +-------------+        |
+------------------------------------------+
```

## 1. Activities

An Activity represents one screen in the app. If your app has three screens, it probably has three Activities.

**Example:**
- LoginActivity (the login screen)
- HomeActivity (the main screen after login)
- ProfileActivity (the user profile screen)

**How Activities Work:**
1. The user taps an icon
2. Android starts the main Activity
3. The Activity shows the UI
4. The user interacts with the screen
5. When done, the Activity closes

**Security Concerns:**
- **Exported Activities** - An activity that other apps can start. If a sensitive activity is exported, any app can open it without permission.
- **Intent Injection** - An activity receives data through Intents. If not validated, attackers can send malicious data.
- **Sensitive Data in Intents** - Passing passwords or tokens in Intent extras can be intercepted.

**Testing with Drozer:**
```
dz> run app.activity.info -a com.example.app
```
This shows all exported activities.

## 2. Services

A Service runs in the background. It does not have a user interface. Services keep working even if the user switches to another app.

**Example:**
- MusicService (plays music in the background)
- SyncService (syncs data with a server)
- NotificationService (checks for new messages)

**Types of Services:**
- **Started Service** - Runs until the task is done
- **Bound Service** - Runs while another component is connected

**Security Concerns:**
- **Exported Services** - Any app can start or bind to the service
- **Service Hijacking** - Malicious app starts your service repeatedly to drain battery
- **Data Leakage** - Services that return sensitive data to any caller

**Testing with Drozer:**
```
dz> run app.service.info -a com.example.app
```

## 3. Broadcast Receivers

A Broadcast Receiver listens for system-wide events. When an event happens, Android sends a broadcast. The receiver responds to it.

**Example:**
- BootReceiver (detects phone boot)
- SmsReceiver (detects incoming SMS)
- BatteryReceiver (detects low battery)

**Types:**
- **Static** - Declared in AndroidManifest.xml
- **Dynamic** - Registered in code at runtime

**Security Concerns:**
- **Exported Receivers** - Any app can send broadcasts to your receiver
- **Broadcast Injection** - Attackers send fake broadcasts with malicious data
- **Data Leakage** - Receiver processing sensitive data from broadcasts

**Testing with Drozer:**
```
dz> run app.broadcast.info -a com.example.app
```

## 4. Content Providers

A Content Provider shares data between apps. It is like a database that other apps can query. Contacts, Calendar, and Media Store are examples of system content providers.

**Example:**
- ContactsProvider (shares contact list)
- MediaProvider (shares photos and videos)
- CustomProvider (app-specific data sharing)

**How They Work:**
Content Providers use a URI (Uniform Resource Identifier) to identify data:

```
content://com.example.app.provider/users
```

**Security Concerns:**
- **Exported Providers** - Any app can read/write your data
- **SQL Injection** - If the provider uses SQLite, attackers can inject SQL commands
- **Directory Traversal** - Accessing files outside the intended directory
- **Mass Data Theft** - Reading all data from an unprotected provider

**Testing with Drozer:**
```
dz> run app.provider.info -a com.example.app
```

## Intents: The Glue Between Components

Intents are messages that components use to communicate. When one component wants to start another, it sends an Intent.

**Types of Intents:**
- **Explicit Intent** - Specifies the exact component to start
- **Implicit Intent** - Specifies an action; Android finds matching components

**Intent Example:**
```java
Intent intent = new Intent(this, SecondActivity.class);
intent.putExtra("user_id", "12345");
startActivity(intent);
```

**Security Concerns:**
- **Intent Sniffing** - Other apps can see implicit intents
- **Intent Spoofing** - Attackers send fake intents to exported components
- **Unvalidated Intent Data** - Not checking data received via intents

## Summary Table

| Component | Purpose | UI | Security Risk |
|-----------|---------|----|---------------|
| Activity | Screen display | Yes | Exported activities, intent injection |
| Service | Background work | No | Service hijacking, data leakage |
| Broadcast Receiver | Listen for events | No | Broadcast injection, spoofing |
| Content Provider | Share data | No | SQL injection, data theft |
| Intent | Communicate | No | Sniffing, spoofing |

Understanding these components is the foundation of Android security testing. Most vulnerabilities involve one of these components being misconfigured or misused.