# Low-Level Design

## JWT Strategy & Security

### Why RS256 (Asymmetric) over HS256 (Symmetric)?
*   **HS256**: Uses a single secret key. Both the Auth Service (signer) and Product Service (verifier) need the secret to verify the token.
    *   *Risk*: If the Product Service is compromised, the attacker can Mint tokens.
*   **RS256**: Uses a Private/Public key pair.
    *   Auth Service holds **Private Key** (to sign tokens).
    *   All other services hold **Public Key** (to verify tokens).
    *   *Benefit*: Even if a microservice is hacked, the attacker cannot forge tokens.

## Token Rotation Flow (Refresh Tokens)
To prevent "Inifinite Access" if a token is stolen.
1.  **Access Token (AT)**: Expires in 15 mins.
2.  **Refresh Token (RT)**: Expires in 7 days.
3.  **Rotation Mechanism**:
    *   When the Client uses `RT_1` to get a new `AT`, the Auth Service invalidates `RT_1` and issues `RT_2` (and `AT_2`).
    *   **Reuse Detection**: If the hacker later tries to use `RT_1` again, the Auth Service detects this reuse. It immediately **invalidates the entire chain** (User is forced to log out everywhere).

## Password Security
*   **Algorithm**: Argon2id.
*   **Parameters**: Tuned so hashing takes ~500ms on server hardware. This makes Rainbow Table attacks or brute forcing practically impossible.
*   **Salting**: Unique random salt per user.

## Race Condition Handling
**Scenario**: User clicks "Refresh Token" twice rapidly.
*   Request A hits server → Rotates `RT_1` to `RT_2`.
*   Request B hits server (microseconds later) → Tries to use `RT_1`.
*   *Default*: Reuse detection triggers, banning the user.
*   *Fix*: Implement a "Grace Period" of 10-30 seconds where the old `RT` is still accepted but returns the *same* new `RT_2` instead of rotating again.
