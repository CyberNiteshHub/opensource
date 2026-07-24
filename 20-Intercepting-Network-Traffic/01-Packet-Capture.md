# Module 20: Intercepting Network Traffic - Packet Capture

## What is Packet Capture?

Packet capture means recording network traffic for analysis. In mobile testing, we capture packets sent between the app and servers to find security issues.

## Tools for Packet Capture

### 1. Burp Suite

Burp Suite is the most common tool for mobile traffic interception.

**How it works:**
```
App -> Burp Suite Proxy -> Internet -> Server
         |
         v
    You can see and modify all traffic
```

**Setup:**
1. Set Burp to listen on port 8080
2. Configure device proxy to point to Burp
3. Install Burp CA certificate on device
4. Traffic flows through Burp

### 2. tcpdump

tcpdump captures raw network packets on the device.

**Usage:**
```
adb shell
su
tcpdump -i any -s 0 -w /sdcard/capture.pcap
```

**Transfer to computer:**
```
adb pull /sdcard/capture.pcap
```

### 3. Wireshark

Wireshark analyzes captured packet files (PCAP).

**Usage:**
```
wireshark capture.pcap
```

### 4. mitmproxy

An open-source interactive HTTPS proxy.

## Setting Up Packet Capture

### Step 1: Configure Proxy on Android

1. Connect device and computer to same network
2. In Wi-Fi settings, set proxy to computer's IP and port 8080

### Step 2: Install CA Certificate

For Burp Suite:
1. Browse to http://burpsuite
2. Download the CA certificate
3. Install in device settings

### Step 3: Start Capturing

Burp Suite automatically captures all HTTP/HTTPS traffic.

## Summary

Packet capture records network traffic between the app and servers. Burp Suite is the primary tool for mobile testing. Setup involves configuring the device proxy and installing the CA certificate.