# Module 23: iOS Penetration Basics - iOS Structure

## iOS Architecture Layers

iOS has a layered architecture similar to Android:

```
+----------------------------------+
|         Cocoa Touch              |
|  (UIKit, MapKit, PushKit)        |
+----------------------------------+
|         Media Layer              |
|  (AVFoundation, Core Audio)      |
+----------------------------------+
|         Core Services            |
|  (Core Data, Core Location)      |
+----------------------------------+
|         Core OS                  |
|  (Kernel, File System, Security) |
+----------------------------------+
|         Hardware                 |
+----------------------------------+
```

## iOS File System Structure

### App Sandbox Structure

Each app has its own sandbox directory:

```
/var/mobile/Containers/Data/Application/<UUID>/
    +-- Documents/       (User data, backed up)
    +-- Library/         (App settings, cached data)
    |   +-- Preferences/ (User defaults)
    |   +-- Caches/      (Temporary cache)
    +-- tmp/             (Temporary files)
```

## Keychain

iOS Keychain is a secure storage for sensitive data (passwords, tokens, keys).

**Features:**
- Data is encrypted
- Access requires user passcode
- Apps can share Keychain items with access groups
- Survives app deletion

## Data Protection API

iOS provides file-level encryption based on device lock state:

| Protection Level | Data Available |
|-----------------|----------------|
| NSFileProtectionComplete | Only when device is unlocked |
| NSFileProtectionCompleteUnlessOpen | After first unlock |
| NSFileProtectionCompleteUntilFirstUserAuth | After first unlock |
| NSFileProtectionNone | Always |

## Summary

iOS has a layered architecture with Cocoa Touch, Media, Core Services, and Core OS. Each app has a sandbox with Documents, Library, and tmp directories. Keychain securely stores sensitive data. Data Protection API provides file-level encryption.