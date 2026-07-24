# Module 14: Insecure Authorization - Common Vulnerabilities

## What is Insecure Authorization?

Insecure Authorization is the number six risk in the OWASP Mobile Top 10 (M6). It means the app does not properly check what a user is allowed to do. Attackers can access data or perform actions they should not be allowed to.

## Authentication vs Authorization

**Authentication:** "Who are you?" (Login)
**Authorization:** "What are you allowed to do?" (Permissions)

An app can have perfect authentication but broken authorization. The user logs in correctly but can then access other users' data.

## Common Authorization Vulnerabilities

### 1. Insecure Direct Object References (IDOR)

IDOR happens when an app uses user-supplied input to access objects directly without checking authorization.

**Vulnerable API:**
```
GET /api/user/12345/profile
Authorization: Bearer token...
```

If user 12345's token can access user 67890's profile by changing the URL, that is IDOR.

**Example:**
```java
// Vulnerable - no check if the user owns this profile
@GetMapping("/api/user/{userId}/profile")
public Profile getProfile(@PathVariable int userId) {
    return profileRepository.findById(userId);
    // Missing: check if logged-in user has access to this userId
}
```

**Attack:**
```
Original: GET /api/user/12345/profile  (user's own profile)
Modified: GET /api/user/12346/profile  (another user's profile)
```

**If the server returns data for user 12346, that is IDOR.**

### 2. Privilege Escalation

A user with low privileges accesses functionality meant for higher-privileged users.

**Horizontal Privilege Escalation:** User A accesses User B's data (same role).
**Vertical Privilege Escalation:** Regular user accesses admin functionality.

**Example:**
```java
// Vulnerable - no role check
@DeleteMapping("/api/admin/users/{userId}")
public void deleteUser(@PathVariable int userId) {
    userRepository.deleteById(userId);
    // Missing: check if the logged-in user has ADMIN role
}
```

### 3. Missing Authorization Checks

API endpoints that do not check authorization at all.

**Vulnerable:**
```java
// No authorization check at all
@PostMapping("/api/transfer")
public TransferResult transfer(@RequestBody TransferRequest request) {
    return transferService.processTransfer(request);
}
```

**Should be:**
```java
@PostMapping("/api/transfer")
public TransferResult transfer(@RequestBody TransferRequest request,
                                Authentication auth) {
    // Check if user is authorized to make this transfer
    if (!authorizationService.canTransfer(auth.getUserId(),
                                          request.getAmount())) {
        throw new UnauthorizedException();
    }
    return transferService.processTransfer(request);
}
```

### 4. Client-Side Authorization

Authorization checks done only on the client (app), not on the server.

**Vulnerable code (client-side only):**
```java
// App checks if user is admin
if (user.getRole().equals("ADMIN")) {
    showAdminPanel();
    // But the API endpoint has no server-side check
}
```

**Attack:** Call the admin API directly without going through the app.

### 5. Insecure Direct Phone Calls

Some authorization vulnerabilities are specific to mobile.

**Example:** Phone authentication without proper authorization. An app that authorizes based on phone number without verifying SIM ownership.

### 6. Role-Based Access Control (RBAC) Flaws

Problems in how roles are assigned and checked.

**Issues:**
- Roles assigned too permissively
- Role hierarchy issues
- Role escalation (user modifies their own role)
- Default role too permissive

**Example:**
```java
// Vulnerable - user can change their own role
@PutMapping("/api/user/role")
public void updateRole(@RequestBody RoleRequest request) {
    user.setRole(request.getRole());
    userRepository.save(user);
    // No check - regular user can set ADMIN role
}
```

### 7. Missing Authorization in Mobile-Specific Features

Mobile-specific features that lack authorization.

**Examples:**
- Push notifications triggering actions without re-authorization
- Deep links performing actions without authorization
- Biometric auth authorizing transactions without server validation
- App widgets accessing data without authorization

## Testing for Authorization Vulnerabilities

### IDOR Testing

```
1. Log in as User A
2. Note the user ID in API requests
3. Change the user ID to User B's ID
4. If the server returns User B's data, IDOR exists
```

### Privilege Escalation Testing

```
1. Log in as a regular user
2. Try to access admin endpoints
3. If successful, privilege escalation exists
```

## Summary

Insecure Authorization (M6) includes IDOR, privilege escalation, missing authorization checks, client-side only checks, and RBAC flaws. Authorization must always be enforced on the server side. Never trust client-side authorization checks.