# GDPR Compliance Checklist – Web-based Booking System

This checklist evaluates GDPR compliance for the booking system described in the assignment.

---

## 1. Personal Data Mapping and Minimization
| Result | Item | Notes |
|--------|-------|--------|
| ✅ | All personal data identified | Email, password (hashed), age, role, logs. |
| ✅ | Data minimization ensured | Only necessary information is collected. |
| ✅ | User age recorded for 15+ verification | Mandatory age field prevents underage use. |

---

## 2. User Registration and Management
| Result | Item | Notes |
|--------|-------|--------|
| ✅ | Registration includes acceptance of Privacy Policy / Terms | Must be shown before account creation. |
| ⚠️ | Users can view/edit/delete their own data | Data deletion may require admin. |
| ✅ | Administrator can delete users (Right to be forgotten) | Admin interface supports deletion. |
| ✅ | Underage registration restricted | System prevents registration under age 15. |

---

## 3. Booking Visibility
| Result | Item | Notes |
|--------|-------|--------|
| ✅ | Public bookings show no personal data | Only resource and time visible. |
| ✅ | No personal identifiers exposed | Names/emails remain hidden. |

---

## 4. Access Control and Authorization
| Result | Item | Notes |
|--------|-------|--------|
| ✅ | Only admins can manage resources/bookings | RBAC implemented. |
| ✅ | Role-based access (reserver/admin) | Enforced in the system. |
| ⚠️ | Admin privileges limited to GDPR purposes | Requires organizational enforcement. |

---

## 5. Privacy by Design and Default
| Result | Item | Notes |
|--------|-------|--------|
| ✅ | Data collection minimized by default | Only essential fields used. |
| ⚠️ | Logs avoid storing unnecessary personal data | Should ensure minimal logging. |
| ✅ | System designed with security and minimal fields | Secure login and limited inputs. |

---

## 6. Data Security
| Result | Item | Notes |
|--------|-------|--------|
| ⚠️ | CSRF, XSS, SQL injection protections | Should be confirmed in implementation. |
| ✅ | Passwords hashed securely | Recommended bcrypt or Argon2. |
| ⚠️ | GDPR-compliant backups | Depends on hosting provider. |
| ⚠️ | Data stored within EU | Deployment environment determines compliance. |

---

## 7. Anonymization and Pseudonymization
| Result | Item | Notes |
|--------|-------|--------|
| ✅ | Anonymization where possible | Public reservations anonymized. |
| ⚠️ | Pseudonymization used internally | Can be improved. |

---

## 8. Data Subject Rights
| Result | Item | Notes |
|--------|-------|--------|
| ⚠️ | User access request possible | May require admin export. |
| ✅ | Data deletion request possible | Admin can remove users. |
| ✅ | Consent withdrawal possible | Only applies to optional cookies/analytics. |

---

## 9. Documentation and Communication
| Result | Item | Notes |
|--------|-------|--------|
| ✅ | Privacy Policy available and accessible | Included in repo. |
| ⚠️ | Internal documentation for developers/admins | Should be implemented. |
| ⚠️ | Data breach response plan documented | Needs organizational procedures. |

---

## Legend
- **✅ Pass**
- **❌ Fail**
- **⚠️ Attention needed**
