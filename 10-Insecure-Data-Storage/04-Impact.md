# Module 10: Insecure Data Storage - Impact

## Overview

Insecure data storage has serious consequences for users, companies, and developers. This lesson covers the impact of data storage vulnerabilities.

## Impact on Users

### 1. Account Takeover

When passwords or authentication tokens are stored insecurely, attackers can take over user accounts.

**How it happens:**
1. App stores session token in SharedPreferences (plain text)
2. Attacker extracts token via backup or root access
3. Attacker uses token to access user account without password

**Result:** Attacker can read private messages, post as the user, change account settings.

### 2. Identity Theft

Personal information stolen from insecure storage can be used for identity theft.

**What is stolen:**
- Full name, date of birth
- Email address, phone number
- Home address
- Social Security number (in some apps)
- Government ID numbers

**What attackers do:**
- Open bank accounts in the victim's name
- Apply for credit cards
- File fraudulent tax returns
- Take out loans

### 3. Financial Fraud

Financial data from insecure storage leads to fraud.

**What is stolen:**
- Credit card numbers
- Bank account details
- Payment history
- Transaction records

**What attackers do:**
- Make unauthorized purchases
- Transfer money from victim accounts
- Sell credit card information on dark web

### 4. Privacy Violation

Private information from apps is exposed.

**Examples:**
- Dating app: Private messages, preferences, photos
- Health app: Medical conditions, prescriptions
- Location app: GPS history, frequent locations
- Messaging app: All conversations, contacts

### 5. Physical Safety Risks

Location data from insecure storage can threaten physical safety.

**Risk:**
- Stalking via real-time location data
- Tracking daily routines
- Finding home and work addresses
- Identifying when the user is away

## Impact on Companies

### 1. Data Breach Costs

The financial impact of a data storage breach.

| Cost Factor | Amount |
|-------------|--------|
| Incident investigation | $50,000 - $200,000 |
| Notification to users | $10,000 - $100,000 |
| Legal fees | $100,000 - $1,000,000 |
| Regulatory fines | $100,000 - $10,000,000 |
| Customer compensation | $50,000 - $500,000 |
| PR and reputation management | $50,000 - $200,000 |
| Security improvements | $100,000 - $500,000 |

**Total:** $500,000 - $10,000,000+

### 2. App Store Removal

Apps with data storage vulnerabilities can be removed from app stores.

**Consequences:**
- Immediate removal from Google Play / App Store
- Loss of all revenue from app store
- Negative impact on other apps from same developer
- Difficulty getting future apps approved

### 3. User Churn

Users delete the app after a data breach.

**Statistics:**
- 65% of users delete an app after a data breach
- 30% leave negative reviews
- 50% tell others not to use the app
- Only 20% return after 6 months

### 4. Regulatory Fines

| Regulation | Maximum Fine | Applied For |
|------------|-------------|-------------|
| GDPR (Europe) | 4% of annual revenue | User data exposure |
| CCPA (California) | $7,500 per violation | Personal data exposure |
| PCI DSS | $500,000 per incident | Credit card data exposure |
| HIPAA | $1.5 million per year | Health data exposure |
| LGPD (Brazil) | 2% of revenue | Personal data exposure |

## Impact on Developers

### 1. Professional Consequences

- Termination of employment
- Difficulty finding new jobs
- Professional reputation damage
- Loss of security clearances

### 2. Legal Liability

In some cases, developers can be held personally liable for:
- Gross negligence in data protection
- Ignoring known security issues
- Violating data protection laws

## Real-World Case Studies

### Case: Uber Data Breach (2016)

**What happened:**
Uber stored driver and rider personal data insecurely. Hackers accessed the data through a third-party service.

**Data exposed:**
- 57 million users' names, emails, phone numbers
- 600,000 drivers' license numbers

**Impact:**
- $148 million settlement
- CEO resigned
- Years of negative media coverage
- Regulatory investigations worldwide

### Case: Equifax Data Breach (2017)

**What happened:**
Equifax stored sensitive personal data without proper encryption. Attackers exploited a web vulnerability.

**Data exposed:**
- 147 million people's names, SSNs, birth dates
- 209,000 credit card numbers
- Dispute documents with personal data

**Impact:**
- $1.4 billion in costs
- $700 million settlement with FTC
- Former CEO investigated for insider trading
- Company still recovering years later

## Severity Classification

| Data Type | Storage Method | Severity |
|-----------|---------------|----------|
| Password | Plain text in SharedPreferences | Critical |
| Credit card | Plain text in SQLite | Critical |
| Session token | Plain text in file | High |
| Personal info | Plain text in cache | High |
| User preferences | Plain text (non-sensitive) | Low |
| Public data | Any storage | Info |

## Summary

Insecure data storage leads to severe consequences. Users face account takeover, identity theft, financial fraud, and privacy violation. Companies face millions in costs, app store removal, user churn, and regulatory fines. Developers face professional and legal consequences. Always encrypt sensitive data on the device to prevent these impacts.