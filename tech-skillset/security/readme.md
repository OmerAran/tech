# Software Security Fundamentals

This document explains core security principles used in modern backend systems. It is framework-independent and applies to any scalable application.

---

## 1. The Purpose of Security

Security ensures that systems can be trusted. Every secure system protects:

### **1.1 Confidentiality**
Only authorized users can see the data.

### **1.2 Integrity**
Data can't be modified without permission.

### **1.3 Availability**
The system remains usable even under attacks (e.g., rate-limits, DDoS mitigation).

---

## 2. Authentication vs Authorization

### **2.1 Authentication — Identity**
Confirms who the user is.  
Examples:
- Email + password
- OAuth
- Certificates

### **2.2 Authorization — Permissions**
Determines what the authenticated user can do.  
Example:
- Can user update this resource?
- Does role allow creating orders?

These two must always stay separate.

---

## 3. Stateful vs Stateless Security

### **3.1 Stateful**
- Server stores session
- Session ID kept in cookies
- Suitable for monolithic web systems

### **3.2 Stateless**
- No server-side session
- Each request uses a signed token (e.g., JWT)
- Ideal for distributed systems, microservices, load-balanced traffic

Stateless systems scale horizontally and are used in most modern APIs.

---

## 4. Token-Based Authentication

### **4.1 Access Token**
- Short-lived (5–15 min)
- Sent on every request
- Contains roles and permissions
- Signed and verifiable

Short life reduces risk if compromised.

---

### **4.2 Refresh Token**
- Long-lived (days)
- Used to obtain new access tokens
- Never used to access APIs directly

Because refresh tokens live longer, they require strict protections.

---

### **4.3 If a Refresh Token is Stolen**
Possible risks:
- Attacker can obtain new access tokens until refresh token expires.

Mitigations:
- Rotating refresh tokens
- Server-side storage (DB/Redis)
- Revocation lists
- Device/IP binding
- http-only secure cookies

---

## 5. Token Invalidation Strategies

### **5.1 Blacklisting (Redis)**
Store invalid tokens until expiration.  
Used for logout, user deactivation, compromised credentials.

### **5.2 Token Versioning**
Store `token_version` in DB.  
If user logs out or is deactivated, increment version.  
All old tokens automatically become invalid.

### **5.3 Short Expiration**
Short access token lifetime reduces exposure if stolen.

---

## 6. Password Security

### **6.1 Hashing Algorithms**
Use:
- BCrypt
- PBKDF2
- Argon2

Never store raw passwords. Never log them.

### **6.2 Salting**
Unique salt ensures identical passwords produce different hashes.

---

## 7. Authorization Models

### **7.1 RBAC (Role-Based Access Control)**
Roles → contain permissions  
Users → have roles  
System checks user’s permissions based on their roles.

### **7.2 PBAC / ABAC**
Permission-based or attribute-based models (fine-grained).

---

## 8. Common Attacks & Defenses

### **8.1 Brute Force / Credential Stuffing**
Defenses:
- Rate limiting
- Account lockout
- Captcha after repeated failures

---

### **8.2 SQL Injection**
Defenses:
- Prepared statements
- ORM frameworks
- Never concatenate SQL strings

---

### **8.3 XSS**
Defenses:
- Escape HTML
- Content Security Policy

---

### **8.4 CSRF**
Relevant for cookie-based auth.  
Defenses:
- CSRF tokens
- SameSite cookies

---

### **8.5 CORS Misconfiguration**
Defenses:
- Avoid wildcard `"*"`
- Allow only trusted origins
- Validate credentials usage

---

### **8.6 Token Theft / Replay Attacks**
Defenses:
- Short access tokens
- Rotating refresh tokens
- Server-side refresh token storage
- Device/IP binding

---

## 9. Secure Design Principles

### **9.1 Least Privilege**
Users get only the permissions they need.

### **9.2 Defense in Depth**
Multiple layers of protection:
- JWT
- Authorization
- Method-level checks
- Database ownership validation

### **9.3 Zero Trust**
Never trust:
- User input
- Tokens
- Devices
- IP addresses

Everything must be revalidated.

---

### **9.4 Fail Secure**
If something goes wrong, default to denial.

---

### **9.5 Logging & Auditing**
Log:
- Authentication events
- Token creation
- Permission denials
- Suspicious activities

Logs are critical for detecting attacks.

---

## 10. Architecture of Secure Systems

### **10.1 Authentication Service**
Handles:
- Login
- Token issuance
- Refresh flow
- Logout
- Revocation

---

### **10.2 API Gateway**
Performs:
- Token validation
- Rate limiting
- Throttling

---

### **10.3 Application Services**
Perform authorization and ownership checks.

---

## 11. Summary

Modern backend security relies on:
- Strong authentication
- Clear authorization logic
- Stateless JWT flow
- Refresh token protection
- Token invalidation
- Secure password handling
- Defense against common attacks
- Zero-trust mindset
- Multiple layers of protection

This knowledge is essential for designing scalable and secure systems.
