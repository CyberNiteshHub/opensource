# Module 15: Client Code Quality - Importance

## What is Client Code Quality?

Client Code Quality is the number seven risk in the OWASP Mobile Top 10 (M7). It means the code running on the device (the app) has quality issues that lead to security vulnerabilities.

## OWASP Mobile Top 10 - M7

M7 covers code quality issues:
- Buffer overflows in native code
- Memory leaks
- Race conditions
- Unhandled exceptions
- Poor error handling
- Insecure coding patterns
- Lack of input validation

## Why Code Quality Matters for Security

Bad code leads to security problems. A bug in the code can be exploited by attackers.

**Example:**
```java
// Bad code quality - no input validation
public void processData(String data) {
    String[] parts = data.split(",");
    int value = Integer.parseInt(parts[0]);
    // If data is empty, this crashes
    // If data has invalid format, this crashes
    // Crashing can lead to denial of service
}
```

## Common Code Quality Vulnerabilities

### 1. Buffer Overflows (Native Code)

Native code (C/C++) can have buffer overflow vulnerabilities.

**Vulnerable code:**
```c
void processMessage(char *message) {
    char buffer[64];
    strcpy(buffer, message); // If message > 64 bytes, overflow!
}
```

**What attackers do:**
Send a message longer than 64 bytes. The extra bytes overwrite memory, potentially executing attacker code.

### 2. Memory Leaks

Memory that is not released causes the app to use more and more memory.

**Example:**
```java
// Memory leak - InputStream never closed
public byte[] readFile(String path) {
    FileInputStream fis = new FileInputStream(path);
    byte[] data = new byte[fis.available()];
    fis.read(data);
    // fis.close() is never called!
    return data;
}
```

**Impact:** App crashes due to out-of-memory error.

### 3. Race Conditions

Multiple threads access the same data without proper synchronization.

**Example:**
```java
private int balance = 100;

public void withdraw(int amount) {
    // Race condition - two threads can check balance simultaneously
    if (balance >= amount) {
        balance -= amount;
    }
}
```

**What happens:**
Thread 1 checks (balance=100, amount=100) -> OK
Thread 2 checks (balance=100, amount=100) -> OK
Both threads subtract 100
Balance becomes -100 (should never be negative)

### 4. Unhandled Exceptions

Exceptions that are not caught crash the app.

**Example:**
```java
public void loadUserData(String userId) {
    // If userId is null, this throws NullPointerException
    User user = userRepository.findById(userId);
    // If user is not found, this also throws
    showUserProfile(user);
}
```

### 5. Poor Input Validation

Not validating input from users or external sources.

**Example:**
```java
public void saveNote(String noteContent) {
    // No validation - any data is accepted
    database.save(noteContent);
}
```

**What could go wrong:**
- Very long strings (buffer issues)
- SQL injection (if passed to database)
- XSS (if displayed in WebView)
- Special characters (crashes)

### 6. Insecure Error Handling

Errors that reveal too much information.

**Example:**
```java
try {
    processPayment();
} catch (Exception e) {
    // Shows full error to user (reveals internal details)
    showErrorDialog(e.getMessage());
    // Stack trace leaks code structure
    e.printStackTrace();
}
```

### 7. Logging Sensitive Data

Developers log sensitive information for debugging.

```java
Log.d("Payment", "User " + userId + " paid $" + amount +
      " with card " + creditCardNumber);
```

## Impact of Code Quality Issues

| Issue | Impact |
|-------|--------|
| Buffer overflow | Code execution, app crash |
| Memory leak | App crash, device slowdown |
| Race condition | Data corruption, unexpected behavior |
| Unhandled exception | App crash, denial of service |
| No input validation | Various attacks (injection, overflow) |
| Poor error handling | Information disclosure |
| Sensitive logging | Data leakage |

## Summary

Client Code Quality (M7) covers code quality issues that lead to security vulnerabilities. Buffer overflows, memory leaks, race conditions, unhandled exceptions, poor input validation, and insecure error handling all create security risks. Good code quality is essential for app security.