
# FOV Web — Security Policy

## Overview

This document outlines security practices, known vulnerabilities, and the hardening roadmap for the **FOV Web** project, which includes two components:

- **FOV Backend** — Node.js / Express API, PostgreSQL, SRT ingestion, HLS generation.
- **FOV Frontend** — Angular single-page application, HLS player, layout editor, auth UI.

**Current Status: Beta — Security hardening in progress.**

The frontend is a pure client-side application served as static assets. It relies entirely on the backend for authentication, authorization, and data integrity. The backend is the single source of truth and must be treated as the primary trust boundary.

---

## Current Status

### ✅ Implemented (Backend)
- CORS middleware (configurable origin)
- HTTP request logging (Morgan)
- Environment variable separation (no hardcoded secrets)
- Connection pooling (prevents connection exhaustion)
- Parameterized SQL queries (`?` placeholders)
- Basic error handling (avoids stack trace leaks in most cases)
- JWT-based authentication (register / login)

### ⚠️ Partially Implemented (Backend)
- Input validation (basic, not comprehensive)
- Error messages (may expose internals in some cases)

### ✅ Implemented (Frontend)
- Route guards on protected pages (`/profile`) via `AuthService.isAuthenticated()`
- JWT-based session stored in `localStorage` (`fov_auth_token`, `fov_auth_user`)
- Centralized HTTP error handling in `AuthService`
- Automatic logout on session loss (missing token)
- Clear separation between public (home, login, register) and private (profile) routes
- Standalone components → no implicit global state leakage between features
- Environment-based API URL (`environment.apiUrl`) — no hardcoded secrets
- HttpClient used consistently (interceptor-ready)

### ⚠️ Partially Implemented (Frontend)
- Input validation on forms (client-side only — must be duplicated server-side)
- Error messages (may expose backend responses verbatim)
- Route protection (auth guard exists but no role/permission system yet)

### ❌ NOT Implemented (High Priority)
- Rate limiting on the API
- Password strength enforcement on the backend (beyond minimum length)
- HTTPS enforcement in application code (rely on reverse proxy)
- Strict CORS whitelist in production
- Security headers (HSTS, CSP, X-Frame-Options, etc.)
- Input sanitization beyond parameterized queries
- HTTP interceptor on the frontend attaching `Authorization: Bearer <token>`
- Automatic logout on `401 Unauthorized`
- Token refresh / silent renewal
- Strict CSP for any future user-generated HTML
- Remember-me / secure token storage strategy (currently plain `localStorage`)
- XSS hardening audit for dynamic content coming from the backend
- Account lockout UX after repeated failed logins

---

## Vulnerability Assessment

### High Risk 🔴

#### 1. JWT stored in `localStorage` (Frontend)
**Impact**: If any XSS occurs, the token is trivially stealable. An attacker with the token can impersonate the user until it expires.
**Current Mitigation**: None. Tokens are stored in plain `localStorage`.
**Fix (roadmap)**:
- Move refresh tokens to `HttpOnly` + `Secure` cookies managed by the backend.
- Keep short-lived access tokens in memory only.
- Add a strict Content Security Policy via the reverse proxy.
- Add a `HttpInterceptor` that logs the user out on `401`.

```typescript
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler) {
    const token = localStorage.getItem('fov_auth_token');
    if (token) {
      req = req.clone({ setHeaders: { Authorization: `Bearer ${token}` } });
    }
    return next.handle(req).pipe(
      catchError((err) => {
        if (err instanceof HttpErrorResponse && err.status === 401) {
          // force logout + redirect to /login
        }
        return throwError(() => err);
      }),
    );
  }
}
````

svgsvg

#### 2. No sanitization of backend-provided strings (Frontend)

**Impact**: Stream titles, usernames, categories, and avatar URLs are rendered directly. Angular interpolation escapes by default, but a single `[innerHTML]` or `bypassSecurityTrust*` would open the door to XSS.
**Current Mitigation**: None used explicitly. **Audit required** to confirm no `bypassSecurityTrust*` is present.
**Fix**:

- Never use `[innerHTML]` for user-controlled content.
- If unavoidable, wrap with `DomSanitizer.sanitize(SecurityContext.HTML, value)`.
- Avoid `bypassSecurityTrustHtml` entirely.

#### 3. SQL injection risk (Backend, low due to parameterized queries)

**Current Status**: Using parameterized queries (`?`).
**Review Needed**: Audit all queries for string concatenation.

javascript

```
// ✅ Safe
db.query('SELECT * FROM users WHERE id = ?', [userId]);

// ❌ Unsafe (do not use)
db.query(`SELECT * FROM users WHERE id = ${userId}`);
```

svgsvg

#### 4. Unauthenticated layout write endpoint (Backend + Frontend)

**Current**: `POST /api/streams/:id/layout` accepts any payload and stores it in memory.
**Risk**: Any visitor can overwrite the layout of any live stream.
**Fix**: Require authentication for `POST`, allow `GET` publicly, or bind the layout to the user session.
The frontend must be aware of this and surface a proper error when the request is rejected.

#### 5. No automatic session invalidation on token expiration (Frontend)

**Impact**: A user with an expired token stays on `/profile` until the next API call fails.
**Fix**: Add the interceptor above and a small "session expired" flow that redirects to `/login`.

### Medium Risk 🟡

#### 1. Client-side validation only (Frontend)

**Current**: All rules (email format, password length, password confirmation) are enforced in the Angular components.
**Fix**: Ensure the backend enforces the same rules. Frontend rules are for UX only, not security.

#### 2. Basic input validation (Backend)

**Needed**: Type validation, length limits, sanitization.

javascript

```
import joi from 'joi';
const userSchema = joi.object({
  username: joi.string().alphanum().min(3).max(30).required(),
  email: joi.string().email().required(),
  password: joi.string().min(8).required(),
});
```

svgsvg

#### 3. Password hashing (Backend)

**Fix**: Use bcrypt.

javascript

```
import bcrypt from 'bcrypt';
const hashedPassword = await bcrypt.hash(password, 10);
```

svgsvg

#### 4. Error messages reflect backend responses (Frontend + Backend)

**Current**: `AuthService.handleError` displays `err.error.error` or `err.error.message` verbatim. The backend may leak internals in some cases.
**Fix**: Whitelist status codes and use generic messages on 5xx. Log details only in development.

typescript

```
if (err.status >= 500) {
  message = 'Une erreur serveur est survenue.';
  if (!environment.production) console.error(err);
}
```

svgsvg

javascript

```
// Backend: generic error response in production
res.status(500).json({ error: 'Internal server error' });
logger.error('Detailed error:', err); // server-side only
```

svgsvg

#### 5. Third-party code in the player (Frontend)

**Current**: `hls.js` parses manifests coming from the backend (which proxies OBS).
**Mitigation**: hls.js is a well-maintained library and the backend is trusted. Keep hls.js up to date.

### Low Risk 🟢

#### 1. No HTTPS enforcement in application code

**Note**: The backend and frontend are HTTP in dev, HTTPS in production via reverse proxy.
**Fix**: Use `helmet.hsts()` or Nginx config.

#### 2. CORS not strictly configured

**Current**: `CORS_ORIGIN=*` in development.
**Fix**: Whitelist specific origins in production.

javascript

```
app.use(cors({
  origin: process.env.CORS_ORIGIN || ['https://fovapp.live'],
  credentials: true,
}));
```

svgsvg

#### 3. No CSP (Frontend)

**Fix**: Add a Content Security Policy header via the reverse proxy (Nginx). Minimal viable CSP for a modern Angular app:

nginx

```
add_header Content-Security-Policy "
  default-src 'self';
  script-src 'self';
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https:;
  media-src 'self' blob: https:;
  connect-src 'self' https://api.fovapp.live wss://api.fovapp.live;
" always;
```

svgsvg

#### 4. Clickjacking protection

**Fix**: Add via Nginx:

nginx

```
add_header X-Frame-Options "SAMEORIGIN" always;
```

svgsvg

#### 5. Rate limiting (Backend)

**Impact**: Vulnerable to brute force and DoS.
**Fix**:

javascript

```
import rateLimit from 'express-rate-limit';
app.use(rateLimit({ windowMs: 15 * 60 * 1000, max: 100 }));
```

svgsvg

---

## Security Best Practices

### 1. Database Credentials (Backend)

**✅ DO:**

- Store in environment variables
- Use different credentials for dev / prod
- Rotate passwords quarterly
- Use a managed database when possible

**❌ DON'T:**

- Hardcode credentials in code
- Commit `.env` files
- Use default database passwords
- Share database passwords over Slack / email

### 2. API Authentication (Backend)

**Recommended**: JWT + Refresh Tokens.

javascript

```
const jwt = require('jsonwebtoken');

function createToken(userId) {
  return jwt.sign({ userId }, process.env.JWT_SECRET, { expiresIn: '1h' });
}

function verifyToken(token) {
  return jwt.verify(token, process.env.JWT_SECRET);
}
```

svgsvg

### 3. Credentials & Tokens (Frontend)

**✅ DO:**

- Store tokens in `localStorage` only as a temporary measure.
- Provide a `logout()` method that clears both token and user.
- Never log tokens in the console.
- Plan to migrate to `HttpOnly` cookies for refresh tokens.

**❌ DON'T:**

- Put tokens in URLs, query strings, or `sessionStorage`.
- Send tokens to any origin other than `api.fovapp.live`.
- Trust the `user` object in `localStorage` for authorization decisions.

### 4. Input Validation

**Example attack:**

text

```
POST /api/users
{"username": "'; DROP TABLE users; --"}
```

svgsvg

**Prevention (Backend):**

javascript

```
if (!username || username.length > 30 || !/^[a-zA-Z0-9_]+$/.test(username)) {
  return res.status(400).json({ error: 'Invalid username' });
}

db.query('INSERT INTO users (username) VALUES (?)', [username]);
```

svgsvg

**Prevention (Frontend):**

- Validate on the client for UX.
- Trim inputs before sending (`this.username.trim()`).
- Show generic errors on 5xx.

### 5. Rendering Untrusted Content (Frontend)

**✅ DO:**

- Use Angular interpolation `{{ value }}` everywhere.
- Sanitize explicitly if you ever need HTML (`DomSanitizer.sanitize`).

**❌ DON'T:**

- Use `[innerHTML]` with data coming from users or the API.
- Use `bypassSecurityTrustHtml`, `bypassSecurityTrustScript`, `bypassSecurityTrustUrl` — ever.

### 6. External Resources (Frontend)

**✅ DO:**

- Load all assets from the same origin.
- Use `rel="noopener noreferrer"` on every `target="_blank"` link.
- Pin any CDN URLs if added later.

**❌ DON'T:**

- Load scripts from third-party domains without SRI.
- Iframe untrusted content.

### 7. Logging & Monitoring (Backend)

**DO log:**

- Authentication attempts (success / failure)
- Failed validation attempts
- Database errors (generic)
- Security-sensitive operations

**DON'T log:**

- Passwords
- API keys
- Personal data
- Sensitive database content

### 8. Secrets Management

**Development:**

- `.env` file locally (never commit)
- `.env.example` as template

**Production:**

- AWS Secrets Manager
- Docker Secrets (if using Swarm)
- Environment variables from CI/CD

### 9. HTTPS / TLS

Ensure in production:

nginx

```
server {
  listen 443 ssl;
  ssl_certificate /path/to/cert.pem;
  ssl_certificate_key /path/to/key.pem;
  ssl_protocols TLSv1.2 TLSv1.3;

  location / {
    proxy_pass http://backend:4000;
  }
}
```

svgsvg

### 10. File Upload Security (Future, Backend)

javascript

```
const ALLOWED_TYPES = ['video/mp4', 'video/x-msvideo'];
if (!ALLOWED_TYPES.includes(file.mimetype)) {
  return res.status(400).json({ error: 'Invalid file type' });
}
// Consider scanning with ClamAV or a similar service.
```

svgsvg

---

## GDPR / Privacy (Frontend)

The frontend stores the following data client-side:

| **Key**          | **Content**               | **Purpose** | **Lifetime**          |
| :--------------- | :------------------------ | :---------- | :-------------------- |
| `fov_auth_token` | JWT                       | Auth        | Until logout / expiry |
| `fov_auth_user`  | `{ id, username, email }` | UI display  | Until logout          |

Nothing else is persisted client-side. No third-party analytics, no trackers, no cookies.

If a cookie banner is added later, list:

- Strictly necessary: session (auth token, if moved to cookies)
- Functional: user preferences (layout) if stored locally

`logout()` acts as a **deletion** path for client-side data.

---

## Incident Response

### Data Breach Protocol

1. **Detect**: Monitor logs, alerts for suspicious activity.
2. **Contain**: Disable affected accounts, revoke tokens (rotate `JWT_SECRET` on the backend → all tokens invalid).
3. **Investigate**: Review backend logs and browser console logs.
4. **Remediate**: Patch the vulnerability, force password resets, add stricter CSP.
5. **Communicate**: Notify affected users, regulatory bodies if required.
6. **Document**: Post-mortem, process improvements.

### Example Response

text

```
Breach Detected: Unauthorized database access on 2026-04-15 10:30 UTC
├─ Impact: All user data exposed (names, usernames, created_at — no passwords)
├─ Scope: ~500 user records
├─ Response:
│  ├─ Disabled public API endpoints (2 hours)
│  ├─ Rotated database credentials
│  ├─ Rotated JWT_SECRET (forced logout on all clients)
│  ├─ Reviewed container logs for other access
│  ├─ Deployed security patch
│  └─ Notified users via email
└─ Root Cause: Unsecured admin debugging endpoint
   Future: Remove debug endpoints in production
```

svgsvg

---

## Security Headers

**Recommended headers (Backend, via middleware):**

javascript

```
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');
  res.setHeader('Content-Security-Policy', "default-src 'self'");
  next();
});
```

svgsvg

Or via helmet:

javascript

```
import helmet from 'helmet';
app.use(helmet());
```

svgsvg

**Frontend headers** are best applied at the reverse proxy (Nginx) — see the CSP and X-Frame-Options sections above.

---

## Security Checklist Before Production

### Backend — Must-have

- □ 

  JWT authentication on all write endpoints
- □ 

  bcrypt password hashing
- □ 

  Rate limiting on `/api/auth/*`
- □ 

  Strict CORS whitelist (`https://fovapp.live`, `https://api.fovapp.live`)
- □ 

  Security headers via helmet or Nginx
- □ 

  HTTPS enforced by reverse proxy
- □ 

  Database credentials rotated and stored in a secrets manager
- □ 

  `.env` files not committed (verify git history)
- □ 

  Generic error messages on 5xx in production
- □ 

  Audit all SQL queries for parameterization

### Frontend — Must-have

- □ 

  HTTP interceptor attaching `Authorization: Bearer <token>`
- □ 

  Automatic logout on `401` responses
- □ 

  Nginx: HTTPS + HSTS + strict CSP + `X-Frame-Options: SAMEORIGIN`
- □ 

  Audit: no `bypassSecurityTrust*`, no `[innerHTML]` on user-controlled data
- □ 

  Backend enforces the same validation as the frontend (email, username, password)
- □ 

  Backend requires auth for `POST /api/streams/:id/layout`
- □ 

  Remove all `console.log` on sensitive data (auth flow, tokens)

### Nice-to-have

- □ 

  Refresh token flow (`HttpOnly` cookie)
- □ 

  Account lockout UI after N failed logins
- □ 

  Password strength meter (client-side, informative)
- □ 

  "Remember me" implemented via refresh token, not extended access token
- □ 

  Session timeout warning modal
- □ 

  OWASP ZAP scan against frontend and backend
- □ 

  `npm audit` clean on both projects
- □ 

  Route-level lazy loading for public pages
- □ 

  Load testing (DoS resistance)

### Penetration Testing Checklist (Before v1.0.0)

- □ 

  OWASP Top 10 review
- □ 

  SQL injection testing
- □ 

  XSS testing (frontend + API responses)
- □ 

  Authentication bypass attempts
- □ 

  Rate limiting testing
- □ 

  CORS misconfiguration review
- □ 

  API parameter fuzzing
- □ 

  Secrets exposure scan (git history)
- □ 

  Dependencies vulnerability scan
- □ 

  Load testing (DoS resistance)

### Tools

bash

```
# Dependency scanning
npm audit

# OWASP ZAP
docker run -t owasp/zap2docker-stable zap-baseline.py -t http://localhost:4000
docker run -t owasp/zap2docker-stable zap-baseline.py -t http://localhost:4200

# Burp Suite (manual testing)
# Download: https://portswigger.net/burp

# git-secrets (prevent credentials in commits)
brew install git-secrets
git secrets --install
```

svgsvg

---

## Compliance & Standards

### GDPR (if European users)

- ✅ Environment variable protection
- ✅ `logout()` acts as a client-side deletion path
- ❌ Data export endpoint (TODO)
- ❌ Right to be forgotten (TODO)
- ❌ Consent management (TODO — not required while no analytics)

### PCI DSS (if handling payments — future)

- Encryption at rest and in transit
- Access controls with authentication
- Regular security testing
- Vulnerability management

### SOC 2 Type II (if enterprise)

- Audit logging
- Access controls
- Change management
- Incident response

---

## Roadmap

### v0.2.0 — Backend Auth Hardening

- JWT on all protected routes
- bcrypt password hashing
- Rate limiting on auth endpoints
- CORS whitelist
- Generic error responses

### v0.3.0 — Frontend Auth Hardening

- HTTP interceptor for `Authorization` header
- Automatic logout on 401
- Session expiry flow
- Audit for `bypassSecurityTrust*` and `[innerHTML]`
- Remove sensitive `console.log`

### v0.4.0 — Infrastructure

- Nginx: HSTS, CSP, X-Frame-Options
- Secrets moved to a secrets manager
- Healthcheck endpoints
- Automated dependency scanning in CI

### v1.0.0 — Production Ready

- Full penetration test passed
- Refresh token flow with HttpOnly cookies
- GDPR data export / deletion endpoints
- Monitoring and alerting in place

---

## Reporting Security Issues

**Please DO NOT file public GitHub issues for security vulnerabilities.**

Instead, email security issues to: **security\@fov-project.io** (placeholder)

Include:

- Description of vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if any)

**Response time**: Within 48 hours

---

## References

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP Cheat Sheet: XSS Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
- [Node.js Security Checklist](https://blog.risingstack.com/node-js-security-checklist/)
- [Express.js Security Best Practices](https://expressjs.com/en/advanced/best-practice-security.html)
- [Angular Security Guide](https://angular.dev/best-practices/security)
- [hls.js Security Considerations](https://github.com/video-dev/hls.js/blob/master/docs/API.md)
- [bcrypt Documentation](https://www.npmjs.com/package/bcrypt)
- [JWT Introduction](https://jwt.io/introduction)
- [JWT Best Practices (RFC 8725)](https://datatracker.ietf.org/doc/html/rfc8725)

---

**Document Version**: 2.0
**Last Updated**: October 2026
**Status**: Active Review Required Before Production
