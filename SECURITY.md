# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in Airlock, **please do not open a public GitHub issue.**

Report it privately to: **bdhobson@gmail.com**

Include:
- A description of the vulnerability
- Steps to reproduce
- Potential impact
- Your suggested fix (optional but appreciated)

We'll acknowledge receipt within 48 hours and aim to resolve confirmed issues within 14 days. We'll credit you in the release notes unless you prefer otherwise.

---

## Scope

Airlock is a security tool — we take vulnerabilities in the tool itself seriously. In scope:

- Bypass of prompt injection detection rules
- Unauthorized access to the dashboard or alert system
- Credential exposure via the proxy layer
- Denial of service against the proxy itself

Out of scope:
- Vulnerabilities in the underlying AI APIs (report those to the respective vendors)
- Issues in your own agent code that Airlock doesn't claim to detect

---

## Disclosure Policy

We follow coordinated disclosure. We ask for a reasonable window (typically 14 days) to patch before public disclosure. We won't pursue legal action against good-faith security researchers.
