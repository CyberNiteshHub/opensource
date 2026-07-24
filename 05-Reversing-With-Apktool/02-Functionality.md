# Module 05: Reversing App with Apktool - Functionality

## How Apktool Works

Apktool performs two main operations: decoding and building. Understanding these operations helps you use Apktool effectively.

## 1. Decoding APK (apktool d)

When you decode an APK, Apktool does the following:

**Step 1: Extract the APK**
APK is like a ZIP file. Apktool first extracts all files from the APK.

**Step 2: Decode AndroidManifest.xml**
The manifest is stored in binary XML format. Apktool converts it to readable text XML.

**Step 3: Decode resources.arsc**
The resource table is also in binary format. Apktool decodes it into individual XML files in the res/ folder.

**Step 4: Decompile DEX to Smali**
The DEX bytecode is converted to smali. Smali is a human-readable assembly language. Each .class file becomes a .smali file.

**Step 5: Extract Other Files**
Assets, libraries, and META-INF files are extracted as-is.

### Decode Command

```
apktool d app.apk
```

This creates a folder called `app` with all decoded files.

### Decode Command Flags

| Flag | Purpose |
|------|---------|
| `-o <folder>` | Specify output folder |
| `-f` | Force overwrite if folder exists |
| `-r` | Do not decode resources |
| `-s` | Do not decode DEX to smali |
| `--only-main-classes` | Only decode main classes.dex |
| `--no-assets` | Do not copy assets folder |

### Example with Flags

```
apktool d app.apk -o myapp -f --no-assets
```

## 2. Building APK (apktool b)

After modifying the decoded files, you rebuild them into a new APK.

**Step 1: Compile Resources**
XML files are converted back to binary format.

**Step 2: Build DEX**
Smali files are compiled back to DEX bytecode.

**Step 3: Package Everything**
All files are packaged into a new APK file.

### Build Command

```
apktool b app_folder
```

This creates a new APK at `app_folder/dist/app.apk`.

### Build Command Flags

| Flag | Purpose |
|------|---------|
| `-o <file>` | Specify output APK file |
| `-p <path>` | Framework path |
| `--use-aapt2` | Use aapt2 instead of aapt |

## 3. Smali vs Java

Smali is the key to understanding Apktool's functionality.

### What is Smali?

Smali is a human-readable representation of DEX bytecode. It looks like assembly language.

**Java code:**
```java
public int add(int a, int b) {
    return a + b;
}
```

**Equivalent smali code:**
```smali
.method public add(II)I
    .registers 3

    .param p1, "a"    # I
    .param p2, "b"    # I

    .prologue
    add-int v0, p1, p2
    return v0
.end method
```

### Smali Syntax Basics

| Smali Element | Description |
|---------------|-------------|
| `.class` | Class declaration |
| `.method` | Method declaration |
| `.end method` | End of method |
| `.field` | Field/variable declaration |
| `.registers` | Number of registers used |
| `.prologue` | Start of code |
| `v0, v1, ...` | Local registers |
| `p0, p1, ...` | Method parameters |
| `const/4 v0, 0x1` | Assign constant |
| `if-eqz v0, :label` | If equal to zero |
| `goto :label` | Jump to label |
| `invoke-virtual` | Call a virtual method |
| `return-void` | Return nothing |
| `return v0` | Return value |

### Data Types in Smali

| Type | Smali | Description |
|------|-------|-------------|
| int | I | Integer |
| long | J | Long integer |
| float | F | Floating point |
| double | D | Double precision |
| boolean | Z | Boolean |
| char | C | Character |
| String | Ljava/lang/String; | String object |
| Object | Ljava/lang/Object; | Any object |

## 4. Framework Files

Android apps depend on system framework resources. Apktool needs these to decode apps correctly.

### Installing Framework

```
apktool if framework-res.apk
```

This installs the Android framework resources.

### Why Frameworks Matter

- Some apps reference system resources
- Without the correct framework, decoding may fail
- Different Android versions have different frameworks

## 5. Common Apktool Operations

### Decode without Resources

```
apktool d app.apk -r
```

Useful when you only want to modify code, not resources.

### Decode without DEX

```
apktool d app.apk -s
```

Useful when you only want to modify resources, not code.

### Build and Specify Output

```
apktool b app_folder -o modified.apk
```

## Summary

Apktool decodes APK files into smali code and readable resources. The smali code can be modified manually. After modifications, Apktool rebuilds the APK. Understanding smali syntax is essential for making changes. Framework files help Apktool decode apps correctly.