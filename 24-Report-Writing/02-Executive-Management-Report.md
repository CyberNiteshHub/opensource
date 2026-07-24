# Module 24: Report Writing - Executive and Management Report

## Overview

The executive report is for non-technical readers like CEOs, CTOs, and managers. It focuses on business risk, not technical details.

## Who Reads Executive Reports

| Role | What They Care About |
|------|---------------------|
| CEO | Business impact, financial risk |
| CTO | Technical risk level, resource needs |
| CFO | Cost of remediation, potential fines |
| Legal | Compliance requirements, liability |
| Board | Overall security posture |

## Executive Summary Structure

### 1. Overview

Brief description of what was tested and why.

```
A penetration test was conducted on the MobileApp v3.2
from January 10-20, 2024. The test focused on the Android
application and its backend API.
```

### 2. Key Findings Summary

```
Total Findings: 12
- Critical: 3
- High: 5
- Medium: 3
- Low: 1
```

### 3. Business Impact

```
The critical findings pose significant risk to the company:
1. Hardcoded API keys could lead to unauthorized access
   to backend services, potentially costing $500K+ in
   fraudulent usage.
2. Exposed user data could result in GDPR fines up to
   4% of annual revenue.
```

### 4. Top Recommendations

```
Immediate actions required:
1. Remove all hardcoded API keys from the app
2. Implement SSL pinning
3. Add server-side authorization checks
```

## Style Guide

| Do | Do Not |
|----|--------|
| Use plain language | Use technical jargon |
| Focus on business impact | Focus on technical details |
| Give clear priorities | List every finding equally |
| Include cost estimates | Ignore business context |

## No Technical Jargon

**Bad:** "The app uses ECB mode for AES encryption which is vulnerable to ciphertext manipulation."

**Good:** "The app's data encryption method has a weakness that could allow attackers to read encrypted data."

## Summary

Executive reports communicate security risks to non-technical stakeholders. Focus on business impact, financial risk, compliance, and clear priorities. Avoid technical jargon.