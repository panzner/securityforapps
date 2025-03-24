# Application Security Audit Guide

## Overview

This guide outlines a comprehensive and actionable checklist for conducting security audits on applications before deployment. It draws from industry best practices and real-world insights to help developers secure their applications against common vulnerabilities and threats. Whether you're deploying a web app, backend API, or using AI models in your stack, this guide aims to make your audit process thorough and reliable.

## Security Audit Checklist

| **Area** | **Checklist / Best Practices** |
|---------|-------------------------------|
| **Authentication & Authorization** | Use trusted providers like Auth.js or Clerk. Prefer OAuth, magic links, or WebAuthn over password-based systems. Never build your own auth unless you're a security expert. Ensure JWTs are stored securely (e.g., HttpOnly cookies) and implement refresh token rotation. Require 2FA/MFA on admin panels and sensitive user accounts. Authenticate every API request with appropriate auth SDK. |
| **Input Validation & Output Encoding** | Validate and sanitize all user input (including search fields). Use schema validation libraries (e.g., Zod). Escape/encode all outputs to prevent XSS and injection attacks. Avoid raw SQL queries—use parameterized queries or ORM-level safeguards. |
| **Environment Variable & Secrets Management** | Never commit `.env` files or hardcode secrets. Use environment variables and secret managers in production. Use the `NEXT_PUBLIC_` prefix in Next.js only for safe-to-expose variables. Add `.env` and other sensitive files to `.gitignore`. |
| **API Security & CORS Configuration** | Authenticate all API routes. Use middleware to protect sensitive routes. Use CORS to restrict access to trusted domains only. Avoid wildcard `*` in production. Use CAPTCHAs on authentication and sensitive routes. |
| **Rate Limiting & Abuse Protection** | Implement rate limiting (e.g., via Redis or middleware). Protect against DDoS attacks using WAFs or host-based solutions (e.g., Vercel’s WAF). Consider row-level security (RLS) on databases like Supabase. |
| **Database Security** | Enforce role-based access control (RBAC). Do not expose your database to the internet. Encrypt sensitive data at rest and in transit. Use ORMs or query builders with built-in injection protections. Set up Row-Level Security (RLS) when available. Limit user input lengths and avoid raw string interpolation in queries. |
| **Secure File Uploads** | Enforce file size limits, MIME type checks, and virus scanning. Store files in secure cloud storage. Avoid writing directly to the server filesystem. |
| **Logging & Monitoring** | Remove `console.log()` statements before deployment. Avoid logging sensitive data. Use centralized logging tools (e.g., Sentry, LogRocket) with alerting. Monitor login attempts, unusual access patterns, and server errors. |
| **Error Handling** | Never expose system errors to users. Show user-friendly messages instead. Avoid leaking internal error stack traces in production. |
| **Dependency & Supply Chain Security** | Use tools like Snyk, OWASP Dependency-Check, or GitHub Dependabot. Avoid outdated or unverified open-source libraries. Track and audit your software supply chain, including AI models. |
| **Security Headers** | Use secure HTTP headers: `Content-Security-Policy`, `X-Frame-Options`, `Strict-Transport-Security`, `X-Content-Type-Options`, etc. |
| **HTTPS & TLS** | Enforce HTTPS in all environments. Redirect HTTP to HTTPS. Use strong TLS configurations and renew certificates automatically. |
| **Infrastructure & Deployment** | Disable debug mode in production. Restrict SSH access. Set up proper CI/CD with staging environments. Separate dev/staging/prod configs. Monitor and protect cron jobs if using WAF attack challenge modes. |
| **Regulatory Compliance** | Ensure compliance with GDPR, CCPA, HIPAA, or other relevant laws. Redact PII when possible and follow data retention guidelines. |
| **Backup & Recovery** | Maintain encrypted, offsite backups. Test recovery processes periodically. Implement rollback procedures. |

## Additional AI-Specific Security Considerations

| **LLM Security Practice** | **Best Practices** |
|--------------------------|-------------------|
| **Prompt Injection Prevention** | Sanitize all user prompts. Avoid letting user input be passed directly to LLMs without filters. Use input/output guards. Understand both direct and indirect injection methods. |
| **Access Control for LLMs** | Apply least-privilege principles. Limit what the LLM can see or do. Use function calling for actions instead of direct execution. |
| **AI Code Review** | Treat AI-generated code like junior developer code—review thoroughly, test with static/dynamic analysis tools, and validate with human oversight. |
| **Model API Security** | Authenticate access to LLM APIs. Apply rate limits and monitor logs for abuse. Protect against model theft with watermarking or access control. |
| **Training Data Validation** | Vet training data sources. Avoid PII or sensitive IP in prompts. Consider sandboxing RAG sources. Track model provenance with an AI BOM (Bill of Materials). |
| **Avoid Hallucinations & Misleading Output** | Validate and fact-check LLM outputs. Use RAG to ground responses. Don’t allow LLMs to make final decisions without review. |
| **Model Supply Chain Security** | Validate open-source models and plugins. Use attestation/signature techniques. Regularly update and revalidate all components. |

## Running an Automated Security Audit with LLM

You can use an AI-powered LLM to help you assess your codebase using this prompt:

```plaintext
Perform a full-stack security audit of the provided codebase. Focus on these key areas:

1. Authentication & Authorization – Look for proper use of OAuth, JWTs, or third-party auth providers. Check for secure storage of tokens and presence of 2FA.
2. Input Validation & Sanitization – Validate all user inputs, check for unescaped outputs, and prevent SQL/XSS/command injection.
3. CORS & API Security – Validate strict domain policies and ensure all endpoints require authentication.
4. Rate Limiting – Confirm middleware or server-side protections are in place to prevent abuse.
5. Secret Management – Ensure sensitive keys are not hardcoded and `.env` files are excluded from version control.
6. Database Practices – Enforce RBAC, avoid raw SQL where possible, and prevent unvalidated queries.
7. Logging – Identify any insecure logging (e.g., passwords or tokens in logs) and confirm use of monitoring tools.
8. Dependency Management – Flag outdated or vulnerable packages and suggest secure alternatives.
9. Security Headers – Check for presence of CSP, HSTS, and other recommended HTTP headers.
10. HTTPS Enforcement – Ensure all routes and APIs are behind TLS.
11. Error Handling – Identify any raw error leaks to the frontend and recommend user-friendly alternatives.
12. Backup & Recovery – Look for implemented strategies and automation for backups and rollback capabilities.
```

## Common Pre-Deployment Security Pitfalls

Before going live, double-check the following:
- Remove console logs
- Disable debugging in production
- Verify production URLs and secrets (NEXTAUTH_URL, NEXTAUTH_SECRET, DATABASE_URL)
- Whitelist production domains in cloud providers (e.g., Google Cloud callbacks)
- Hide system errors from users and return friendly messages
- Use API routes or actions folder to prevent frontend from talking directly to the database
- Use secure file upload handling and limit file sizes/types
- Separate personal and support/admin emails

## Recommended Tools
- Validation: Zod, Yup, Joi
- Secrets Management: Doppler, HashiCorp Vault, Vercel Env Config
- Rate Limiting: Redis, Next.js Middleware
- Monitoring: Sentry, LogRocket
- Dependency Scanning: Snyk, OWASP Dependency-Check
- Static Code Analysis: ESLint, SonarQube, DeepCode

## Acknowledgments

This guide is inspired by insights from Saïd Aitmbarek ([@SaidAitmbarek](https://x.com/SaidAitmbarek)), Jack Friks ([@jackfriks](https://x.com/jackfriks)), and others.

## Contributing

Security best practices evolve quickly. Contributions to keep this guide up-to-date are welcome. Submit a pull request or open an issue.

## License

This project is licensed under the MIT License. You are free to use, modify, and distribute it.