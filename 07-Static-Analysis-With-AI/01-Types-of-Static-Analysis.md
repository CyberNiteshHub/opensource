# Module 07: Static Analysis with AI - Types of Static Analysis

## What is Static Analysis?

Static analysis means analyzing code without running it. You read the code, check for patterns, and find problems. It is like reading a book to find mistakes without acting out the story.

In mobile security, static analysis helps find vulnerabilities by examining the app's code, resources, and configuration files.

## Types of Static Analysis

There are three main types of static analysis:

1. Manual Code Review
2. Automated Static Analysis
3. AI-Powered Static Analysis

## 1. Manual Code Review

Manual code review is done by a human. The security expert reads the source code line by line.

**How it works:**
1. The expert opens the code (decompiled or original source)
2. Reads through each file
3. Looks for security issues manually
4. Documents findings

**Advantages:**
- Can understand complex business logic
- Finds issues that automated tools miss
- Understands the context of the code

**Disadvantages:**
- Very time consuming
- Requires expert knowledge
- Human can miss issues due to fatigue
- Expensive

**What a manual reviewer looks for:**
```
Hardcoded passwords
Logic flaws
Authentication bypasses
Authorization issues
Business logic vulnerabilities
Custom cryptography
```

## 2. Automated Static Analysis

Automated static analysis uses tools to scan the code. The tool checks for known vulnerability patterns.

**How it works:**
1. The tool receives the code (APK, source code)
2. It decompiles or reads the code
3. It matches code patterns against a database of known vulnerabilities
4. It generates a report

**Common tools:**
- MobSF
- QARK
- Androwarn
- SonarQube
- Checkmarx
- Fortify

**Advantages:**
- Very fast
- Can scan entire codebase quickly
- Consistent results
- Cost-effective for large projects

**Disadvantages:**
- May produce false positives
- Cannot understand business logic
- Misses context-dependent issues
- Limited to known patterns

**What automated tools look for:**
```
OWASP Mobile Top 10 vulnerabilities
Known insecure API usage
Permission issues
Configuration problems
Standard vulnerability patterns
```

## 3. AI-Powered Static Analysis

AI-powered static analysis uses artificial intelligence to analyze code. The AI has been trained on millions of code samples.

**How it works:**
1. The AI receives code fragments
2. It uses natural language processing to understand the code
3. It compares the code to patterns learned from training
4. It identifies potential vulnerabilities
5. It explains the issues in plain language

**AI tools used:**
- ChatGPT
- Claude
- GitHub Copilot
- Specialized security AI tools

**Advantages:**
- Understands code context
- Can explain findings in plain language
- Learns from new vulnerabilities
- Can handle complex logic
- Suggests fixes automatically

**Disadvantages:**
- May hallucinate (make up issues)
- Requires good prompts
- Privacy concerns (code sent to external service)
- Limited by training data

## Comparison Table

| Aspect | Manual Review | Automated Tools | AI-Powered |
|--------|--------------|----------------|------------|
| Speed | Slow | Fast | Medium |
| Cost | High | Low | Medium |
| Accuracy | High (for experts) | Medium | Medium-High |
| False Positives | Low | High | Medium |
| Context Understanding | Excellent | Poor | Good |
| Business Logic | Can detect | Cannot detect | Limited |
| Learning Ability | Human learns | Needs updates | Can learn |
| Scalability | Poor | Excellent | Good |

## When to Use Each Type

### Use Manual Review When:
- Testing critical applications (banking, healthcare)
- Understanding complex business logic
- The app has custom security implementations
- You need deep analysis of specific areas

### Use Automated Tools When:
- Doing initial assessment
- Testing many apps
- Looking for common vulnerabilities
- Integrating with CI/CD pipeline
- Budget is limited

### Use AI-Powered Analysis When:
- You need quick understanding of code
- You want explanations of vulnerabilities
- You need fix suggestions
- You are learning mobile security
- You have complex code to analyze

## Combined Approach (Best Practice)

The best approach uses all three types together:

```
Step 1: Automated Scan (MobSF)
        |
        v
Step 2: AI Analysis (ChatGPT/Claude)
        |
        v
Step 3: Manual Review (Expert)
        |
        v
Final Report
```

**Example workflow:**
1. Run MobSF on the APK - get automated findings
2. Use AI to analyze suspicious code sections
3. Manual expert reviews the AI findings
4. Combine all findings into a final report

## Summary

Static analysis has three types: manual review, automated tools, and AI-powered analysis. Manual review is thorough but slow. Automated tools are fast but limited. AI-powered analysis combines speed with understanding. The best approach uses all three together for comprehensive security testing.