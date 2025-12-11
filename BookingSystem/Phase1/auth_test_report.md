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
