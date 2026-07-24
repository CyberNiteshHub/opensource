# Module 02: Lab Setup - Burp Suite Setup

## What is Burp Suite?

Burp Suite is a proxy tool. It sits between your phone and the internet. When the app sends data to a server, Burp Suite captures that data. You can see it, modify it, and send it again.

Think of it like this:

```
Phone App --> Burp Suite --> Internet --> Server
                |
                v
           You can see and modify data here
```

Burp Suite is the most important tool for mobile penetration testing. You will use it in almost every test.

## Installing Burp Suite on Kali

### Step 1: Download Burp Suite

1. Open Firefox in Kali
2. Go to the PortSwigger website (portswigger.net)
3. Download the Community Edition (free version)
4. Choose the Linux version

### Step 2: Install Java

Burp Suite needs Java to run. Check if Java is installed:

```
java -version
```

If Java is not installed, run:

```
sudo apt install default-jre -y
```

### Step 3: Run Burp Suite

1. Open terminal
2. Navigate to the Downloads folder: `cd ~/Downloads`
3. Make the file executable: `chmod +x burpsuite_community_*.sh`
4. Run the installer: `./burpsuite_community_*.sh`
5. Follow the installation wizard
6. Launch Burp Suite from the Applications menu or terminal

### Step 4: Initial Configuration

1. When Burp Suite opens, select "Temporary Project"
2. Click "Next"
3. Select "Use Burp defaults"
4. Click "Start Burp"

## Setting Up the Proxy

A proxy is a middleman. It forwards traffic between the phone and the internet.

### Step 1: Configure Proxy Listener

1. Go to the **Proxy** tab
2. Click **Proxy Settings**
3. Make sure there is a listener on **127.0.0.1:8080**
4. If not, click **Add** and enter:
   - Bind to port: **8080**
   - Bind to address: **All interfaces**
5. Click **OK**

### Step 2: Check the Proxy is Running

Look at the Proxy tab. You should see "Running" next to the listener.

## Installing Burp CA Certificate on Android

To intercept HTTPS traffic, the phone must trust Burp Suite's certificate.

### Step 1: Export the Certificate

1. In Burp Suite, go to **Proxy** -> **Proxy Settings**
2. Click **Import/Export CA certificate**
3. Select **Export certificate in DER format**
4. Save it as `cacert.der`

### Step 2: Set Proxy on Android Device/Emulator

1. Connect your phone or emulator to the same network as Kali
2. Go to **Settings** -> **Wi-Fi**
3. Tap on the connected network
4. Tap **Advanced options**
5. Set **Proxy** to **Manual**
6. Enter Kali's IP address (run `ip a` in Kali to find it)
7. Enter port **8080**
8. Tap **Save**

### Step 3: Install the Certificate

**For Android 10 and below:**

1. Open the phone browser
2. Go to `http://burpsuite`
3. Click on the certificate download link
4. Go to **Settings** -> **Security** -> **Install from storage**
5. Select the downloaded certificate
6. Name it "Burp" and click OK

**For Android 11 and above (Root required):**

Android 11+ does not allow user-installed CA certificates for app traffic. You need a rooted device or emulator:

1. Use ADB to push the certificate to system trust store:
   ```
   adb root
   adb remount
   adb push cacert.der /sdcard/
   adb shell
   ```
2. In the shell:
   ```
   openssl x509 -inform DER -in /sdcard/cacert.der -out /system/etc/security/cacerts/9a5ba575.0
   chmod 644 /system/etc/security/cacerts/9a5ba575.0
   reboot
   ```

### Alternative: Use Magisk Module

Install the "Move Certificates" Magisk module to automatically move user certificates to the system store.

## Configuring Proxy in Android Emulator

For Android emulators (AVD), use the emulator's proxy settings:

1. Launch the emulator
2. Click the **three dots** (More options) in the emulator toolbar
3. Go to **Settings** -> **Proxy**
4. Select **Manual proxy configuration**
5. Enter hostname: **10.0.2.2** (This is Kali's address from the emulator)
6. Enter port: **8080**
7. Click **Apply**

## Testing the Setup

1. Make sure Burp Suite is running and intercept is ON
2. Open the browser on your phone/emulator
3. Go to any website (like http://example.com)
4. Check Burp Suite - you should see the request

If you see traffic in Burp Suite, the setup is working correctly.

## Troubleshooting

| Problem | Solution |
|---------|----------|
| No traffic in Burp | Check proxy IP and port |
| HTTPS not intercepted | Install CA certificate correctly |
| Connection refused | Burp Suite not running or wrong port |
| Slow connection | Normal when intercept is on |
| App not connecting | App might have SSL pinning |

## Summary

Burp Suite is essential for mobile testing. Install it on Kali, configure the proxy, and install the CA certificate on your test device. Once set up correctly, you can see and modify all traffic between the app and the server.