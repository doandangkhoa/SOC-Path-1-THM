---
title: IAAA Failures
updated: 2026-03-21 14:24:07Z
created: 2026-03-21 13:08:02Z
latitude: 21.02776440
longitude: 105.83415980
altitude: 0.0000
---

- **Identity** - the unique account (e.g., user ID/email) that represents a person or service.
- **Authentication** - proving that identity (passwords, OTP, passkeys).
- **Authorisation** - what that identity is allowed to do.
- **Accountability** - recording and alerting on who did what, when, and from where.

# A01: Broken Access Control

happens when the server **doesn't properly** enforce **`who can access what`** on every request.

**horizontal privilege escalation** : same role, other user's stuff.

**Vertical privilege escalation** : jumping to admin-only actions.

# A07: Authentication Failures

Authentication Failures happen when an application can’t reliably verify or bind a user’s identity. Common issues include:

- username enumeration
- weak/guessable passwords (no lockout/rate limits)
- logic flaws in the login/registration flow
- insecure session or cookie handling

If any of these are present, an attacker can often log in as someone else or bind a session to the wrong account.

# A09: Logging & Alerting Failures

When applications don’t record or alert on security-relevant events, defenders can’t detect or investigate attacks. Good logging underpins **accountability** (being able to prove who did what, when, and from where).

&nbsp;

&nbsp;