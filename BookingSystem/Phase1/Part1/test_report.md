# Penetration Test Report
**Based on ZAP (Checkmarx) results**

---

## 1️⃣ Introduction

**Testers:**  
- Ahmed Bahgat  
- Md Hosen  

**Purpose:**  
Identify security vulnerabilities in the application’s registration flow, HTTP security headers, and input validation using automated scanning (ZAP) and manual verification.

**Scope:**  
**Tested components:**  
- `GET /`  
- `GET /register`  
- `POST /register`  
- Static files under `/static/*`  

**Exclusions:**  
- No authentication-required areas  
- No admin panels  
- No backend infrastructure or cloud configuration  

**Test Approach:**  
Gray-box testing  

**Environment & Dates:**  
- **Start:** 14/11/2025  
- **End:** 17/11/2025  
- **Environment Details:**  
  - OS: Kali (Dockerized app)  
  - Browsers: Firefox, ZAP internal engine  
  - Application URL: `http://localhost:8000` (local instance only)  

---

## 2️⃣ Executive Summary

The automated ZAP scan identified multiple high-risk security issues, including **SQL Injection** and **Path Traversal**, along with missing security headers and absent CSRF protection.  

**Overall Risk Level:** 🔴 High  

**Top 5 Immediate Actions:**  
1. Implement parameterized SQL queries to prevent SQL Injection.  
2. Enforce strict path handling & canonicalization to prevent Path Traversal.  
3. Add CSRF tokens to all POST forms.  
4. Configure essential security headers (CSP, X-Frame-Options, X-Content-Type-Options).  
5. Validate and sanitize all input fields on the server side.  

---

## 3️⃣ Severity Scale & Definitions

| Severity | Description | Recommended Action |
|----------|------------|------------------|
| 🔴 High | Critical vulnerability (SQLi, RCE, Path Traversal) enabling system compromise | Fix immediately |
| 🟠 Medium | Significant issue requiring specific conditions (XSS, CSRF, missing headers) | Fix ASAP |
| 🟡 Low | Minor weakness or information disclosure | Fix soon |
| 🔵 Info | No direct risk; useful for system hardening | Monitor |

---

## 4️⃣ Key Findings Summary (Top 5)

| ID | Severity | Finding | Description | Evidence |
|----|---------|--------|-------------|---------|
| F-01 | 🔴 High | SQL Injection | `username` parameter in `POST /register` triggers DB errors and Boolean-based SQLi | 500 Internal Server Error + Boolean tests |
| F-02 | 🔴 High | Path Traversal | `username` parameter accepts traversal payloads, allowing unsafe file access attempts | ZAP Path Traversal payloads triggered |
| F-03 | 🟠 Medium | No Anti-CSRF tokens | `/register` POST form has no CSRF protection | `<form method="POST">` without token |
| F-04 | 🟠 Medium | Missing essential security headers | CSP, X-Frame-Options, X-Content-Type-Options missing | ZAP header checks |
| F-05 | 🟠 Medium | Format string vulnerability | Payloads with `%s/%n` cause abnormal termination | “Potential Format String Error” from ZAP |

---

## 5️⃣ Detailed Findings

### High-Risk Findings

**1. SQL Injection**  
- **Endpoint:** `POST /register`  
- **Parameter:** `username`  
- **Impact:** Database manipulation, data leakage, or full database compromise  
- **Evidence:** Server returned 500 Internal Server Error for `'` and Boolean SQLi test (`AND 1=1` vs `AND 1=2`)  

**2. Path Traversal**  
- **Endpoint:** `POST /register`  
- **Parameter:** `username`  
- **Impact:** Access to files outside intended scope  
- **Evidence:** ZAP detected acceptance of traversal-like payloads  

### Medium-Risk Findings

**3. No Anti-CSRF Tokens**  
- **Affected Endpoint:** `/register`  
- **Impact:** Potential CSRF attacks  
- **Evidence:** POST form lacks server-generated token  

**4. Missing Content-Security-Policy (CSP)**  
- **Affected Endpoints:** `/` and `/register`  
- **Impact:** Increases risk of XSS and injection attacks  

**5. Format String Vulnerability**  
- **Affected Endpoint:** `POST /register`  
- **Impact:** Server-side string formatting issues, possible abnormal termination  
- **Evidence:** `%s` / `%n` payloads trigger unexpected behavior  

**6. Missing Anti-Clickjacking Header**  
- **Evidence:** No X-Frame-Options or frame-ancestors headers detected  

### Low-Risk Findings

**7. X-Content-Type-Options Missing**  
- **Affected Endpoints:** `/`, `/register`, and static assets  
- **Impact:** Allows MIME-sniffing attacks  

### Informational Findings

**8. User-Agent Fuzzer Results**  
- **Impact:** No direct vulnerabilities; purely informational  
