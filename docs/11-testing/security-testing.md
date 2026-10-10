# Security Testing

Automated vulnerability scanning, SAST, DAST, dependency checks, and fuzz testing.

---

## 1. Security Testing Pipeline

1. **Dependency Vulnerability Scanning (OWASP Dependency-Check & Snyk)**:
   - Scans Maven and NPM dependencies in CI; builds fail on any CVE with CVSS score $\ge 7.0$ (High/Critical).
2. **Static Application Security Testing (SAST - SonarQube / Semgrep)**:
   - Scans code for hardcoded secrets, SQL injection risks, and insecure random number generators.
3. **Dynamic Application Security Testing (DAST - OWASP ZAP)**:
   - Scans running staging environment endpoints for missing CSP headers, cookie security flags, and CORS misconfigurations.
