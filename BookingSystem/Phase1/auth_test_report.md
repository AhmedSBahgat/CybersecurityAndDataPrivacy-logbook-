# Authorization Test Report — Phase 3 Implementation

## 1. Guest (Not Logged In)

### ✅ Guest — Allowed Actions

| Action | Endpoint | Behavior | Spec OK? | Notes |
|--------|---------|----------|----------|-------|
| View public resource list | `/` | Works, shows resources without names | ✔️ Yes | Matches GDPR requirement (spec #8) |
| Access login page | `/login` | Works | ✔️ Yes | - |
| Access registration page | `/register` | Works | ✔️ Yes | - |
| Access public API resources | `/api/resources` | Returns data | ❌ No | Data exposure vulnerability |

### ❌ Guest — Blocked Actions

| Action | Endpoint | Behavior | Spec OK? |
|--------|---------|----------|----------|
| Access profile | `/profile` | Not Found | ✔️ Yes |
| Access reservation page | `/reservation` | Unauthorized | ✔️ Yes |
| Access admin dashboard | `/admin` | Not Found | ✔️ Yes |
| Access admin user list | `/admin/users` | Not Found | ✔️ Yes |
| Access admin resources | `/admin/resources` | Not Found | ✔️ Yes |
| Access reservations API | `/api/reservations` | Protected (returns nothing) | ✔️ Yes |
| Access admin user API | `/api/admin/users` | Not Found | ✔️ Yes |

---

## 2. Reserver (Logged-In Normal User)

### ✅ Reserver — Allowed Actions

| Action | Endpoint | Behavior | Spec OK? | Notes |
|--------|---------|----------|----------|-------|
| Login | `/login` | Works | ✔️ Yes | - |
| Register | `/register` | Works | ✔️ Yes | - |
| Access reservation page | `/reservation` | Works | ✔️ Yes | Can book resources |
| View resources (API) | `/api/resources` | Returns data | ✔️ Yes | Reserver can see available resources |
| View reservations (API) | `/api/reservations` | Returns data | ✔️ Yes | Can view own or all reservations |

### ❌ Reserver — Blocked / Incorrect Actions

| Action | Endpoint | Behavior | Spec OK? | Notes |
|--------|---------|----------|----------|-------|
| Access profile | `/profile` | Not Found (404) | ❌ No | BUG: Reserver should have a profile page |
| Access admin pages | `/admin/*` | 404 – “The process failed” | ✔️ Yes | Correctly blocked, though 403 preferred |
| Access admin API | `/api/admin/users` | 404 – “The process failed” | ✔️ Yes | Correctly protected |

---

## 3. Administrator (Logged-In Admin User)

### ✅ Admin — Allowed Actions

| Action | Endpoint | Behavior | Spec OK? | Notes |
|--------|---------|----------|----------|-------|
| View homepage | `/` | Works, names hidden | ✔️ Yes | Good privacy |
| Login | `/login` | Works | ✔️ Yes | - |
| Register | `/register` | Works | ✔️ Yes | - |
| Access reservation UI | `/reservation` | Works | ✔️ Yes | - |
| List resources (API) | `/api/resources` | Returns data | ✔️ Yes | - |
| Create resource | `POST /api/resources` | Works | ✔️ Yes | Admin can add resources |
| Modify resource | `PUT /api/resources/1` | Works | ✔️ Yes | Admin can edit resources |
| View all reservations | `/api/reservations` | Works | ✔️ Yes | Admin access allowed |

### ❌ Admin — Unexpected Limitations / Bugs

| Action | Endpoint | Behavior | Spec OK? | Notes |
|--------|---------|----------|----------|-------|
| Access admin dashboard | `/admin` | Not Found | ❌ No | Admin UI missing |
| View users (UI) | `/admin/users` | Not Found | ❌ No | Required by spec |
| Manage resources (UI) | `/admin/resources` | Not Found | ❌ No | UI does not exist |
| Delete resource | `DELETE /api/resources/1` | ❌ Error | ❌ No | Admin must delete resources |
| View users (API) | `GET /api/admin/users` | Not Found | ❌ No | Must exist per spec |
| Delete user | `DELETE /api/admin/users/1` | Not Found | ❌ No | Admin cannot remove reservers |

---

## 4. Summary of Findings

### ✔️ Positive Security Behaviors
- Guest cannot access protected content  
- Reservations API is protected from Guests  
- Admin and Reserver roles are separated correctly  
- No direct admin privilege exposure to Guests  

### ❌ Security Issues
- `/api/resources` exposed to Guests (data leak)  
- Admin cannot delete resources (broken feature)  
- Admin cannot manage users (missing functionality)  
- Reserver profile page missing (bug)

### ❌ Specification Deviations

| Requirement | Status |
|------------|--------|
| Admin can add/modify/remove resources | ❌ Only add/modify work; remove fails |
| Admin can delete reservers | ❌ Not implemented |
| Admin can manage reservations | ✔️ Yes via `/api/reservations` |
| Booking system hides user identity | ✔️ Yes |
| System follows Privacy by Design | ❌ Guest API leak violates PbD |
| GDPR compliance | ❌ Guest API data leak |

---

# 4. Phase 3 — Hidden Directory & Endpoint Discovery (Gobuster + wfuzz)

Hidden endpoint testing was performed on:

- `http://localhost:8003/`
- `http://localhost:8003/api/`

## 4.1 Tools & Commands Used

### Directory discovery
gobuster dir -u http://localhost:8003 -w /usr/share/wordlists/dirb/common.txt --no-error


### API discovery
wfuzz -c -w /usr/share/wordlists/dirb/common.txt --hc 404 http://localhost:8003/api/FUZZ


### IDOR (Reservation ID Enumeration)
wfuzz -c -z range,1-200 --hc 404 http://localhost:8003/api/reservations/FUZZ

---

## 4.2 Discovery Results

✔️ **No new directories or pages found**

**Gobuster results:**

| Path | Status | Notes |
|------|--------|-------|
| `/` | 200 | Public homepage |
| `/login` | 200 | Login |
| `/register` | 200 | Registration |
| `/resources` | 200 | Resource list |
| `/static/*` | 200 | Static assets |
| `/reservation` | 200/302 | Depends on login |
| `/status.html` | 200 | Template |

Admin paths:

- `/admin`
- `/admin/users`
- `/admin/resources`

→ **All returned 404 (missing UI)**

---

## 4.3 API Endpoint Discovery Results

| Endpoint | Status | Description |
|----------|--------|-------------|
| `/api/resources` | 200 | ❌ **Public, visible to Guests** |
| `/api/reservations` | 200 (Admin), empty (Reserver), protected (Guest) | ✔️ OK |
| `/api/admin/*` | 404 | Missing, not implemented |

Missing required admin APIs:

- `/api/admin/users`
- `/api/admin/users/{id}`
- `/api/admin/resources`
- `/api/admin/reservations`

---

## 4.4 IDOR Testing (Reservation ID Enumeration)

Tested:

/api/reservations/1
/api/reservations/2
...
/api/reservations/200


Result:

✔️ All returned **404 Not Found**

Interpretation:

- No IDOR found
- But also means: **Missing API for single reservation access**

---

## 4.5 Access Control Tests on Discovered Endpoints

### Guest

| Endpoint | Result | Expected? | Notes |
|----------|--------|-----------|-------|
| `/api/resources` | 200 | ❌ No | Public data leak |
| `/api/reservations` | Protected | ✔️ Yes | Works |
| `/api/admin/*` | 404 | ✔️ Yes | Admin API missing |

### Reserver

| Endpoint | Result | Expected? | Notes |
|----------|--------|-----------|-------|
| `/api/resources` | 200 | ✔️ Yes | OK |
| `/reservation` | Works | ✔️ Yes | OK |
| `/api/reservations` | 200 | ✔️ Yes | OK |
| `/api/admin/users` | 404 | ✔️ Yes | Protected |

### Admin

| Endpoint | Result | Expected? | Notes |
|----------|--------|-----------|-------|
| `/api/resources` | 200 | ✔️ Yes | OK |
| POST `/api/resources` | Works | ✔️ Yes | OK |
| PUT `/api/resources/1` | Works | ✔️ Yes | OK |
| DELETE `/api/resources/1` | ❌ Error | ❌ No | Bug |
| `/api/admin/users` | 404 | ❌ No | Missing |

---

## 4.6 Findings from Phase 3

### ✔️ Positive
- No IDOR vulnerabilities
- No hidden admin pages
- Backend access control mostly correct
- No unintended API exposure except one case

### ❌ Issues
- `/api/resources` is **public** → **Critical data leak**
- Admin API not implemented
- Cannot delete resources
- Cannot delete reservers
- No admin UI pages
- Using **404 instead of 403** for unauthorized access
- Reserver profile page missing

---

## 4.7 Phase 3 Conclusion

- No hidden endpoints discovered  
- Missing admin features are *not hidden* — they are simply **not implemented**
- **One major vulnerability:**  
  **Guest can access `/api/resources` → Privacy violation**

---
