# Requirements

## Functional Requirements

1.  **Sign Up**: Users can register with Email/Password or Social Providers (Google, GitHub).
2.  **Sign In**: Users can authenticate and receive a secure token pair (Access + Refresh).
3.  **Token Verification**: Internal services must be able to verify the validity of an Access Token.
4.  **Token Refresh**: Users can obtain a new Access Token using a valid Refresh Token without re-login.
5.  **Logout**: Invalidate the current session (global logout preferred).
6.  **Password Reset**: Secure flow for forgotten passwords.
7.  **MFA**: Multi-Factor Authentication support (TOTP).

## Non-Functional Requirements

1.  **Security**: Industry-standard encryption (bcrypt/Argon2). Protection against XSS, CSRF, and MITM.
2.  **Availability**: 99.99% uptime. If Auth is down, nobody can use the platform.
3.  **Latency**:
    - Login: < 500ms.
    - Token Verification: < 20ms (P99).
4.  **Scalability**: Must handle viral growth (10x spikes).
5.  **Compliance**: GDPR (Right to be forgotten), SOC2 audit logs.
