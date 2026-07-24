# Module 15: Client Code Quality - Code Structure and Organization

## Overview

Well-organized code is easier to review for security issues. Poorly organized code hides vulnerabilities. This lesson covers how code structure affects security.

## Why Code Structure Matters for Security

- **Reviewability** - Well-structured code is easier to audit
- **Maintainability** - Bugs are easier to find and fix
- **Testability** - Structured code is easier to test
- **Reusability** - Secure patterns can be reused
- **Consistency** - Security checks are applied uniformly

## Good Code Structure

### Separation of Concerns

Different concerns should be in different layers.

```
Presentation Layer (UI)
    |
Business Logic Layer
    |
Data Access Layer
    |
Network Layer
```

**Example of good separation:**
```java
// Presentation layer - handles UI only
public class LoginActivity extends Activity {
    private LoginViewModel viewModel;

    public void onLoginClick() {
        String username = usernameEditText.getText().toString();
        String password = passwordEditText.getText().toString();
        viewModel.login(username, password);
    }
}

// Business logic layer - handles logic
public class LoginViewModel {
    private AuthService authService;

    public void login(String username, String password) {
        authService.authenticate(username, password);
    }
}

// Network layer - handles API calls
public class AuthService {
    public boolean authenticate(String username, String password) {
        // Make API call
    }
}
```

### Security Logic in One Place

Security checks should be centralized, not scattered.

**Bad:**
```java
// Security check in every activity
if (isLoggedIn()) {
    showProfile();
} else {
    showLogin();
}
```

**Good:**
```java
// Centralized security in an interceptor
public class AuthInterceptor {
    public boolean requiresAuth(Activity activity) {
        if (!isLoggedIn()) {
            showLogin(activity);
            return false;
        }
        return true;
    }
}
```

## Poor Code Structure (Red Flags)

### 1. God Classes

One class that does everything.

**Red flag:**
```java
public class MainActivity {
    // Handles UI
    // Handles network calls
    // Handles database
    // Handles authentication
    // Handles encryption
    // All in one file!
}
```

### 2. Copy-Paste Code

The same security logic repeated in multiple places.

**Red flag:**
```java
// Same SQL query in 5 different files
db.rawQuery("SELECT * FROM users WHERE id = " + userId);
db.rawQuery("SELECT * FROM users WHERE id = " + userId);
db.rawQuery("SELECT * FROM users WHERE id = " + userId);
```

### 3. Dead Code

Code that is never used but still in the codebase.

**Red flag:**
```java
// This method is never called but contains hardcoded passwords
private void debugLogin() {
    String adminPassword = "debug123";
    // ...
}
```

### 4. Magic Strings and Numbers

Hardcoded values without constants.

**Red flag:**
```java
// What does "300000" mean? Why 5 attempts?
if (failedAttempts > 5) {
    // Wait 300000ms = 5 minutes
    Thread.sleep(300000);
}
```

## Code Organization Best Practices

### Use Packages Properly

```
com.example.app/
    +-- ui/              # UI layer
    |   +-- activities/
    |   +-- fragments/
    |   +-- adapters/
    +-- data/             # Data layer
    |   +-- api/
    |   +-- db/
    |   +-- model/
    +-- security/         # Security layer
    |   +-- auth/
    |   +-- crypto/
    |   +-- permissions/
    +-- utils/            # Utilities
```

### Use Consistent Patterns

- Use MVVM or MVP consistently
- Use dependency injection
- Use repository pattern for data access
- Use interceptors for security checks

## Code Structure Checklist

```
[ ] Separation of concerns (layered architecture)
[ ] Security logic centralized (not scattered)
[ ] No god classes
[ ] No copy-paste code
[ ] No dead code
[ ] No magic strings/numbers
[ ] Consistent package structure
[ ] Consistent design patterns
[ ] Input validation in one place
[ ] Error handling consistent across the app
```

## Summary

Good code structure improves security by making code reviewable, maintainable, and testable. Use separation of concerns, centralize security logic, and avoid red flags like god classes, copy-paste code, dead code, and magic values. A well-organized codebase is easier to audit for vulnerabilities.