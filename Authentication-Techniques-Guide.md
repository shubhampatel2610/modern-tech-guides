# Authentication Techniques – Easy Guide (with simple examples)

**Date:** February 18, 2026  
**Goal:** Explain authentication methods in simple language with commented examples, flows, pros/cons, and interview tips.

This guide uses **plain words**, **real-world examples**, and **simple request/response snippets** so you can understand the ideas quickly and remember them during interviews.

---

## Table of Contents
1. [What is Authentication?](#what-is-authentication)
2. [JWT (JSON Web Token)](#jwt-json-web-token)
3. [Basic Authentication](#basic-authentication)
4. [Token Authentication (Opaque Tokens)](#token-authentication-opaque-tokens)
5. [Cookie-Based Authentication](#cookie-based-authentication)
6. [OAuth 2.0](#oauth-20)
7. [OpenID Connect (OIDC)](#openid-connect-oidc)
8. [SAML 2.0](#saml-20)
9. [Quick Comparison Table](#quick-comparison-table)
10. [Interview Questions & Quick Answers](#interview-questions--quick-answers)

---

## What is Authentication?
Authentication answers: **“Who are you?”**  
Authorization answers: **“What are you allowed to do?”**

**Real-life analogy:**
- **Authentication** is showing your ID card at the building entrance.  
- **Authorization** is which rooms your badge can open.

---

## JWT (JSON Web Token)

### What is it?
JWT is a **compact, signed token** that carries user identity and some claims. It’s commonly used for **stateless authentication** in web apps and APIs.

### Why do we need it?
- Avoids server-side session storage (stateless)
- Easy to send across web/mobile clients
- Works well with microservices and APIs

### Real-world example
You build a **mobile app** that calls a backend API. The app stores a JWT after login and sends it with every API request.

### How it works (simple flow)
1. User logs in with username/password.
2. Server creates a JWT and signs it with a secret/private key.
3. Client stores JWT (often in memory, local storage, or cookie).
4. Client sends JWT in each request (usually in `Authorization: Bearer <token>` header).
5. Server verifies signature and checks token expiry.
6. If valid → request allowed.

### Flow diagram (JWT)
```
User/Client           Auth Server                Resource API
  |  login (creds)      |                          |
  |-------------------->|                          |
  |                     |  sign JWT (exp, claims)  |
  |                     |------------------------->|
  |  JWT                |                          |
  |<--------------------|                          |
  |  Authorization: Bearer JWT                     |
  |----------------------------------------------->|
  |                     |  verify signature/exp    |
  |                     |------------------------->|
  |                     |  allow/deny              |
  |<-----------------------------------------------|
```

### JWT structure (3 parts)
`header.payload.signature`

- **Header:** algorithm + token type
- **Payload:** user info + claims (e.g., userId, role, exp)
- **Signature:** proves token was created by server and not changed

### Simple example (commented)
```http
POST /login HTTP/1.1
Content-Type: application/json

{
  "email": "alice@example.com",
  "password": "mypassword"
}
```
```json
// Server response (JWT)
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```
```http
// Client sends JWT with every request
GET /orders HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Theoretical details (JWT)
- **Signing vs Encryption:** JWT is typically **signed** (integrity), not encrypted. Anyone can read the payload, but only the server can **verify** the signature.
- **Algorithms:** Commonly HS256 (shared secret) or RS256 (public/private key). RS256 scales better across services.
- **Claims:** Standard claims like `iss`, `sub`, `aud`, `exp`, `iat` help validation and security.
- **Statelessness:** Server does not store session state, so horizontal scaling is easier.

### Pros
- **Stateless**: no session storage on server
- **Scalable**: easy for microservices
- **Portable**: works on web/mobile

### Cons
- **Revocation is hard** (token lives until expiry)
- **Token size** can be larger than session ID
- **If stolen**, attacker can use until expiry

### What if it expires?
- The server rejects it (401 Unauthorized).
- Client must **refresh** the token or re-login.

**Common solution: Refresh Token**
- Short-lived access token (minutes)
- Long-lived refresh token (days/weeks)
- Refresh token used to get new access token

### JWT best practices (simple)
- Use **short expiry** (e.g., 15 min)
- Store refresh token securely (HTTP-only cookie)
- Use HTTPS only
- Avoid storing sensitive data in JWT payload

---

## Basic Authentication

### What is it?
A very simple method: send **username and password** in every request as a base64 string.

### Real-world example
Internal tools or quick scripts where you want the **simplest setup** and you can guarantee HTTPS.

### Flow
1. Client sends `Authorization: Basic base64(username:password)`
2. Server decodes and validates

### Flow diagram (Basic)
```
Client                         Server
  |  Authorization: Basic ...     |
  |------------------------------>|
  |                               | decode + validate
  |                               | allow/deny
  |<------------------------------|
```

### Theoretical details (Basic)
- **Encoding, not encryption:** Base64 is reversible, so HTTPS is mandatory.
- **Stateless:** No server session required.
- **Challenge-response:** Often used with `WWW-Authenticate` header.

### Example
```http
GET /profile HTTP/1.1
Authorization: Basic YWxpY2U6cGFzc3dvcmQ=
```

### Pros
- Very easy to implement
- Works everywhere

### Cons
- Credentials sent every time
- If not HTTPS, it’s unsafe
- No logout; must change password

---

## Token Authentication (Opaque Tokens)

### What is it?
A **random token string** with no readable data inside. Server stores the mapping.

### Real-world example
You issue **API tokens** for partners. If a partner loses a token, you revoke it immediately in your database.

### Flow
1. User logs in
2. Server creates a random token and stores it in DB/cache
3. Client sends token in each request
4. Server looks it up and validates

### Flow diagram (Opaque Tokens)
```
Client                Auth Server/Token Store         API
  |  login            |                               |
  |------------------>|                               |
  |   random token    |                               |
  |<------------------|                               |
  |  Bearer token                                     |
  |----------------------------------------------->   |
  |                     lookup token + user            |
  |<-----------------------------------------------    |
```

### Theoretical details (Opaque Tokens)
- **Server state:** Requires token storage and lookup (cache/DB).
- **Easy revoke:** Delete or blacklist token instantly.
- **Introspection:** Some systems expose a token-introspection endpoint.

### Example
```http
Authorization: Bearer 9f8a7b23c1...  // random token
```

### Pros
- Easy to revoke (delete token server-side)
- Token doesn’t expose user data

### Cons
- Requires server storage
- Slightly slower (lookup needed)

---

## Cookie-Based Authentication

### What is it?
Server creates a **session** and stores it server-side. Client gets a **session cookie**.

### Real-world example
Classic server-rendered websites (banking, admin dashboards) where browsers handle cookies automatically.

### Flow
1. User logs in
2. Server creates session (sessionId) in DB/cache
3. Cookie is sent to client (`Set-Cookie`)
4. Browser sends cookie automatically on each request

### Flow diagram (Cookie Session)
```
Browser/Client              Server
  |  login (creds)          |
  |------------------------>|
  |   Set-Cookie: sessionId |
  |<------------------------|
  |  Cookie: sessionId      |
  |------------------------>|
  |  lookup session         |
  |<------------------------|
```

### Theoretical details (Cookie Session)
- **Session store:** Redis/DB usually stores session data.
- **CSRF risk:** Browsers attach cookies automatically; use SameSite + CSRF token.
- **HttpOnly/Secure:** Prevents JS access and enforces HTTPS.

### Example
```http
Set-Cookie: sessionId=abc123; HttpOnly; Secure; SameSite=Lax
```

### Pros
- Simple for browser apps
- Easy logout (delete session)

### Cons
- Stateful (server must store sessions)
- Harder to scale without shared session store
- Vulnerable to CSRF if not protected

---

## OAuth 2.0

### What is it?
OAuth is **authorization** framework that lets apps access user data from another service **without sharing passwords**.

**Example:** Allowing a music app to access your Google Drive files.

### Real-world example
“Login with GitHub” or allowing a calendar app to **read your Google Calendar events**.

### Flow (Authorization Code – common)
1. User clicks “Login with Google”
2. User is redirected to Google login/consent
3. Google sends authorization code to your app
4. App exchanges code for access token
5. App uses access token to call Google APIs

### Flow diagram (OAuth 2.0 Authorization Code)
```
User/Browser       Client App         Authorization Server
  |  login click     |                    |
  |----------------->|  redirect to IdP   |
  |<--------------------------------------|
  |  login + consent |                    |
  |-------------------------------------->|
  |  auth code       |                    |
  |<--------------------------------------|
  |  code -> token   |------------------->|
  |                  |  access token      |
  |<-----------------|                    |
  |  call API with token                   |
```

### Theoretical details (OAuth 2.0)
- **Roles:** Resource Owner, Client, Authorization Server, Resource Server.
- **Scopes:** Limit what the token can access.
- **PKCE:** Required for public clients (mobile/SPA) to prevent code interception.

### Pros
- Users don’t share passwords with third-party apps
- Good for delegated access

### Cons
- More complex to implement
- Needs careful configuration of redirect URIs

---

## OpenID Connect (OIDC)

### What is it?
OIDC is **authentication layer on top of OAuth 2.0**.
It adds **ID Token** for user identity.

### Real-world example
Single sign-on for your app using **Microsoft Entra ID (Azure AD)** or **Okta**.

### Flow
Same as OAuth, but the response also includes an **ID Token (JWT)** containing user identity (name, email, etc.).

### Flow diagram (OIDC)
```
User/Browser       Client App         OpenID Provider
  |  login click     |                    |
  |----------------->|  redirect to OP    |
  |<--------------------------------------|
  |  login + consent |                    |
  |-------------------------------------->|
  |  auth code       |                    |
  |<--------------------------------------|
  |  code -> tokens  |------------------->|
  |                  |  ID token + access |
  |<-----------------|                    |
```

### Theoretical details (OIDC)
- **ID Token:** Proves identity, not authorization.
- **UserInfo endpoint:** Optional profile data.
- **Standard claims:** `sub`, `email`, `name`, `picture`.

### Pros
- Standard login for modern apps
- Works with Google, Microsoft, Okta, etc.

### Cons
- Slight learning curve
- More setup than basic login

---

## SAML 2.0

### What is it?
SAML is an **enterprise SSO** standard. Mostly used in large organizations.

### Real-world example
Employees log in once to a company portal and get access to **Salesforce, Workday, and internal apps**.

### Flow (simplified)
1. User tries to access app (Service Provider)
2. App redirects to Identity Provider (IdP)
3. User logs in at IdP
4. IdP sends a signed SAML assertion to app
5. App logs user in

### Flow diagram (SAML 2.0)
```
User/Browser       Service Provider (SP)       Identity Provider (IdP)
  |  access app        |                          |
  |------------------->|  redirect to IdP         |
  |<---------------------------------------------|
  |  login             |                          |
  |---------------------------------------------->|
  |  SAML assertion    |                          |
  |<----------------------------------------------|
  |  validate + login  |                          |
```

### Theoretical details (SAML 2.0)
- **XML-based:** Assertions are XML documents signed with X.509 certs.
- **Bindings:** HTTP-Redirect, HTTP-POST are common.
- **Used for enterprise SSO:** Especially with legacy IdPs.

### Pros
- Widely used in enterprise SSO
- Strong security model

### Cons
- XML-heavy and complex
- Harder to implement than OIDC

---

## Quick Comparison Table

| Method | Stateless? | Common Use | Pros | Cons |
|---|---|---|---|---|
| JWT | ✅ | APIs, microservices | Fast, scalable | Hard to revoke |
| Basic Auth | ✅ | Simple APIs | Easy | Weak if no HTTPS |
| Opaque Token | ❌ | Internal APIs | Easy revoke | Needs storage |
| Cookie Session | ❌ | Traditional web apps | Simple for browsers | Needs session store |
| OAuth 2.0 | ✅ | Delegated access | Secure delegation | Complex |
| OIDC | ✅ | Login/SSO | Standard login | Extra setup |
| SAML | ✅ | Enterprise SSO | Mature, secure | Complex XML |

---

## Interview Questions & Quick Answers

**1) What is JWT?**  
A compact token with user claims, signed by the server. Used for stateless authentication.

**2) JWT vs Session Cookie?**  
JWT is stateless and stored on the client. Session cookies rely on server-side sessions.

**3) What happens when a JWT expires?**  
Client gets 401 and must refresh or re-login.

**4) Why are refresh tokens needed?**  
They let you issue new short-lived access tokens without forcing login.

**5) How to revoke JWT?**  
Use short expiry + refresh token blacklist or maintain a token revocation list.

**6) OAuth vs OIDC?**  
OAuth is for authorization; OIDC adds authentication (ID token).

**7) SAML vs OIDC?**  
SAML is older, XML-based and common in enterprise; OIDC is JSON/JWT-based and modern.

**8) Is Basic Auth secure?**  
Only if HTTPS is used. Otherwise credentials are exposed.

**9) What is an opaque token?**  
A random token that has no data inside; server validates it by lookup.

**10) Common JWT security mistakes?**  
Long expiries, storing tokens in insecure places, and not using HTTPS.

---