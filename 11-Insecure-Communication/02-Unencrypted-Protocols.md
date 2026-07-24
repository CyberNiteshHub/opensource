# Module 11: Insecure Communication - Unencrypted Protocols

## Overview

Unencrypted protocols send data in plain text. Anyone who can see the network traffic can read the data. This lesson covers common unencrypted protocols used in mobile apps.

## 1. HTTP (Hypertext Transfer Protocol)

HTTP is the most common unencrypted protocol. It sends all data as plain text.

**How HTTP works:**
```
App -----------------> Server
       GET /api/login
       username=admin
       password=secret123
       (ALL IN PLAIN TEXT)
```

**What an attacker sees:**
```
GET http://api.example.com/login HTTP/1.1
Host: api.example.com
Authorization: Basic YWRtaW46c2VjcmV0MTIz
User-Agent: MobileApp/1.0

username=admin&password=secret123
```

**Everything is visible:**
- URL and endpoint
- Request parameters
- Headers (including auth tokens)
- Response data
- Cookies

### How to Detect HTTP Usage

**Static analysis:**
```java
// Look for these patterns in code:
new URL("http://api.example.com")
HttpURLConnection.connect()
OkHttp client without HTTPS
```

**Dynamic analysis:**
```
1. Set up Burp Suite proxy
2. Monitor traffic from the app
3. Look for "http://" URLs
```

## 2. FTP (File Transfer Protocol)

FTP transmits files and authentication in plain text.

**How FTP works:**
```
App -> Server
USER anonymous
PASS password123
RETR file.txt
(ALL IN PLAIN TEXT)
```

**What is exposed:**
- Username and password
- File names and content
- Directory structure

## 3. SMTP (Simple Mail Transfer Protocol)

SMTP without TLS sends email data in plain text.

**Vulnerable code:**
```java
Properties props = new Properties();
props.put("mail.smtp.host", "smtp.example.com");
props.put("mail.smtp.port", "25"); // Port 25 = no encryption
Session session = Session.getInstance(props);
```

**What is exposed:**
- Email content
- Attachments
- Recipient addresses
- Authentication credentials

## 4. WebSocket (ws://)

WebSocket without encryption (ws://) is vulnerable to MITM.

**Vulnerable code:**
```java
WebSocket ws = new WebSocket("ws://chat.example.com");
ws.send("Hello, this is a private message");
```

**What is exposed:**
- All messages sent and received
- Authentication data
- Session information

## 5. DNS (Domain Name System)

DNS queries are usually unencrypted. They reveal which servers the app connects to.

**What is exposed:**
- Every domain the app contacts
- Frequency of connections
- App behavior patterns

## 6. Custom Protocols

Some apps implement their own protocols without encryption.

**Example:**
```java
Socket socket = new Socket("server.example.com", 12345);
OutputStream out = socket.getOutputStream();
out.write("LOGIN:admin:secret123".getBytes());
```

**What is exposed:**
- Custom protocol commands
- Authentication data
- Application data

## Real-World Examples

### Case 1: HTTP API Login

A social media app used HTTP for its login API.

```
POST http://socialapp.example.com/login
username=johndoe&password=MyP@ss123

Response: {"token": "abc123xyz"}
```

An attacker on the same Wi-Fi captured the password and session token.

### Case 2: FTP for Image Upload

A photo editing app used FTP to upload images.

```
USER photouser
PASS upload123
STOR /photos/vacation.jpg
```

Attackers captured uploaded photos and user credentials.

### Case 3: Unencrypted Chat

A messaging app used WebSocket without encryption.

```
ws://chat.example.com/connect
{"user": "john", "message": "My credit card is 4111-1111-1111-1111"}
```

All messages were visible on the network.

## How to Test for Unencrypted Protocols

### Step 1: Set Up Traffic Interception

```
1. Install Burp Suite on Kali
2. Configure proxy on the test device
3. Route all traffic through Burp
```

### Step 2: Monitor Traffic

In Burp Suite, check the "HTTP History" tab. Look for:
- "http://" URLs (not https)
- Non-standard ports (80, 21, 25, etc.)
- Raw TCP connections

### Step 3: Use Wireshark for Non-HTTP Traffic

For non-HTTP protocols (FTP, SMTP, custom protocols):

```
1. Start Wireshark on Kali
2. Capture on the network interface
3. Filter by IP address of the target server
4. Look for plain text protocols
```

### Step 4: Check Source Code

Look for:
```java
// HTTP
URL url = new URL("http://example.com");

// FTP
URL url = new URL("ftp://example.com");

// SMTP without TLS
props.put("mail.smtp.port", "25");

// WebSocket without SSL
new WebSocket("ws://example.com");

// Raw socket
new Socket("example.com", 1234);
```

## Impact of Unencrypted Protocols

| Protocol | Data Exposed | Risk Level |
|----------|-------------|------------|
| HTTP | All request/response data | Critical |
| FTP | Files and credentials | Critical |
| SMTP (no TLS) | Emails and credentials | High |
| WebSocket (ws) | All messages | High |
| DNS | Server domains | Medium |
| Custom protocols | Application data | Variable |

## Prevention

**Always use encrypted alternatives:**
- HTTP -> HTTPS
- FTP -> SFTP or FTPS
- SMTP -> SMTPS (port 465) or STARTTLS (port 587)
- WebSocket (ws) -> WebSocket Secure (wss)
- DNS -> DNS over HTTPS (DoH) or DNS over TLS (DoT)
- Custom protocols -> Implement TLS on top

## Summary

Unencrypted protocols expose all data to anyone on the network. HTTP, FTP, SMTP, WebSocket, and custom protocols send data in plain text. Attackers on the same Wi-Fi can read, modify, and steal data. Always use encrypted versions of these protocols (HTTPS, SFTP, SMTPS, WSS).