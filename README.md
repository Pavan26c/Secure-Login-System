# 🛡️ VAULTGATE — Secure Login System

A browser-based secure authentication system built with vanilla HTML, CSS, and JavaScript. Demonstrates real-world security practices including bcrypt password hashing, brute-force lockout, session management, and TOTP-based Two-Factor Authentication.

---

## ✨ Features

| Feature | Details |
|---|---|
| **Password Hashing** | bcrypt.js with cost factor 12 (~200ms per hash) |
| **Input Sanitization** | Strips `< > " ' \` & ;` to block XSS and injection |
| **Email Validation** | Regex validation before any processing |
| **Brute-force Lockout** | Account locks for 30s after 5 failed login attempts |
| **Session Management** | Random token generated on login, invalidated on logout |
| **Two-Factor Auth (2FA)** | TOTP-based 6-digit code, refreshes every 30 seconds |
| **Password Strength Meter** | Live scoring: length, uppercase, numbers, symbols |
| **Audit Log** | Timestamped log of all auth events (login, logout, failures) |

---

## 🚀 Getting Started

### Prerequisites

No build tools or package managers required. Just a modern browser.

### Run Locally

```bash
git clone https://github.com/your-username/vaultgate.git
cd vaultgate
open index.html   # or double-click the file
```

That's it — open `index.html` in any modern browser.

---

## 🗂️ Project Structure

```
vaultgate/
├── index.html        # Main app (all HTML, CSS, JS in one file)
└── README.md         # This file
```

---

## 🔐 Security Implementation Details

### Password Hashing — bcrypt
Passwords are never stored in plaintext. On registration, each password is hashed using **bcrypt** with a cost factor of **12**, meaning the hashing operation takes ~200ms — making brute-force and dictionary attacks computationally expensive.

```javascript
// Registration
const hash = bcrypt.hashSync(password, 12);

// Login verification
const match = bcrypt.compareSync(inputPassword, storedHash);
```

### Input Sanitization
All user inputs are sanitized before processing to prevent XSS and injection attacks:

```javascript
function sanitize(s) {
  return String(s).replace(/[<>"'`&;]/g, '').trim().slice(0, 100);
}
```

### Brute-force Protection
A per-account lockout system tracks failed attempts. After **5 failures**, the account is locked for **30 seconds**:

```javascript
const MAX_ATTEMPTS = 5;
const LOCKOUT_DURATION = 30000; // 30 seconds
```

### Session Management
On successful login, a random session token is generated from a combination of random base-36 and timestamp. It is stored in memory (not localStorage) and invalidated immediately on logout.

```javascript
const token = Math.random().toString(36).slice(2) + Date.now().toString(36);
```

### Two-Factor Authentication (TOTP)
When 2FA is enabled at registration, a TOTP secret is generated and stored. A 6-digit code is derived from the secret and the current 30-second time window — compatible with the TOTP standard (RFC 6238). In production, this would be scanned as a QR code into Google Authenticator or Authy.

---

## 🧪 How to Test

1. **Register** a new account (try enabling 2FA)
2. **Wrong passwords** — enter wrong credentials 5 times to trigger the lockout
3. **2FA login** — the simulated authenticator code is displayed on the 2FA screen; enter it to log in
4. **Dashboard** — view the live session timer and audit log after login
5. **Logout** — session is cleared and the token is invalidated

---

## ⚠️ Demo Limitations

This project is a **frontend-only demonstration**. For a production system, you should:

- Use a **backend server** (Node.js, Python/Flask, etc.) — never hash passwords client-side in real apps
- Store users in a **secure database** (PostgreSQL, MongoDB, etc.) — this demo uses `localStorage`
- Use **HTTPS** and set cookies with `Secure` and `HttpOnly` flags
- Implement **CSRF tokens** on all state-changing endpoints
- Use a proper TOTP library like [`speakeasy`](https://github.com/speakeasy/speakeasy) (Node) or [`pyotp`](https://github.com/pyauth/pyotp) (Python) with real QR code generation
- Add **rate limiting** at the server/network layer (e.g., nginx, express-rate-limit)

---

## 🛠️ Tech Stack

- **HTML5 / CSS3 / Vanilla JavaScript** — no framework required
- **[bcryptjs](https://github.com/dcodeIO/bcrypt.js)** — password hashing (CDN)
- **[Font Awesome 6](https://fontawesome.com/)** — icons (CDN)

---

## 📸 Preview

> Register → (optional 2FA setup) → Login → Dashboard with audit log

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

## 🙌 Acknowledgements

Built as a learning project demonstrating OWASP-recommended authentication best practices.
