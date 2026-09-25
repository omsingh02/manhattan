# Security Policy

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| 0.1.x   | :white_check_mark: |

## Reporting a Vulnerability

If you discover a security vulnerability, sensitive information exposure, or flaw in this repository:

1. **Do not create a public GitHub issue.**
2. Report the vulnerability privately to the repository owner or project maintainers.
3. Please include:
   - Description of the vulnerability and attack vector
   - Steps to reproduce or proof-of-concept
   - Potential impact on system isolation, authentication, or data integrity

## Secure Coding & Dependency Standards

- **Zero Hardcoded Secrets:** All credentials, private keys, API tokens, and sensitive environment paths must be loaded via environment variables (matching `.env.example` templates). Never commit actual secret values to git.
- **Dependency Auditing:** Dependabot vulnerability alerts and automated security updates are enabled to patch insecure dependencies promptly.
- **Input Validation & Boundary Isolation:** All external inputs, network streams, and serialized payloads must be strictly validated and sanitized before processing.
