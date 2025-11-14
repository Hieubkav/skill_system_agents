---
name: database-security-audit
description: Comprehensive database security scanning and data integrity validation. Identify vulnerabilities, enforce OWASP compliance, validate data types/formats/ranges, ensure referential integrity, and implement business rules. Use when assessing database security, checking compliance, validating data integrity, or enforcing constraints.
allowed-tools: Read, Write, Edit, Grep, Glob, Execute
version: 1.0.0
---

# Database Security Audit

## Level 1: Overview

Ensure database security and data integrity through automated security scanning and comprehensive data validation based on OWASP standards.

## Prerequisites

- Database admin access for security checks
- Understanding of security best practices
- Backup before applying security fixes

## What This Skill Does

1. Scans database for security vulnerabilities
2. Checks OWASP Top 10 compliance
3. Identifies weak passwords and permission issues
4. Validates data integrity (types, ranges, formats)
5. Ensures referential integrity and constraints
6. Provides remediation scripts and recommendations

---

## Level 2: Quick Start

### Security Scanning Workflow

**Step 1: Run security scan**
```bash
# Assess overall security posture
Scan database for security vulnerabilities
```

**Step 2: Review findings**
- Critical: Fix immediately (weak passwords, public access)
- High: Fix soon (overly permissive grants)
- Medium: Schedule fix (missing logging)

**Step 3: Apply remediations**
```sql
-- Use provided SQL fix scripts
-- Test in staging first
```

**Step 4: Verify compliance**
```bash
# Re-scan to confirm fixes
Check OWASP compliance score
```

### Data Validation Workflow

**Step 1: Define validation rules**
```sql
-- Age range, email format, phone format
-- Foreign key relationships
-- Business rules (total = sum of items)
```

**Step 2: Implement constraints**
```sql
ALTER TABLE users ADD CONSTRAINT check_age
  CHECK (age >= 13 AND age <= 120);
```

**Step 3: Test validations**
```sql
-- Try inserting invalid data
-- Should fail with constraint error
```

---

## Common Use Cases

### Case 1: Pre-Production Security Audit
Before deploying to production, scan database for vulnerabilities, ensure OWASP compliance, fix all critical issues.

### Case 2: Data Integrity Enforcement
Add CHECK constraints to validate email formats, age ranges, price values, preventing invalid data entry at database level.

### Case 3: Compliance Assessment
Prepare for security audit by running compliance checks, generating reports, and documenting remediation efforts.

---

## Best Practices

**Security:**
- Use prepared statements to prevent SQL injection
- Enforce SSL/TLS for connections
- Implement least privilege access
- Enable comprehensive logging
- Regular security scans (monthly)
- Encrypt sensitive data at rest
- Strong password policies

**Data Validation:**
- CHECK constraints for ranges and formats
- Foreign keys for referential integrity
- Unique constraints where appropriate
- NOT NULL for required fields
- Triggers for complex business logic
- Test with both valid and invalid data

---

## Common Vulnerabilities

- Weak/default passwords
- SQL injection risks
- Overly permissive user grants
- Missing SSL/TLS encryption
- Insufficient logging
- Unencrypted sensitive data
- Anonymous access enabled
- Outdated database version

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Can't connect to scan | Verify credentials and network access |
| Permission denied | Need admin privileges for security checks |
| Too many findings | Prioritize Critical → High → Medium |
| Constraint fails on existing data | Audit existing data first, clean before constraint |
| False positives | Review context, may not apply to your setup |

---

## Integration

- **Security Tools** - OWASP ZAP, sqlmap, Nessus
- **CI/CD** - Automate security scans in deployment pipeline
- **Monitoring** - Alert on security events
- **Compliance** - PCI DSS, HIPAA, GDPR validation
