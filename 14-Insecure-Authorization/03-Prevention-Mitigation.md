# Module 14: Insecure Authorization - Prevention and Mitigation

## Overview

Prevention means implementing proper authorization checks. This lesson covers how to prevent authorization vulnerabilities.

## General Principles

1. **Server-side enforcement** - Never trust client-side checks
2. **Least privilege** - Give minimum required access
3. **Default deny** - Deny access unless explicitly allowed
4. **Centralized authorization** - Use a single authorization system
5. **Test all endpoints** - Every API must have authorization checks

## 1. Implement Server-Side Authorization

Every API endpoint must check authorization on the server.

```java
@GetMapping("/api/user/{userId}/profile")
public Profile getProfile(@PathVariable int userId,
                           Authentication auth) {
    // Get the logged-in user's ID from authentication
    String loggedInUserId = auth.getUserId();

    // Check if the logged-in user can access this profile
    if (!loggedInUserId.equals(userId) && !auth.isAdmin()) {
        throw new AuthorizationException("Access denied");
    }

    return profileRepository.findById(userId);
}
```

## 2. Use Proper Access Control Models

### Role-Based Access Control (RBAC)

Assign roles to users and check permissions based on roles.

```java
public boolean hasPermission(User user, String action) {
    switch (user.getRole()) {
        case "ADMIN":
            return true; // Admin can do everything
        case "USER":
            return !action.startsWith("admin_"); // User cannot do admin actions
        case "GUEST":
            return action.equals("view"); // Guest can only view
        default:
            return false;
    }
}
```

### Attribute-Based Access Control (ABAC)

Check permissions based on user attributes, resource attributes, and environment.

```java
public boolean canAccess(User user, Document doc) {
    // Owner can always access
    if (doc.getOwnerId().equals(user.getId())) return true;

    // Shared documents with specific users
    if (doc.getSharedWith().contains(user.getId())) return true;

    // Public documents can be viewed by anyone
    if (doc.isPublic() && user.getRole().equals("VIEWER")) return true;

    return false;
}
```

## 3. Use Secure Object References

Do not use sequential IDs in URLs. Use unpredictable references.

**Bad (predictable):**
```
/api/user/12345/profile
/api/user/12346/profile
```

**Good (unpredictable):**
```
/api/user/a1b2c3d4/profile
/api/user/e5f6g7h8/profile
```

## 4. Implement Authorization Middleware

Use a centralized authorization check that applies to all endpoints.

```java
// Authorization middleware/interceptor
public class AuthorizationInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request,
                              HttpServletResponse response,
                              Object handler) throws Exception {
        String userId = request.getHeader("X-User-Id");
        String endpoint = request.getRequestURI();

        if (!authorizationService.isAllowed(userId, endpoint)) {
            response.setStatus(403);
            return false;
        }
        return true;
    }
}
```

## 5. Authorization Checklist

```
[ ] Every API endpoint checks authorization server-side
[ ] No client-side only authorization checks
[ ] Sequential IDs replaced with unpredictable references
[ ] Role-based access control implemented
[ ] Default deny principle applied
[ ] Authorization middleware/enforcement layer exists
[ ] Admin endpoints require admin role
[ ] User can only access their own data (unless admin)
[ ] Authorization tested with automated tests
[ ] Penetration testing includes authorization testing
```

## Summary

Prevent insecure authorization by enforcing server-side checks for every API endpoint. Use proper access control models (RBAC, ABAC). Use unpredictable object references. Implement centralized authorization middleware. Always deny access by default and grant minimum required permissions.