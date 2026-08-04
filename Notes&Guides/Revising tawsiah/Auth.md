# AuthController

> **Before reading the methods** — this controller is the entry gate of the entire system. It handles who gets in, who gets a token, and who gets out. Every other protected endpoint in the system depends on what happens here.

---

## Register

- Description: Creates a new user account. Validates the request fields, checks for duplicate emails, hashes the password before storing it, and assigns default role and status. Returns a safe user response (no password, no token). Throws an exception if the email is already registered.
    
- Method Signature:
    
    - Public
    - Returns: `UserRegistrationResponseDTO`
    - Parameters: `@RequestBody @Valid RegisterRequestDTO registrationRequest`
- Flow:
    
    - Scenario: Controller receives the request with name, email, and password in the body → `@Valid` checks the fields first
        
        - Scenario ❌ (Validation fails): A required field is missing or invalid → Spring rejects immediately → 400 Bad Request returned, service is never reached.
            
        - Scenario ❌ (Email already exists): Validation passes → service checks if the email already exists in the database → it does → throws `RuntimeException` "Email already exists!" → error response returned, nothing is saved.
            
        - Scenario ✅ (New email, valid data): Email is fresh → service converts the request into a User entity → hashes the plain-text password and replaces it → sets role to `USER` and status to `ACTIVE` as defaults → saves the user to the database → converts the saved user into a safe registration response (no password field) → controller returns it with 201 Created.
            
- Extra notes:
    
    **Why hash the password?**
    
    > Passwords are never stored as plain text. A hashing algorithm (BCrypt) converts the password into an unreadable scrambled string. Even if the database is leaked, the real passwords are not exposed. When the user logs in later, BCrypt compares the typed password against the stored hash without ever reversing it.
    
    **Role and status are never trusted from the request.**
    
    > Even if someone sends `"role": "ADMIN"` in the request body, the service ignores it and always sets `USER` and `ACTIVE`. This prevents privilege escalation at registration.
    

---

## Login

- Description: Authenticates an existing user with email and password. Checks account status before allowing access. Auto-lifts expired suspensions. Updates the last login timestamp. Returns the user's info plus a JWT token to be used in all subsequent requests.
    
- Method Signature:
    
    - Public
    - Returns: `UserAuthResponseDTO` (includes JWT token)
    - Parameters: `@RequestBody LoginRequestDTO loginRequestDTO`
- Flow:
    
    - Scenario: Controller receives the request with email and password → passes to the service → service trims the email and looks it up in the database
        
        - Scenario ❌ (Email not found): Database has no user with that email → throws `AuthenticationException` "Invalid email or password" → error response returned. Note: the message is ==intentionally vague — saying "email not found" would help attackers know which emails exist.==
            
        - Scenario ❌ (Wrong password): Email is found → service checks the typed password against the stored hash → they don't match → throws `AuthenticationException` "Invalid email or password" → error response returned.
            
        - Scenario ❌ (Account banned): Password is correct → service checks the user's status → it's `BANNED` → throws `AuthenticationException` "Your account has been permanently banned" → login blocked.
            
        - Scenario ❌ (Account suspended, still active): Status is `SUSPENDED` → service checks if `suspendedUntil` is still in the future → it is → throws `AuthenticationException` "Your account is suspended until {date}" → login blocked.
            
        - Scenario ✅ (Suspended but expired): Status is `SUSPENDED` but `suspendedUntil` is in the past → service automatically sets status back to `ACTIVE` and clears `suspendedUntil` → continues to login without any error. The suspension lifted itself.
            
        - Scenario ✅ (All checks pass): Service updates `lastLogin` to right now → saves the user → generates a JWT token tied to the user's email → builds the auth response with user info + token → controller returns it with 200 OK.
            
- Extra notes:
    
    **What is a JWT token?**
    
    > JWT (JSON Web Token) is a self-contained string the server gives the user after login. It encodes the user's email and an expiry date, then signs it with a secret key. The user sends this token in the header of every future request. The server verifies the signature to confirm the token is genuine and hasn't been tampered with — no session or database lookup needed.
    
    **This system is stateless.**
    
    > The server stores no session. It doesn't remember who is logged in. Every request must carry the JWT token, and the server re-validates it on every call. This is why logout (next method) is essentially a no-op on the server side.
    
    **The suspension auto-lift happens silently during login.**
    
    > There's no scheduled job or background task that lifts suspensions. The check only happens when the user tries to log in. If the suspension has expired by then, it's cleared automatically and login proceeds normally.
    
    **Why is the error message the same for wrong email and wrong password?**
    
    > Giving different messages ("email not found" vs "wrong password") would allow attackers to enumerate which emails are registered in the system. Using the same vague message for both cases is a deliberate security practice.
    

---

## Logout

- Description: Ends the user's session. Since this system uses stateless JWT authentication, the server has nothing to invalidate. The logout is handled entirely on the client side by discarding the token.
    
- Method Signature:
    
    - Public
    - Returns: plain text message
    - Parameters: `HttpServletRequest request` (not actively used)
- Flow:
    
    - Scenario: Controller receives the request → immediately returns 200 OK with "Logged out successfully". Nothing else happens on the server.
- Extra notes:
    
    **Why does the server do nothing on logout?**
    
    > JWT tokens are stateless — the server never stored them to begin with. The server can't "cancel" a token once issued. True logout means the client (mobile app or browser) deletes the token from its storage so it can no longer be sent. The token technically remains valid until it expires, but without the client holding it, it can't be used.
    
    **Is this a security concern?**
    
    > Slightly — if a token is stolen before logout, it stays valid until expiry. The standard solutions are short expiry times or a token blacklist (a list of invalidated tokens stored in Redis). This project currently uses short expiry and relies on client-side deletion, which is acceptable for most use cases.
    

---

## Security Layer — How the JWT Filter Works (Extra Context)

This is not a controller method, but it's what enforces authentication on every other endpoint.

- Every incoming request passes through `JwtAuthenticationFilter` before reaching any controller.
- The filter reads the `Authorization` header, extracts the token, validates its signature and expiry, identifies the user by email, and tells Spring Security "this request is authenticated".
- If the token is missing, expired, or tampered with — the request is rejected before reaching the controller.

**Which endpoints are open (no token needed)?**

> Register, Login, all GET requests on events and places, filter endpoints, interests list, map, profile, recommendations, search, and masar. Everything else requires a valid token.

**Which endpoints require ADMIN role?**

> Creating, updating, or deleting events and places. Getting all users. Suspending, banning, or unblocking users. Deleting ratings. The role is embedded in the JWT token and checked by Spring Security automatically.