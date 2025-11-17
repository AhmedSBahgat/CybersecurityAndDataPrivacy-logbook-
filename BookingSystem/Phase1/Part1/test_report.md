Penetration Test Report

(Based on ZAP by Checkmarx results)

1️⃣ Introduction
Testers:

Name: Ahmed Bahgat, Md Hosen

Purpose:

Identify security vulnerabilities in the application’s registration flow, HTTP security headers, and input validation, using automated scanning (ZAP) and manual verification.

Scope

Tested components:

GET /

GET /register

POST /register

Static files under /static/*

Exclusions:

No authentication-based areas

No admin panels

No backend infrastructure or cloud configuration

Test approach:
Gray‑box

Test environment & dates

Start: 14/11/2025
End: 17/11/2025

Environment details:
OS: Kali (Dockerized app)
Browsers: Firefox, (ZAP internal engine)

Only accessible local instance (http://localhost:8000)

2️⃣ Executive Summary
Short summary:

The automated ZAP scan discovered several high‑risk issues including SQL Injection and Path Traversal, along with missing security headers and CSRF protections.

Overall risk level:

🔴 High

Top 5 immediate actions

Implement parameterized SQL queries to eliminate SQL Injection.

Enforce strict path handling & canonicalization to prevent Path Traversal.

Add CSRF tokens to all POST forms.

Configure essential security headers (CSP, X‑Frame‑Options, X‑Content‑Type‑Options).

Validate and sanitize all input fields server‑side.

3️⃣ Severity scale & definitions
Severity Level	Description	Recommended Action
🔴 High	Serious vulnerability (SQLi, RCE, Path Traversal) enabling system compromise	Fix immediately
🟠 Medium	Significant issue requiring some conditions (XSS, CSRF, header missing)	Fix ASAP
🟡 Low	Minor weakness or info disclosure	Fix soon
🔵 Info	No direct risk; useful for hardening	Monitor
4️⃣ Key Findings Summary (Top 5)

(Extracted from ZAP alerts)

ID	Severity	Finding	Description	Evidence
F‑01	🔴 High	SQL Injection	username parameter in POST /register triggers DB errors and Boolean-based SQLi	500 Internal Server Error + manipulated boolean tests
F‑02	🔴 High	Path Traversal	username parameter accepts traversal-like payloads allowing unsafe file access attempts	ZAP Path Traversal payloads triggered
F‑03	🟠 Medium	Absence of Anti‑CSRF tokens	/register POST form has no CSRF protection	<form method="POST"> without token
F‑04	🟠 Medium	Missing essential security headers	Missing CSP, X‑Frame‑Options, X‑Content-Type-Options	ZAP header checks
F‑05	🟠 Medium	Format string vulnerability	%s/%n payload causes abnormal termination	“Potential Format String Error” from ZAP
Detailed Findings (Full ZAP Breakdown)

Below is a compact, professional summary of the ZAP results you posted.

High Risk Findings
1. Path Traversal (High)

Endpoints: POST /register

Parameter: username

Impact: Possible access to server files outside intended scope.

Evidence: ZAP detected traversal payload acceptance.

2. SQL Injection (High)

Endpoints: POST /register

Parameter: username

Impact: Database manipulation, data leakage, or full DB compromise.

Evidence:

Server returned 500 Internal Server Error on '

Boolean SQLi successful (AND 1=1 vs AND 1=2)

Medium Risk Findings
3. No Anti‑CSRF Tokens

Form lacks server-generated CSRF tokens.

Affects: /register

4. Missing Content-Security-Policy (CSP)

Allows XSS, injection attacks.

Affects: / and /register

5. Format String Error

Payloads with %s, %n cause abnormal behavior.

Indicates unsafe server-side string formatting.

6. Missing Anti-clickjacking Header

No X-Frame-Options or frame-ancestors header.

Low Risk Findings
7. X-Content-Type-Options Missing

Affects /, /register, and static assets

Allows MIME-sniffing attacks.

Informational
8. User-Agent Fuzzer Results

No clear vulnerabilities; informational only.
