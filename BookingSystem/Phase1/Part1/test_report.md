# 1️⃣ Introduction

**Tester(s):**  
- Ahmed Bahgat  
- Md Hosen  

**Purpose:**  
- Identify security vulnerabilities in the registration flow, input validation, and HTTP security headers using ZAP and manual verification.

**Scope:**  
- **Tested components:**  
  - `GET /`  
  - `GET /register`  
  - `POST /register`  
  - Static files under `/static/*`  
- **Exclusions:**  
  - No authentication-required areas  
  - No admin panels  
  - No backend infrastructure or cloud configuration  
- **Test approach:** Gray-box

**Test environment & dates:**  
- **Start:** 14/11/2025  
- **End:** 17/11/2025  
- **Environment details:**  
  - OS: Kali (Dockerized app)  
  - Browsers: Firefox, ZAP internal engine  
  - Application URL: `http://localhost:8000`

**Assumptions & constraints:**  
- Credentials provided for testing registration  
- Limited to local instance only  
- Limited time window (14–17 Nov 2025)

---

# 2️⃣ Executive Summary

**Short summary (1-2 sentences):**  
Automated ZAP scan identified multiple vulnerabilities in the registration flow, including SQL Injection, Path Traversal, and missing security headers.

**Overall risk level:** High 🔴

**Top 5 immediate actions:**  
1. Implement parameterized SQL queries to prevent SQL Injection  
2. Enforce strict path handling & canonicalization to prevent Path Traversal  
3. Add CSRF tokens to all POST forms  
4. Configure essential security headers (CSP, X-Frame-Options, X-Content-Type-Options)  
5. Validate and sanitize all input fields server-side  

---

# 3️⃣ Severity scale & definitions

|  **Severity Level**  | **Description**                                                                                                              | **Recommended Action**           |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| 🔴 High              | Serious vulnerability that can lead to full system compromise or data breach (e.g., SQL Injection, Path Traversal).         | *Immediate fix required*         |
| 🟠 Medium            | Significant issue requiring specific conditions (e.g., XSS, CSRF, missing headers).                                         | *Fix ASAP*                       |
| 🟡 Low               | Minor weakness or information disclosure (e.g., server version info).                                                        | *Fix soon*                       |
| 🔵 Info              | No direct risk; useful for system hardening (e.g., missing security headers).                                               | *Monitor and fix in maintenance* |

---

# 4️⃣ Findings

| ID   | Severity | Finding                  | Description                               | Evidence / Proof |
|------|---------|--------------------------|-------------------------------------------|-----------------|
| F-01 | 🔴 High | SQL Injection in registration | `username` input allows DB injection      | Server 500 + Boolean test |
| F-02 | 🔴 High | Path Traversal            | `username` input allows traversal payloads | ZAP payload triggered |
| F-03 | 🟠 Medium | Missing CSRF tokens       | POST form `/register` has no CSRF token  | `<form method="POST">` lacks token |
| F-04 | 🟠 Medium | Missing security headers  | CSP, X-Frame-Options, X-Content-Type-Options missing | ZAP header scan |
| F-05 | 🟠 Medium | Format string vulnerability | `%s/%n` payload causes abnormal behavior | ZAP payload triggered |

---

# 5️⃣ OWASP ZAP Test Report (Attachment)


**Link / Attachment:**  
(https://github.com/AhmedSBahgat/CybersecurityAndDataPrivacy-logbook-/blob/main/BookingSystem/Phase1/Part1/zap_report_round1.md)


