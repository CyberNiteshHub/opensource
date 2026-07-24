# Module 02: Lab Setup - Kali Lab Setup

## What is Kali Linux?

Kali Linux is a special version of Linux made for security testing. It comes with many pre-installed tools for penetration testing. Think of it as a toolbox. When you install Kali, you get hundreds of security tools ready to use.

Kali is based on Debian Linux. It looks and works like Ubuntu but has many extra tools for security work.

## Why Use Kali for Mobile Penetration Testing?

- Most mobile testing tools are already installed
- Easy to install additional tools
- Good community support
- Works well with Android emulators
- All tools are tested and work together

## How to Install Kali Linux

There are three main ways to install Kali:

### Option 1: Virtual Machine (Recommended)

This is the easiest and safest way. Kali runs inside your existing operating system.

**Requirements:**
- At least 4GB RAM (8GB recommended)
- 20GB free disk space
- VirtualBox or VMware installed

**Steps:**

1. Download the Kali Linux VirtualBox image from the official website
2. Open VirtualBox
3. Click File -> Import Appliance
4. Select the downloaded Kali file
5. Click Next and then Import
6. Start the virtual machine
7. Login with default credentials: `kali` / `kali`

### Option 2: Windows Subsystem for Linux (WSL2)

This runs Kali inside Windows without a virtual machine.

**Steps:**

1. Open PowerShell as Administrator
2. Run: `wsl --install -d kali-linux`
3. Restart your computer
4. Launch Kali from Start menu
5. Set up your username and password

**Note:** WSL2 does not have a graphical interface by default. You need to install a VNC server or use the command line only.

### Option 3: Bare Metal (Full Installation)

This installs Kali as your main operating system. This is not recommended for beginners.

**Steps:**

1. Download the Kali ISO file
2. Create a bootable USB drive using Rufus or similar
3. Boot from the USB drive
4. Follow the installation wizard
5. Set up partitions and install

## Setting Up Kali for Mobile Testing

After installing Kali, you need to set it up properly.

### Update the System

Open a terminal and run these commands:

```
sudo apt update
sudo apt upgrade -y
```

This updates all packages to the latest versions.

### Install Essential Tools

Some tools are not pre-installed in newer Kali versions. Install them manually:

```
sudo apt install apktool -y
sudo apt install jadx -y
sudo apt install adb -y
sudo apt install fdroidcl -y
```

### Install Python Tools

```
pip3 install frida-tools
pip3 install objection
```

### Install Docker (for MobSF)

```
sudo apt install docker.io -y
sudo systemctl enable docker --now
sudo usermod -aG docker $USER
```

Then restart your session.

### Android Studio Setup

1. Download Android Studio from the official website
2. Extract the file: `tar -xzf android-studio-*.tar.gz`
3. Run: `cd android-studio/bin && ./studio.sh`
4. Follow the setup wizard
5. Create an Android Virtual Device (AVD) for testing

### Verify Setup

Run these commands to check everything is working:

```
adb --version
apktool --version
java -version
python3 --version
```

## Minimum Requirements for a Mobile Testing Lab

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| RAM | 8GB | 16GB |
| CPU | 4 cores | 8 cores |
| Disk | 60GB | 120GB SSD |
| OS | Kali Linux | Kali Linux |

## Summary

Kali Linux is the best operating system for mobile penetration testing. Install it using a virtual machine for the easiest setup. Update it regularly and install all necessary tools. Set up Android Studio for creating emulators. Verify your setup by checking tool versions.