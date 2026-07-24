# Module 06: Reversing App with MobSF - Installation and Setup

## Installation Methods

MobSF can be installed in multiple ways. The easiest method is using Docker. You can also install from source.

## Method 1: Install with Docker (Recommended)

Docker is the easiest way to run MobSF. It does not require installing dependencies manually.

### Step 1: Install Docker

On Kali Linux:
```
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable docker --now
sudo usermod -aG docker $USER
```

Log out and log back in for the group change to take effect.

### Step 2: Pull MobSF Docker Image

```
docker pull opensecurity/mobile-security-framework-mobsf
```

### Step 3: Run MobSF

```
docker run -it -p 8000:8000 opensecurity/mobile-security-framework-mobsf
```

MobSF will start and be available at `http://localhost:8000`.

### Docker Run Options

| Option | Purpose |
|--------|---------|
| `-p 8000:8000` | Map port 8000 to host |
| `-d` | Run in background |
| `--name mobsf` | Name the container |
| `-v /path:/home/mobsf/.MobSF` | Persist data |

**Run in background:**
```
docker run -d -p 8000:8000 --name mobsf opensecurity/mobile-security-framework-mobsf
```

**Stop the container:**
```
docker stop mobsf
```

**Start again:**
```
docker start mobsf
```

## Method 2: Install from Source

### Step 1: Install Dependencies

```
sudo apt update
sudo apt install python3 python3-pip python3-venv git -y
```

### Step 2: Clone the Repository

```
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
cd Mobile-Security-Framework-MobSF
```

### Step 3: Set Up Virtual Environment

```
python3 -m venv venv
source venv/bin/activate
```

### Step 4: Install Requirements

```
pip install --upgrade pip
pip install -r requirements.txt
```

### Step 5: Run Setup

```
python3 setup.py
```

This downloads additional tools (APKTool, jadx, etc.) needed by MobSF.

### Step 6: Start MobSF

```
python3 manage.py runserver 0.0.0.0:8000
```

## Method 3: Install with Vagrant

If you want MobSF in a virtual machine:

```
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
cd Mobile-Security-Framework-MobSF
vagrant up
```

This creates an Ubuntu VM with MobSF installed.

## Setting Up Dynamic Analysis

Dynamic analysis requires an Android emulator or device.

### Option 1: Android Emulator (AVD)

**Step 1: Create an AVD with Google APIs**

Use Android Studio to create an AVD. Make sure to select a system image with **Google APIs** (not Google Play). Google API images have root access.

**Step 2: Configure MobSF for the Emulator**

In the MobSF web interface:
1. Go to **Dynamic Analyzer**
2. Click **Start Instrumentation**
3. Select the emulator from the list

**Step 3: MobSF Runtime Dependencies**

MobSF will install its agent app on the emulator. This app monitors the target app's behavior.

### Option 2: Physical Device

**Requirements:**
- Rooted Android device
- USB debugging enabled
- ADB connected

**Step 1: Connect the device**

```
adb devices
```

Make sure the device appears in the list.

**Step 2: Configure MobSF**

In the MobSF web interface:
1. Go to **Dynamic Analyzer**
2. Select your device
3. Click **Start Instrumentation**

## First-Time Setup Checklist

After installing MobSF, verify everything works:

```
1. Start MobSF
2. Open http://localhost:8000
3. Upload a test APK
4. Wait for analysis to complete
5. View the report
```

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Port 8000 in use | Use a different port: `docker run -p 8080:8000 ...` |
| Docker permission denied | Run `sudo usermod -aG docker $USER` and reboot |
| Analysis stuck | MobSF may take time. Wait or check logs. |
| Dynamic analysis not working | Make sure ADB is installed and device is connected |
| Python errors | Use Python 3.8 or higher |
| Missing tools | Run `python3 setup.py` to download tools |

## Updating MobSF

### Docker Update

```
docker pull opensecurity/mobile-security-framework-mobsf
docker stop mobsf
docker rm mobsf
docker run -d -p 8000:8000 --name mobsf opensecurity/mobile-security-framework-mobsf
```

### Source Update

```
cd Mobile-Security-Framework-MobSF
git pull
source venv/bin/activate
pip install -r requirements.txt --upgrade
python3 setup.py
```

## MobSF Directory Structure

After installation, MobSF creates these directories:

```
MobSF/
  +-- MobSF/               # Main application code
  +-- StaticAnalyzer/      # Static analysis code
  +-- DynamicAnalyzer/     # Dynamic analysis code
  +-- Upload/              # Uploaded APK/IPA files
  +-- Downloads/           # Generated reports
  +-- Screenshots/         # Dynamic analysis screenshots
  +-- DATABASE/            # SQLite database
  +-- tools/               # Third-party tools
```

## Summary

The easiest way to install MobSF is with Docker. Pull the image and run it. For dynamic analysis, you need an Android emulator or rooted device. After installation, verify by uploading and scanning a test APK.