# Security Policy

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| 1.0.x   | ✅ |
| < 1.0   | ❌ |

## Security Considerations for CNC Monitoring

AI CNC Monitor is designed for industrial environments where security is critical. This system may have access to:

- **Live video feeds** from manufacturing equipment
- **Machine status data** including operational parameters
- **Network access** to industrial control systems
- **Alert systems** that could affect production decisions

## Reporting a Vulnerability

**⚠️ Please do not report security vulnerabilities through public GitHub issues.**

Instead, please report security vulnerabilities via email to: [security@your-domain.com]

### What to Include

When reporting a security vulnerability, please include:

1. **Description** of the vulnerability
2. **Steps to reproduce** the issue
3. **Potential impact** on CNC operations and safety
4. **Affected versions** or components
5. **Your contact information** for follow-up

### Response Timeline

- **Initial Response**: Within 24 hours
- **Triage Assessment**: Within 72 hours  
- **Fix Development**: Based on severity (see below)
- **Public Disclosure**: After fix is available and deployed

## Severity Levels

### Critical (Fix within 24-48 hours)
- Remote code execution vulnerabilities
- Authentication bypasses
- Unauthorized access to video streams
- Safety-critical system interference

### High (Fix within 1 week)
- Privilege escalation
- Data exposure vulnerabilities
- Cross-site scripting (XSS) in admin panels
- SQL injection

### Medium (Fix within 2 weeks)
- Information disclosure
- Cross-site request forgery (CSRF)
- Denial of service vulnerabilities
- Configuration vulnerabilities

### Low (Fix within 1 month)
- Minor information leaks
- Non-exploitable security misconfigurations
- Security hardening improvements

## Security Best Practices

### For Deployment

1. **Network Isolation**
   - Deploy on isolated network segments
   - Use VPN for remote access
   - Implement proper firewall rules

2. **Authentication & Authorization**
   - Enable multi-factor authentication
   - Use strong passwords/API keys
   - Implement role-based access control
   - Regular credential rotation

3. **Data Protection**
   - Encrypt video streams in transit
   - Secure storage of logs and recordings
   - Regular backup verification
   - GDPR/privacy compliance for video data

4. **Monitoring & Logging**
   - Enable audit logging
   - Monitor for unauthorized access
   - Set up intrusion detection
   - Regular security assessments

### For Development

1. **Code Security**
   ```bash
   # Run security audits
   pnpm audit
   npm audit fix
   
   # Check for vulnerabilities
   pnpm dlx audit-ci --moderate
   ```

2. **Dependencies**
   - Keep dependencies updated
   - Regular security scanning
   - Use dependabot for updates
   - Review third-party packages

3. **Environment Variables**
   - Never commit secrets to git
   - Use proper environment configuration
   - Rotate API keys regularly
   - Use secrets management tools

## Manufacturing Environment Security

### Physical Security
- **Camera Placement**: Ensure cameras don't expose sensitive areas
- **Network Access**: Secure network connections to CNC controllers
- **Device Management**: Keep monitoring devices updated and secured

### Operational Security
- **User Training**: Train operators on security best practices
- **Incident Response**: Have plans for security incidents
- **Access Control**: Limit system access to authorized personnel
- **Data Retention**: Implement proper data lifecycle management

### Compliance Considerations
- **Industry Standards**: Follow relevant manufacturing security standards
- **Privacy Laws**: Comply with local privacy regulations for video monitoring
- **Export Controls**: Be aware of technology export restrictions
- **Insurance**: Verify cyber insurance coverage for industrial systems

## Common Security Issues to Avoid

### Video Stream Security
```typescript
// ❌ Don't expose raw stream URLs
const streamUrl = "rtmp://internal-camera/stream";

// ✅ Use authenticated proxy endpoints
const streamUrl = "/api/streams/camera1?token=xxx";
```

### API Security
```typescript
// ❌ Don't trust client-side validation
const machineId = req.body.machineId; // Potential injection

// ✅ Validate and sanitize inputs
const machineId = validateMachineId(req.body.machineId);
```

### Authentication
```typescript
// ❌ Don't store passwords in plaintext
const user = { password: "plaintext123" };

// ✅ Use proper hashing and salting
const user = { passwordHash: await bcrypt.hash(password, 12) };
```

## Security Resources

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Node.js Security Best Practices](https://nodejs.org/en/docs/guides/security/)
- [Next.js Security Headers](https://nextjs.org/docs/advanced-features/security-headers)
- [Supabase Security](https://supabase.com/docs/guides/auth/auth-security)

## Contact Information

For security-related questions or concerns:
- **Email**: security@your-domain.com
- **Response Time**: Within 24 hours for security issues
- **PGP Key**: [Optional: Include PGP key for encrypted communication]

---

**Note**: This security policy is regularly reviewed and updated. Please check back periodically for the latest version. 