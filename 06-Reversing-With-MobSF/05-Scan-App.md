# Module 06: Reversing App with MobSF - Scan the App with MobSF

## Step-by-Step Scanning Guide

This lesson walks you through scanning an APK with MobSF and understanding the results.

## Step 1: Start MobSF

If using Docker:
```
docker start mobsf
```

Then open `http://localhost:8000` in your browser.

## Step 2: Upload the APK

1. On the MobSF home page, click the upload area
2. Select your APK file
3. Click **Upload**

MobSF will start analyzing the APK. This may take a few minutes depending on the app size.

## Step 3: Scanning Progress

While scanning, MobSF shows progress:

```
[INFO] Starting Static Analysis...
[INFO] Extracting APK...
[INFO] Analyzing Android Manifest...
[INFO] Analyzing Java Code...
[INFO] Analyzing Native Libraries...
[INFO] Checking for Malware...
[INFO] Generating Report...
[INFO] Analysis Complete
```

## Step 4: Understanding Scan Results

After scanning, MobSF shows the results dashboard.

### App Information Section

This section shows basic information:
- **Package Name** - com.example.app
- **App Name** - App display name
- **Version** - Version code and name
- **Platform** - Android or iOS
- **File Size** - APK file size
- **MD5/SHA1/SHA256** - File hashes

### Security Score

MobSF assigns a security score:

| Score | Meaning |
|-------|---------|
| 0-20 | Very insecure |
| 21-40 | Insecure |
| 41-60 | Average |
| 61-80 | Good |
| 81-100 | Very secure |

### Finding Summary

The summary shows:
- **Critical** findings (red)
- **High** findings (orange)
- **Medium** findings (yellow)
- **Low** findings (blue)
- **Info** findings (gray)

## Step 5: Analyze Each Section

### Manifest Analysis

**What you see:**
- List of all exported components
- Permissions used by the app
- Backup and debug settings
- Deep link configurations

**What to look for:**
```
Exported Activities:
  - com.example.app.LoginActivity (No auth required)
  - com.example.app.AdminActivity (Should not be exported)

Permissions:
  - android.permission.READ_CONTACTS (Why needed?)
  - android.permission.SEND_SMS (High risk)
```

### Code Analysis

**What you see:**
- Hardcoded secrets found
- Insecure API usage
- Weak cryptography detected

**What to look for:**
```
Hardcoded Secrets:
  [HIGH] API Key found: sk_live_xxxxxxxxx at MainActivity.java:45
  [CRITICAL] Password found: admin123 at Config.java:12

Insecure WebView:
  [HIGH] JavaScript enabled at WebViewActivity.java:30
```

### Network Analysis

**What you see:**
- URLs found in the app
- HTTP vs HTTPS usage
- SSL pinning status

**What to look for:**
```
Network Endpoints:
  http://api.example.com (No HTTPS - INSECURE)
  https://secure.example.com (HTTPS - OK)

SSL Issues:
  TrustManager that accepts all certificates found
```

### Storage Analysis

**What you see:**
- Database files
- SharedPreferences usage
- External storage access

**What to look for:**
```
Insecure Storage:
  SQLite database without encryption
  SharedPreferences storing passwords
  External storage read/write enabled
```

### Malware Analysis

**What you see:**
- Known malware signatures matched
- Suspicious permission combinations
- Code obfuscation detection

## Step 6: Understanding Individual Findings

Each finding in MobSF includes:

```
Finding Title: Hardcoded API Key
Severity: HIGH
Category: Insecure Data Storage
Location: MainActivity.java:45
Description:
  An API key was found hardcoded in the source code.
  Hardcoded keys can be extracted by anyone with access
  to the APK file.
Impact:
  Attackers can use this API key to access backend services.
Remediation:
  Store API keys in a secure backend server or use
  Android Keystore for encryption.
```

## Step 7: Exporting Reports

### Export as PDF

1. Click **PDF Report** button
2. Save the PDF file

### Export as HTML

1. Click **HTML Report** button
2. MobSF opens the report in a new tab

### Export as JSON

1. Click **JSON Report** button
2. Save the JSON file

## Example Scan Walkthrough

Let us scan a sample app called "VulnerableBank.apk".

**Upload:** Drag and drop the APK onto MobSF.

**Wait:** Analysis takes about 2 minutes.

**Results:**

```
Security Score: 15/100 (Very Insecure)

Findings Summary:
  Critical: 8
  High: 12
  Medium: 5
  Low: 3
  Info: 2
```

**Top Findings:**

| Finding | Severity |
|---------|----------|
| Hardcoded database password | Critical |
| Weak encryption (AES/ECB) | Critical |
| Exported AdminActivity | High |
| HTTP traffic to API | High |
| SQL Injection in Content Provider | High |
| Backup enabled | Medium |
| Debug mode enabled | Low |

**Analysis:**

1. The app stores database credentials in the code. Anyone can extract them from the APK.
2. The app uses AES in ECB mode. ECB mode is not secure for encrypting data.
3. The AdminActivity is exported. Any app can open the admin screen.
4. The app communicates with the server over HTTP. Traffic can be intercepted.
5. The content provider is vulnerable to SQL injection.

**Remediation Recommendations:**

1. Remove hardcoded credentials from code.
2. Use AES-GCM instead of AES-ECB.
3. Set android:exported="false" for AdminActivity.
4. Use HTTPS instead of HTTP.
5. Sanitize database queries.

## Summary

Scanning with MobSF is straightforward. Upload the APK, wait for analysis, and review the results. Each finding includes severity, location, description, and remediation. Export reports as PDF, HTML, or JSON. Use the findings to fix vulnerabilities and improve app security.