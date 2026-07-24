# Module 05: Reversing App with Apktool - Installation

## Installing Apktool on Kali Linux

Kali Linux comes with many tools pre-installed. Apktool is included in most Kali versions.

### Method 1: Using APT (Recommended)

The easiest way to install Apktool on Kali:

```
sudo apt update
sudo apt install apktool -y
```

**Verify installation:**
```
apktool --version
```

This should show the version number. If it does, Apktool is ready to use.

### Method 2: Installing from GitHub (Latest Version)

The APT version might be outdated. To get the latest version, install from GitHub.

**Step 1: Download the latest release**

```
cd /tmp
wget https://github.com/iBotPeaches/Apktool/releases/download/v2.9.3/apktool_2.9.3.jar
```

Check the latest version number from the GitHub releases page.

**Step 2: Download the wrapper script**

```
wget https://raw.githubusercontent.com/iBotPeaches/Apktool/master/scripts/linux/apktool
```

**Step 3: Make the script executable**

```
chmod +x apktool
```

**Step 4: Move files to system location**

```
sudo mv apktool /usr/local/bin/
sudo mv apktool_2.9.3.jar /usr/local/bin/
```

**Step 5: Verify installation**

```
apktool --version
```

### Method 3: Manual Installation (Any Linux)

This method works on any Linux distribution, not just Kali.

**Prerequisites:**
- Java Runtime Environment (JRE) 8 or higher
- Internet connection

**Check Java:**
```
java -version
```

If Java is not installed:
```
sudo apt install default-jre -y
```

**Steps:**
1. Download the JAR file from GitHub
2. Download the wrapper script
3. Place both files in `/usr/local/bin/`
4. Make the script executable

## Installing Apktool on Windows

**Step 1: Download Java**

Download and install Java from java.com. Make sure Java is added to PATH.

**Step 2: Download Apktool**

1. Download the latest apktool JAR file from GitHub
2. Rename it to `apktool.jar`
3. Place it in `C:\Apktool\`

**Step 3: Download wrapper script**

1. Download the Windows wrapper script (apktool.bat) from GitHub
2. Place it in `C:\Apktool\`

**Step 4: Add to PATH**

1. Open System Properties -> Environment Variables
2. Add `C:\Apktool\` to the PATH variable
3. Click OK

**Step 5: Verify**

Open Command Prompt and type:
```
apktool --version
```

## Installing Apktool on macOS

### Using Homebrew:

```
brew install apktool
```

### Manual installation:

Same as Linux method. Download the JAR and wrapper script, place them in `/usr/local/bin/`.

## Verifying Installation

Run these commands to verify Apktool is working correctly:

```
apktool --version
apktool --help
```

## Troubleshooting Common Issues

| Problem | Solution |
|---------|----------|
| "Command not found" | Apktool is not in PATH. Add it to PATH or use full path. |
| Java error | Install Java: `sudo apt install default-jre` |
| "Permission denied" | Make the script executable: `chmod +x /path/to/apktool` |
| Outdated version | Install from GitHub for the latest version |
| "Cannot find framework" | Install Android framework: `apktool if framework-res.apk` |
| Build fails | Check smali syntax errors or use `--use-aapt2` flag |

## Quick Test

Create a test APK or download a simple one, then run:

```
apktool d test.apk
```

If this works without errors, Apktool is installed correctly. The decoded files will be in the `test` folder.

## Summary

Apktool is easy to install on Kali using apt. For the latest version, install from GitHub. Windows and macOS users can also install it easily. Always verify the installation by decoding a test APK.