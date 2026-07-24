# Module 14: Insecure Authorization - Impact

## Overview

Insecure authorization allows users to access data and perform actions they should not be allowed to. This lesson covers the impact of authorization vulnerabilities.

## Impact on Data Security

### 1. Unauthorized Data Access

Users access data belonging to other users.

**Examples:**
- Viewing other users' private messages
- Reading other users' financial records
- Accessing medical records of other patients
- Viewing private photos of other users

### 2. Unauthorized Data Modification

Users modify data they should not be able to change.

**Examples:**
- Changing other users' account details
- Modifying other users' orders
- Editing shared documents without permission
- Deleting other users' content

### 3. Privilege Escalation

Regular users gain admin privileges.

**What an admin can do:**
- Delete any user account
- Access all user data
- Change system settings
- Bypass all restrictions
- Approve or reject transactions

## Real-World Case Studies

### Case 1: Facebook (2019)

**Vulnerability:** IDOR allowed changing any user's profile information.

**Impact:**
- Thousands of users' profiles modified
- Private posts exposed
- Photos changed without permission

### Case 2: Instagram (2020)

**Vulnerability:** IDOR allowed accessing private account data.

**Impact:**
- Private photos and videos exposed
- Contact information leaked
- High-profile accounts affected

### Case 3: Uber (2014)

**Vulnerability:** Missing authorization allowed viewing any user's trip history.

**Impact:**
- Trip history of any Uber user could be viewed
- Pickup/dropoff locations exposed
- Payment information at risk

## Business Impact

### Financial Costs

| Cost Type | Amount |
|-----------|--------|
| Legal settlements | $1M - $100M |
| Regulatory fines | $100K - $10M |
| Security fixes | $50K - $500K |
| User compensation | $100K - $5M |
| Reputation recovery | $500K - $10M |

### User Trust

- Users expect their data to be isolated from others
- Authorization failures destroy trust
- Users leave the platform

## Summary

Insecure authorization leads to unauthorized data access, modification, and privilege escalation. Users can access other users' data or gain admin privileges. Real-world examples (Facebook, Instagram, Uber) show severe consequences. Always enforce authorization on the server side.