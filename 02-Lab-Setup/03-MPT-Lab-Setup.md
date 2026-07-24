# Module 02: Lab Setup - Mobile Penetration Testing Lab Setup

## Full Lab Architecture

A complete mobile pentesting lab looks like this:

```
+------------------+       +------------------+       +------------------+
|   Kali Linux     |       |  Android Device  |       |   Target Server  |
|  (Testing Machine)|<---->|   (Test Target)  |<---->|   (API/Backend)  |
+------------------+       +------------------+       +------------------+
        |                           |
        |    Same Network           |   Internet
        +---------------------------+
                  |
           Burp Suite Proxy
           (On Kali or Separate)
```

All devices must be on the same network. The Android device sends traffic through Burp Suite on Kali. Kali forwards the traffic to the target server.

## Setting Up Android Emulator (Android Studio AVD)

An Android Virtual Device (AVD) is an emulated Android phone running on your computer.

### Step 1: Install Android Studio

1. Download Android Studio from developer.android.com
2. Extract the archive
3. Run `./android-studio/bin/studio.sh`
4. Complete the setup wizard
5. Install Android SDK (API 30 or higher)

### Step 2: Create an AVD

1. In Android Studio, click on **AVD Manager** (phone icon)
2. Click **Create Virtual Device**
3. Select a device (Pixel 4 is a good choice)
4. Click **Next**
5. Select a system image (Android 12 or 13, x86_64)
6. Download the image if not already installed
7. Click **Next**
8. Name your AVD and click **Finish**

### Step 3: Configure AVD for Testing

Create the AVD with these settings:

| Setting | Value |
|---------|-------|
| RAM | 2048 MB |
| VM heap | 256 MB |
| Internal storage | 2048 MB |
| SD card | 512 MB |
| Emulated Performance | Hardware - GLES 2.0 |

### Step 4: Enable Root Access

For testing purposes, use a rooted AVD:

1. Use a system image with Google APIs (not Google Play)
2. These images have root access via ADB

To verify root:

```
adb shell
su
```

If you get a `#` prompt, you have root access.

## Setting Up a Physical Android Device

Sometimes an emulator is not enough. You may need a physical device.

### Requirements

- An Android phone (old phones work well)
- USB cable
- Developer options enabled
- USB debugging enabled

### Step 1: Enable Developer Options

1. Go to **Settings** -> **About Phone**
2. Tap **Build Number** 7 times
3. You will see "You are now a developer"

### Step 2: Enable USB Debugging

1. Go to **Settings** -> **Developer Options**
2. Turn on **USB Debugging**
3. Connect the phone to your computer via USB
4. Accept the RSA key fingerprint prompt on the phone

### Step 3: Verify Connection

```
adb devices
```

You should see your device listed.

### Step 4: Rooting the Device (Optional but Recommended)

For a complete test, you need root access. Popular rooting methods:

- **Magisk** - Systemless root (recommended)
- **KingRoot** - Easy but less reliable
- **LineageOS** - Custom ROM with built-in root

**Warning:** Rooting voids warranty and can brick the device. Use an old dedicated testing phone.

## Connecting Device/Emulator to Burp Proxy

### For Emulator:

1. In emulator settings, set proxy to `10.0.2.2:8080`
2. Install Burp CA certificate (as shown in previous lesson)

### For Physical Device:

1. Connect device and Kali to the same Wi-Fi network
2. Set Wi-Fi proxy to Kali's IP address and port 8080
3. Install Burp CA certificate

## Installing Tools on Kali for MPT

Run these commands to install all essential tools:

```
# Basic tools
sudo apt update
sudo apt install apktool jadx adb dex2jar -y

# Python tools
pip3 install frida-tools objection drozer

# Docker for MobSF
sudo apt install docker.io -y
sudo docker pull opensecurity/mobile-security-framework-mobsf

# Network tools
sudo apt install burpsuite wireshark nmap -y

# Android Studio (download from website)
# Or install via:
sudo apt install android-sdk -y
```

## Testing End-to-End Connectivity

Follow this checklist to verify everything works:

### Network Connectivity

1. Ping the Android device from Kali:
   ```
   ping <android-ip>
   ```

2. Ping Kali from Android (use Termux app):
   ```
   ping <kali-ip>
   ```

### Proxy Connectivity

1. Start Burp Suite on Kali
2. Set proxy on Android
3. Open browser on Android
4. Go to `http://burpsuite`
5. You should see the Burp Suite page

### ADB Connectivity

1. Connect device via USB
2. Run `adb devices`
3. You should see the device listed

### App Installation

1. Download a test APK
2. Install it: `adb install test.apk`
3. The app should appear on the device

## Sample Lab Setup Script

Save this as `setup-mpt-lab.sh` on Kali:

```bash
#!/bin/bash
echo "Setting up MPT Lab..."
echo "Updating system..."
sudo apt update && sudo apt upgrade -y

echo "Installing tools..."
sudo apt install apktool jadx adb -y
pip3 install frida-tools

echo "Starting ADB server..."
adb start-server

echo "Checking devices..."
adb devices

echo "Lab setup complete!"
```

## Summary

A proper lab setup includes Kali Linux, an Android device/emulator, Burp Suite, and all necessary tools. The Android device and Kali must be on the same network for proxy testing. Use a rooted emulator for easier testing. Install the Burp CA certificate to intercept HTTPS traffic. Verify all connections work before starting the actual test.