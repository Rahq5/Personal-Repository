what is jwt and how it works 
what is the 3 contents of it 
pros and cons 

# Introduction 
JSON Web Token (JWT) is a compact and secure method for transmitting information between parties as a JSON object. It is commonly used for authentication and authorization in web applications

key featuers:
- **stateless authentication:** 
	means user informations are stored in a token, at client's machine which reduces the need for server-side storage session
	
- **Widely supported:** 
	Works across different programming languages and platforms

- **compact format**: 
	  Consists of three parts—Header, Payload, and Signature—encoded into a single string.
	  
- **Secure data exchange**
	  uses digital sigantures (unique mark) to prove this token isnt manipulated

## JWT Structure
JWT consists of 3 parts separated by dots:
1. Header
2. payload
3. signature

### **Header**:
contains metadata about the token, the signing algorithm and the token type
see example:
```json
{
    "alg": "HS256",
    "typ": "JWT"
}
```

- `alg`: algorithm used for signing
- `typ`: type of this token (for JWT will be always JWT)

### **Payload**: 
contains informations about the user and some additional informations like timestamp at when it issued and expiry date of the token 
see example:
```json
{  
    "userId": 123,  
    "role": "admin",  
    "exp": 1672531199  
}
```

these informations are also called "claims" and there's some types of it:
- **`iss` (issuer):** 
	  The server or service that created and signed this token, which the receiver checks to decide whether to trust it.
	  
- **`sub` (subject):** 
	  The unique identifier of the user or entity this token is about, usually a user ID rather than a name or email.
	  
- **`aud` (audience):** 
	  The service or API that this token is meant for, so any other service receiving it must reject it.
	  
- **`exp` (expiration):**
	  defines when the token expires
	  
- **`iat` (issued at):**
	  timestamp when the token was created
	  
- **`nbf` (not before):**
	  Specifies when the token becomes valid.

example in json:
```json
{
	"sub":"1234567890",
	"name":"John Doe",
	"iat":1708345123,
	"exp":1708355123
}
```
which looks like this if you take this and tried to encode it to base64
``` json
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNzA4MzQ1MTIzLCJleHAiOjE3MDgzNTUxMjN9
```

### Signature
the signature ensures token integrity and it's not manipulated, generated using the header,payload and a secret key

Again what signature contains of? :
```
header + payload + secret = signature
```

one of the algorithms uses this way of making the signature which is **HMACSHA256**:
```python
HMACSHA256(  
    base64UrlEncode(header) + "." + base64UrlEncode(payload), 
    secret  
)
```

while other algorithms uses other ways of making thier own signature like these for example:
```python

- HS256:  
HMACSHA256(
	base64url(header) + "." + base64url(payload), secret)

- HS384:  
HMACSHA384(
base64url(header) + "." + base64url(payload), secret)

- HS512:  
HMACSHA512(
base64url(header) + "." + base64url(payload), secret)

- RS256:  
RSASSA-PKCS1-v1_5-SIGN
(SHA256(base64url(header) + "." + base64url(payload)), privateKey)
```


### final example
following these steps of making a JWT token will produce a full token, dot seperated example like this one:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNzA4MzQ1MTIzLCJleHAiOjE3MDgzNTUxMjN9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```
 which decoded to 
 ```json
 {
  "header": {
    "alg": "HS256",
    "typ": "JWT"
  },
  "payload": {
    "sub": "1234567890",
    "name": "John Doe",
    "iat": 1708345123,
    "exp": 1708355123
  },
  "signature": "IJIHǊ(]Oǉ~:N%_u,×"
}
 ```
> Note: for the signature part it's supposed to be in 32 raw bytes and not a text like that `IJIHǊ(]Oǉ~:N%_u,×` thing 


## JWT Authentication work 
https://www.geeksforgeeks.org/web-tech/json-web-token-jwt/

JWT used in authentication in these key steps:
1. **Login request:** 
	   user logs in through the client app (e.g, web browser or mobile app ) by sending their credentials (username + password)
	   
2. **Server generates JWT:**
	   if credentials are correct , the server generates a JWT token using secret key
	   
3. **Return JWT:**
	   server sends back the JWT, which client gonna save 
	   
4. **Further requests with JWT:**
	   for any further requests the client sends the jwt along with the request.
	   The server verifies the JWT before granting access to protected resources.

> ****Note:**** 
> 	JWTs are primarily used for authentication and secure data exchange in web applications and APIs

## Hands-on JWT
here am entering a website to perform some experiments to understand JWT auth better

1. Logged into the app as a normal user (`wiener`), which issued a JWT session cookie.
   
2. Captured that request in Burp’s **Proxy → HTTP history**, sent it to **Repeater**.
   
3. Used Burp’s **Inspector** panel to decode the JWT into its three parts: Header, Payload, Signature.
   
4. Tried accessing `/admin` directly — got denied, since `wiener` isn’t an admin.
   
5. In the JWT **Payload**, changed the `sub` (subject) claim from `wiener` to `administrator`, then clicked **Apply changes** — Burp re-encoded the token automatically, but the **Signature stayed unchanged/invalid** for the new payload.

6. Sent the request to `/admin` with this tampered token. The server **decoded the payload and trusted it without verifying the signature matched** — granting admin access.
   
7. From the admin panel, found the delete link for user `carlos` and sent that request too, deleting the user.

### Best Practices

- **Use HTTPS:** 
	  Encrypt communication between the client and server. In the lab everything was encooded to Base64 which allowed me to read them after decoded
	  
- **store tokens securely:**
	  Prefer HttpOnly cookies over Local Storage for sensitive tokens.
	  
- **set short token expiration:**
	  Keep access tokens short-lived (for example, 15–60 minutes)
	  
- **use refreash token:** 
	  Generate new access tokens without requiring users to log in again.
	  
- **Verify JWT signature:**
	  Always validate the token's signature before trusting its contents.
	  In this one the website lab only looked at the payload and not the signtaure although the signature got changed too after i changed the payload from `sub: "wiener` to `sub: "adminstrator` and the website didnt check for that
	  
- **Avoid senstive data in payload:** 
	  Return appropriate HTTP status codes without exposing sensitive error details.

### Common Issues and errors 
The following are some common errors you may encounter while implementing JWT authentication:

- ****Invalid Signature:**** The signing and verification keys do not match.
  
- ****Token Expired:**** The JWT has passed its expiration time (exp).
  
- ****Malformed Token:**** The JWT is incomplete, corrupted, or has an invalid format.
  
- ****Missing Authorization Header:**** The request does not include the Authorization header.
  
- ****Invalid Bearer Token:**** The Bearer prefix is missing or the token format is invalid.
  
- ****JWT Rejected:**** The server failed to validate the token due to expiration, an invalid signature, or invalid claims.

- ****Insufficient Scope:**** The token lacks the required permissions for the requested action.
  
- ****JWT Decode Failed:**** The token cannot be decoded because it is malformed or improperly encoded.

# JWT in Spring Security (Theory)

These notes connect general JWT theory and the Burp Suite lab to the Spring Security course flow (register → login → authorize). They matter because the lab showed what happens when a server skips signature verification, and the Spring flow is where that check actually lives.

What will be covered:

1. Quick recap of JWT
2. How the signature works
3. What the Burp lab proved
4. Spring Security building blocks (filter chain, `SecurityContextHolder`)
5. Pathway 1: Register
6. Pathway 2: Login
7. Pathway 3: Authorize requests
8. Corrected misunderstandings

---

## 1. Recap: JWT in One Minute

Full notes: [[03-Token_Based_Authentiation_(JWT)#JWT Structure]]

- A JWT is a compact string with 3 dot-separated parts: `header.payload.signature`.
- **Header:** algorithm + token type. **Payload:** claims (`sub`, `iat`, `exp`, ...). **Signature:** proof nothing was changed.
- It is **stateless**: the server stores no session, the client holds the token and sends it with every request.

> **Claim:** one piece of information inside the payload, like `sub` or `exp`.

> **Stateless:** the server keeps no memory about you between requests. Everything it needs is in the token you send.

---

## 2. The Signature (the Part That Protects You)

The signature is an **HMAC computed with the secret key** over the encoded header and payload. It is not a plain concatenation.

> **HMAC:** a keyed hash. Same input + same key always gives the same output, and nobody without the key can produce a matching output.

```python
signature = HMAC_SHA256(
    key = secret,
    data = base64url(header) + "." + base64url(payload)
)
```

Other algorithms follow the same idea with different functions: HS384, HS512 (HMAC with SHA-384/512), and RS256 (RSA signature with a **private key**, verified with a public key).

> **Base64url is encoding, not encryption.** Anyone can decode header and payload without a key. The signature only proves nothing was **changed**; it does not hide anything. So never put secrets in the payload.

Verification = the server recomputes the HMAC from the received header + payload using its own secret, then compares it with the signature inside the token. If different → reject. Then it checks `exp`.

---

## 3. What the Burp Lab Proved

Steps in the lab:

1. Logged in as `wiener`, got a JWT session cookie.
2. Decoded it in Burp Inspector (header / payload / signature).
3. `/admin` was denied for `wiener`.
4. Changed `sub` from `wiener` to `administrator`.
5. The server **accepted it anyway**, so it decoded the payload and trusted it **without verifying the signature**.
6. Used admin access to delete user `carlos`.

> **Note:** when I changed `sub` to `administrator`, the token's signature part no longer matched the new payload (the old signature was computed over the old payload). A server that verifies would recompute the HMAC over the new payload, get a different value than the one in the token, and **refuse the request**. This lab server never compared them.

> **Lesson:** a JWT is only as secure as the verification step. Reading the payload is not verifying the token.

In Spring terms: the `sub` claim becomes the username that the server looks up. If the signature is not checked, whoever edits `sub` becomes that user.

---

## 4. Spring Security Building Blocks

Spring Security works as a **chain of servlet filters** that every request passes through before reaching your controller.

> **Filter:** code that runs before (and after) your controller and can stop the request early.

Key pieces:

- **`SecurityFilterChain` bean:** where you write the rules (which endpoints are open, which filters to add). This is the "who has the security rule" from the course.
- **`AuthorizationFilter`:** the last filter in the chain. It enforces `permitAll()` / `authenticated()` rules.
- **`PasswordEncoder` (BCrypt):** hashes passwords before saving and checks them at login.
- **`UserDetailsService`:** loads a user from your database by username.
- **`AuthenticationManager` / `DaoAuthenticationProvider`:** checks the login credentials using the two items above.
- **`SecurityContextHolder`:** explained below.

> **BCrypt:** a slow, salted password hashing function. Slow on purpose so brute-force attacks are expensive.

### SecurityContextHolder 

It is not a folder. It is a **ThreadLocal** storage: a place that belongs only to the thread handling the current request.

```
SecurityContextHolder
  └── SecurityContext
        └── Authentication
              ├── principal    (the user)
              ├── credentials  (usually null after login)
              ├── authorities  (roles / permissions)
              └── authenticated flag
```

> **ThreadLocal:** a variable where each thread sees its own private copy. One request's user can never leak into another request's thread.

"Setting the authentication on the security context holder" means: after the token is verified, the filter puts an `Authentication` object there. From that moment every later component (`AuthorizationFilter`, `@PreAuthorize`, `@AuthenticationPrincipal`) knows **who this request belongs to**.

With stateless sessions the context is **cleared after every request**. That is exactly why the JWT must be sent with every request.

---
## 5. Intro to JWT flow in spring security
A user's whole life with our API happens in 3 pathways: they sign up, they log in to get a JWT, and then they use that JWT to access protected endpoints. Each pathway is handled by a different part of Spring Security, and only the last one verifies the token.

The 3 pathways:

1. **Register:** `/register` is open to everyone (`permitAll` in the `SecurityFilterChain`). The password is hashed with BCrypt and the user is saved. No token is issued.
2. **Login:** `/login` is also open. Spring checks the credentials against the saved record. If they match, the server signs a JWT and returns it with `200 OK`. If not, `401` and no token.
3. **Authorize requests:** every later request carries the token in `Authorization: Bearer <token>`. The JWT filter verifies the signature and expiry, then sets the user on the `SecurityContextHolder`. Only then can the request reach the controller.

Overview flow:

```
Register  -->  Login  -->  Authorize
 (save user)   (check credentials,   (verify token on every request,
               issue JWT)             set SecurityContext, run controller)
```

> **Key idea:** register and login are the only open doors. After login, the JWT is the user's proof of identity, and the server re-verifies it on every single request because it keeps no session.

---

### Pathway 1: Register

Goal: create the user. **No token is issued here.**

- `/register` is open to everyone: `permitAll()` in the `SecurityFilterChain`.
- The password is **hashed with BCrypt** and only the hash is saved.

```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http, JwtAuthFilter jwtAuthFilter) throws Exception {
    http
        // CSRF attacks abuse cookies that browsers attach automatically.
        // Our token travels in the Authorization header (never auto-attached), so this protection is not needed.
        .csrf(csrf -> csrf.disable())

        // STATELESS = Spring never creates an HttpSession.
        // The SecurityContext is dropped after each request, so every request must carry its own JWT.
        .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))


		// vvv important part vvv
        .authorizeHttpRequests(auth -> auth
            // These two must be open: a user has no token yet when registering or logging in.
            .requestMatchers("/register", "/login").permitAll()
            
            // Everything else needs an authenticated user (someone set in the SecurityContext).
            .anyRequest().authenticated())

        // Place our JWT filter BEFORE the default login filter so the token is checked first
        // and the SecurityContext is already filled when the authorization rules run.
        .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);

    return http.build();
}

@Bean
PasswordEncoder passwordEncoder() {
    // BCrypt adds a random salt per password and is deliberately slow (brute-force protection).
    return new BCryptPasswordEncoder();
}
```

```java
@PostMapping("/register")
public ResponseEntity<Void> register(@RequestBody RegisterRequest req) {
    // Never save the raw password: if the database leaks, hashes are far harder to abuse than plain text.
    // encode() generates a new salt each time, so the same password gives different hashes.
    User user = new User(req.username(), passwordEncoder.encode(req.password()));
    userRepository.save(user);

    // 201 Created: a user was created. Note: no JWT here, that is the login step's job.
    return ResponseEntity.status(HttpStatus.CREATED).build();
}
```

---

### Pathway 2: Login (Authentication)

Goal: prove who you are, then receive a JWT. This version uses the filter approach: login is handled by a custom filter that runs before the request ever reaches a controller. Background on the internals is in [[jwt-login-authentication-flow]].

Steps:

- 1. Client sends username + password to `/login` (open endpoint, no token needed).
- 2. The login filter matches the request and `attemptAuthentication()` builds an unauthenticated `UsernamePasswordAuthenticationToken(username, password)`, then calls `authenticationManager.authenticate(...)`.
- 3. `ProviderManager` hands the token to `DaoAuthenticationProvider`, which loads the user through `UserDetailsService`, then runs `passwordEncoder.matches(rawPassword, storedHash)`. Plain text is never compared.
- 4. Match: `successfulAuthentication()` runs, the server generates a JWT signed with the secret and returns it with `200 OK`.
- 5. No match: `BadCredentialsException` is thrown, the filter catches it and `unsuccessfulAuthentication()` runs, which you map to `401`. No token is issued.

> **Where each job lives:** reading credentials = `attemptAuthentication()`; checking them = `DaoAuthenticationProvider`; issuing the token = `successfulAuthentication()`; error response = `unsuccessfulAuthentication()`. The manager never hashes or issues tokens itself.

> **Open endpoint:** the login path must be allowed without authentication in the security rules, since the user has no token yet.

#### Token generation and key setup (jjwt 0.12.x)

Same in both approaches; only the place it is called from changes (here: inside `successfulAuthentication()`).

```java
// The secret is stored Base64-encoded in config (e.g. application.properties), never hardcoded in code.
// hmacShaKeyFor builds the key object and rejects keys shorter than 256 bits.
private final SecretKey signingKey =
    Keys.hmacShaKeyFor(Decoders.BASE64.decode(secret));

public String generateToken(String username) {
    return Jwts.builder()
        .subject(username)                  // becomes the "sub" claim: what we later use to load the user
        .issuedAt(new Date())               // "iat" claim
        .expiration(new Date(System.currentTimeMillis() + 15 * 60 * 1000)) // "exp" claim: 15 min, limits damage if stolen
        .signWith(signingKey)               // signs header+payload; jjwt picks HS256/384/512 from the key size
        .compact();                         // produces the final header.payload.signature string
}
```

#### Filter vs controller (quick comparison)

- **Controller way:** the controller calls `authenticate()` itself; failure surfaces as an exception inside Spring MVC, so a `@ControllerAdvice` can map it to `401`.
- **Filter way:** the filter calls `authenticate()` inside the security layer, before `DispatcherServlet`; failure goes through `unsuccessfulAuthentication()`, and any other error needs a filter-level handler.
- Both end the same way: verify credentials, then generate a token.

> **Note:** some courses implement login with a custom authentication filter instead of a controller. Both end the same way: verify credentials, then generate a token.

---

### Pathway 3: Authorize Requests

Goal: for every protected request, verify the token and tell Spring who the user is.

The client sends:

```
Authorization: Bearer <token>
```

The JWT filter (`OncePerRequestFilter`, registered with `addFilterBefore(...)` so it runs before `UsernamePasswordAuthenticationFilter`):

1. Read the `Authorization` header. Missing or no `Bearer` prefix → pass the request on untouched.
2. Extract the token and **verify the signature** (recompute the HMAC with the secret, compare) and check `exp`.
3. Read `sub` and load the user via `UserDetailsService`.
4. Build an `Authentication` object and **set it on the `SecurityContextHolder`**.
5. Continue the chain. `AuthorizationFilter` sees an authenticated user and lets the request through.
6. The controller runs and returns `200 OK`.

```java
@Component
public class JwtAuthFilter extends OncePerRequestFilter { // OncePerRequestFilter guarantees one run per request

    private final JwtService jwtService;
    private final UserDetailsService userDetailsService;

    public JwtAuthFilter(JwtService jwtService, UserDetailsService userDetailsService) {
        this.jwtService = jwtService;
        this.userDetailsService = userDetailsService;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain chain) throws ServletException, IOException {

        String header = request.getHeader("Authorization");

        // No token = not our business. We do NOT reject here; we just continue with an EMPTY SecurityContext.
        // AuthorizationFilter later decides: open endpoint -> allowed, protected endpoint -> rejected.
        if (header == null || !header.startsWith("Bearer ")) {
            chain.doFilter(request, response);
            return;
        }

        String token = header.substring(7); // cut "Bearer " (7 chars) to keep only the JWT

        try {
            // Signature check + expiry check happen INSIDE this call (see JwtService below).
            // If the token was tampered with or is expired, it throws and we skip the code after it.
            String username = jwtService.extractUsername(token);

            // Only authenticate if nothing is set yet for this request.
            if (SecurityContextHolder.getContext().getAuthentication() == null) {

                // Load fresh user data (roles, enabled, locked) from the DB instead of trusting the payload for them.
                UserDetails user = userDetailsService.loadUserByUsername(username);

                // 3-argument constructor = AUTHENTICATED token (principal, credentials, authorities).
                // credentials is null: the password is not needed anymore after identity is proven.
                var auth = new UsernamePasswordAuthenticationToken(user, null, user.getAuthorities());

                // Attaches request info (remote IP, session id) to the authentication, useful for audit logs.
                auth.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));

                // THIS is "setting the authentication on the SecurityContextHolder":
                // from here on, the rest of the chain and your controllers know who the user is.
                SecurityContextHolder.getContext().setAuthentication(auth);
            }
        } catch (JwtException | IllegalArgumentException e) {
            // Bad signature, expired, or malformed token: set NOTHING.
            // The context stays empty, so AuthorizationFilter will reject protected endpoints.
        }

        // Always continue the chain, whether we authenticated the user or not.
        chain.doFilter(request, response);
    }
}
```

Verification inside `JwtService` (this is the step the lab server skipped):

```java
public String extractUsername(String token) {
    return Jwts.parser()
        // Tells the parser which key to recompute the HMAC with. Also binds the algorithm to this key,
        // so a forged header like "alg": "none" cannot downgrade the check.
        .verifyWith(signingKey)
        .build()
        // The "Signed" in the name means unsigned tokens are rejected.
        // Throws SignatureException if the signature does not match (the Burp lab case)
        // and ExpiredJwtException if "exp" has passed.
        .parseSignedClaims(token)
        .getPayload()
        .getSubject(); // the "sub" claim, only reachable AFTER verification succeeded
}
```

Important details:

- **Who sends the 200?** Your controller, not the filter. The filter only decides who the user is.
- **Invalid token:** the filter sets nothing and moves on. `AuthorizationFilter` then rejects the request. By default this can come back as `403`; configure an `AuthenticationEntryPoint` if you want an explicit `401`.
- **Key size:** HS256 needs a secret of at least 256 bits (32 bytes); jjwt rejects weaker keys.

---
## 6. Exception handling + Dispatcher servlet

 These notes explain why DispatcherServlet (or controller) cannot handle exceptions thrown by Spring Security filters, and how a custom `ExceptionHandlerFilter` fixes it. It matters because without it, errors in the security layer return confusing responses (like 403) instead of a clean 400.

What is covered:

- The problem: two "doors" and where global exceptions live
- The fix: a filter placed first in the chain
- The code
- A gotcha that caused a 403
- The big picture flow

### The Problem

Global exception handling (`@ControllerAdvice` / `@ExceptionHandler`) only works **inside the `DispatcherServlet`**, which is the part that routes requests to controllers. Security filters run **before** the `DispatcherServlet`. So if a request fails in a filter, it never reaches the controller area, and the global handler never sees the exception.

shown this diagram explains the problem
![[Untitled Diagram.jpg]]



House analogy: door 1 is the security filters, door 2 is the controller area where the exception handler lives. A request kicked out at door 1 never reaches door 2.

> **DispatcherServlet**: Spring MVC's front controller; it receives requests and sends them to the right controller method. **Global handler**: a class annotated with `@ControllerAdvice` that turns exceptions into HTTP responses.

### The Fix

Add our own filter that sits **first** in the chain. It does no checking on the way in. It just wraps everything after it in a `try/catch` (a safety net). Any exception thrown further down the chain travels back up to it, and it turns the exception into a response.

Shown diagram where to put our ExcecptionHandlerFilter:
![[Untitled Diagram.drawio.png]]
> **Safety net**: code that catches failures after they happen instead of preventing them. **Chain order**: filters form a list; the first one is the outermost wrapper, so every later exception passes through it on the way out.


> **addFilterBefore / addFilterAt / addFilterAfter**: place a filter relative to a known filter in the list. **addFilter**: add a filter at its natural position; it must extend a filter Spring Security already knows.

### The Code

The filter:

java

```java
public class ExceptionHandlerFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
            throws ServletException, IOException {
            
            
        try {
            // pass the request down the chain (AuthenticationFilter and the rest run inside this call)
            // .doFilter will go to the next filter which is the AuthenticationFiler
            filterChain.doFilter(request, response);
            
            
        } catch (RuntimeException e) {
            // an exception came back up the chain: answer with 400 instead of crashing
            response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
        }
    }
}
```

Registering it in the security config:
java
```java
http
    // run ExceptionHandlerFilter BEFORE AuthenticationFilter, so it wraps it
    .addFilterBefore(new ExceptionHandlerFilter(), AuthenticationFilter.class)
    
    // add our custom login filter (it extends UsernamePasswordAuthenticationFilter)
    .addFilter(authenticationFilter);
```

Where the exception comes from (inside `AuthenticationFilter`):
java
```java
try {
    // read the JSON body and map it to the User entity
    User user = new ObjectMapper().readValue(request.getInputStream(), User.class);
} catch (IOException e) {
    // bad JSON or unknown field names: rethrow so it travels up to ExceptionHandlerFilter
    throw new RuntimeException();
}
```


see full version example of SecurityConfig code:
```java
@Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {

        
        AuthenticationFilter authenticationFilter = new AuthenticationFilter();
        authenticationFilter.setFilterProcessesUrl("/authenticate");

        http
            .headers(headers -> headers.frameOptions().disable())
            .csrf(csrf -> csrf.disable())
            .authorizeRequests(requests -> requests
                
                .antMatchers("/h2/**").permitAll() 
                .antMatchers(HttpMethod.POST,SecurityConstants.REGISTER_PATH).permitAll()
                .anyRequest().authenticated())
                
                // here's our part exactly
                .addFilterBefore(new ExceptionHandlerFilter(), AuthenticationFilter.class) 
                .addFilter(authenticationFilter)
                

            .sessionManagement(management -> management.sessionCreationPolicy(SessionCreationPolicy.STATELESS));
        return http.build();
    }
    
}
```


### Full codes example
full version of AuthenticationFiler code:
```java

public class AuthenticationFilter extends UsernamePasswordAuthenticationFilter{

    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response)
            throws AuthenticationException {

        try{
		        // here it where to read the json and map it , if something wrong here it will 
		        // throw our exceptionHandlerFilter
                User user = new ObjectMapper().readValue(request.getInputStream(), User.class);
               

                System.out.println(user.getPassword());
                System.out.println(user.getUsername());
                // these are just for testing to see credintials if they arrived or not 
        
        }catch(IOException e){
            throw new RuntimeException();
        }

        return super.attemptAuthentication(request, response);
    }
}
```

full version of ExceptionHandlerFilter:
```java

public class ExceptionHandlerFilter extends OncePerRequestFilter{

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
        
            try{
	            // here where it goes to next filters
                filterChain.doFilter(request, response);
                
                // and this catches the error and deal with it , in this case it just 
                // sends back a 400 response
            }catch(RuntimeException e){
                System.out.println(e.getMessage());
                response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
            }
    }

}
```


to send a message to the user through your exception, add these lines in the body code of catch
```java
}catch(RuntimeException e){
                System.out.println(e.getMessage());
                response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
                
                response.getWriter.write("you got Bad_Request buddy");
                response.getWriter.flush();
            }
```


### Limits to Remember

- The catch only sees exceptions that **travel back up** to it. A wrong password (`AuthenticationException`) is handled inside `AbstractAuthenticationProcessingFilter` (401), so it never reaches our filter.
  
- An unauthenticated request to a protected URL is handled by `ExceptionTranslationFilter`, not by ours.
  
- `setStatus(400)` sends only a status code with no body, and the exception is swallowed (the stack trace is lost unless logged).





##  Corrected Misunderstandings

- "`header + payload + secret = signature`" is loose. The signature is an **HMAC computed with** the secret over the encoded header and payload.
- "Everything is Base64, so I could read it" means encoding, not encryption. HTTPS and Base64 solve different problems.
- "The security rule `permitAll` lives somewhere unknown" lives in the **`SecurityFilterChain` bean**, enforced by `AuthorizationFilter`.
- "Context folder" is a **ThreadLocal** holding an `Authentication` object.
- Register issues **no** token. Login issues it.
- The JWT filter does not send `200 OK`. The controller does.

---

### Official Sources

- https://docs.spring.io/spring-security/reference/servlet/architecture.html
- https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html
- https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html
- https://datatracker.ietf.org/doc/html/rfc7519


# JWT in Spring Security (Code)
here we gonna implement the pathways we explained in these topics
- [[03-Token_Based_Authentiation_(JWT)#Pathway 1 Register|Register]]
- [[03-Token_Based_Authentiation_(JWT)#Pathway 2 Login (Authentication)|Log in]]
- [[03-Token_Based_Authentiation_(JWT)#Pathway 3 Authorize Requests|Authoriztion]]

## Register Implementation
### Recap 
we had an endpoint which is `/register` which permits all requests in order to be able to take user credentials without the need to authenticate. Then credentials will be executed (password hashed) then saved on the database.

So the point here is to make an endpoint open for anyone.

### implementation

steps:
1. we gonna add the permitAll to register endpoint [[#^reg-step-1|click here]]
2. save user credentials to the database [[#^reg-step-2|click here]]
3. encrypting password on saving to database; so no to plain passwords on databases [[#^reg-step-3|click here]]


Step 1: permitting register
```java 

@Configuration
@AllArgsConstructor
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .headers(headers -> headers.frameOptions().disable())
            .csrf(csrf -> csrf.disable())
            
            .authorizeRequests(requests -> requests
                .antMatchers("/h2/**").permitAll() // New Line: allows us to access the h2 console without the need to authenticate. ' ** '  instead of ' * ' because multiple path levels will follow /h2.
                
                //  vvvvvv this line vvvvvvvv
                .antMatchers(HttpMethod.POST,SecurityConstants.REGISTER_PATH).permitAll()
                // here i told the filter that any post request goes to the path of register
                // as example "/user/register/" will be permitted for all users  
                
            
                .anyRequest().authenticated())
                // BUT any request will requierd to be authenticated 
            .sessionManagement(management -> management.sessionCreationPolicy(SessionCreationPolicy.STATELESS));
        return http.build();
    }
    
}
```
^reg-step-1

step 2: saving user to database
```java
@RestController
@RequestMapping("/user")
public class UserController {


    UserService userService;

	@GetMapping("/{id}")
	public ResponseEntity<User> findById(@PathVariable Long id) {
		return new ResponseEntity<>(HttpStatus.OK);
	}

    @PostMapping("/register")
	public ResponseEntity<User> createUser(@Valid @RequestBody User user) {
	
		//                          vvvvvvvvvvvvvvvvvvvvvvvvv
		return new ResponseEntity<>(userService.saveUser(user), HttpStatus.CREATED);
		//this line-----------------^^^^^^^^^^^^^^^^^^^^^^^^^^---------------------
		// just added the user have to be saved after being registerd 
	}

}
```
^reg-step-2

step 3: encrypting the password before saving to database
>Note: some codes are deleted in order to focus on the important part (dont worry nothing important got deleted :) )
```java 

import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
@Service
//@AllArgsConstructor
public class UserServiceImpl implements UserService {

    private UserRepository userRepository;
	private BCryptPasswordEncoder bCryptPasswordEncoder;

    @Override
    public User saveUser(User user) {
    
    //  vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
        user.setPassword(bCryptPasswordEncoder.encode(user.getPassword()));
    // this calls bCrypt to encrypt the data before being saved to database    
    
        return userRepository.save(user);
    }
    
}
```
^reg-step-3

now try to send via postman a post request with this JSON body:
```json
{
	"username": "anything",
	"password": "anyhting295"
}
```

and you should get 201 created response and now you are done with this part 

## Logging in 
### Recap
after the user created their account , the credentials will be saved in the database, then comes the logging in part which gonna ask the user to enter that credentials again in order to respond with the token. 
- if credentials are correct: it will respond with the token that gonna be saved in user's browser 
- if credentials are wrong: it will not respond and not token is created 

so what happens is:
1. our `AuthFilter` will have a method called `attemptAuth` that will contain the credentials of the user 
2. then this object that contains the creds will be sent over to `AuthManager` 
3. the `AuthManager` gonna perform a hash to the password then compare it to the hash found in the records
	1. if matched: it's gonna return the authentication object and upon that it will call `successAuth()` which is gonna create the JWT token, then sends back the JWT token to the user 
		1. In details: `successfulAuthentication()` will return the payload but mixed with the signtature which gonna be the jwt token it self (header+payload+signature)
	2. if not matched : nothing is returned and no token is created 
### implementation
steps :
1. impelement the `attmeptAuthentications` [[#^login-step-1|click here]]
2. Add the written filter in `filterChain()` [[#^login-step-2|click here]]
3. Implement the custom authentication manager in `CustomAuthenticationManager` [[#^login-step-3-1|click here]]
	1. Create the manager module first [[#^login-step-3-1|click here]]
	2. edit the `authenticationFilter` to return creds to manager [[#^login-step-3-2|click here]]
4. add the `successfulAuthentication` inside `AuthenticationFilter` which gonna invoked if password hashes are match [[#^login-step-4|click here]]
5. add the `unSuccessfulAuthentication` inside the same file which gonna be invoked if password hashes didnt match [[#^login-step-5|click here]]



step 1: implementing the `attmeptAuthentications`:
inside `project/security/filter/AuthenticationFilter.java`
>Note: this step is to show if creds arrives, follow the steps to see when we send creds to manager
```java 


public class AuthenticationFilter extends UsernamePasswordAuthenticationFilter{
    

    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response)throws AuthenticationException {

//      vvvvvvvvvvvvvvvvvvvvv this line vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv


	try{
		User user = new ObjectMapper().readValue(request.getInputStream(), User.class);
// this line will strip out the credintials from the request as a binary (binary cuz it's using streaming) then read it and map it to the entity user.java

// the request should have the exact field names as user.java otherwise it will fire a RunTimeException

		
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
		System.out.println(user.getPassword());
		System.out.println(user.getUsername());
		// these are just for testing to see credintials if they arrived or not 
	
        }catch(IOException e){
            throw new RuntimeException();
            // this will be fired in case of one of the fields didnt match 
        }

        return super.attemptAuthentication(request, response);
    }
}

```
^login-step-1

>Note: to confirm what i meant by "matching fields".
>assuming that user.java got a field called "password". if the request sends this field but written in a diffirent way like "passwordddd" it will be rejected  



step 2: Add the written filter in `filterChain()`
```java

@Configuration
@AllArgsConstructor
public class SecurityConfig {

	// ---------vvvvvvvvvvvvv-------------------
	// this will be disscussed further
	CustomAuthenticationManager customAuthenticationManager;
	

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {

//  vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
        AuthenticationFilter authenticationFilter = new AuthenticationFilter(customAuthenticationManager);
        //needed a variable to hold the filter, so you could call a setter on it before adding it to the chain
        //EDIT: added a customAuthenticationManager object on this object cuz it's not expecting as to return that
        
        authenticationFilter.setFilterProcessesUrl("/authenticate");
        // tells the filter “only react to `POST /authenticate`”.
// ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

        http
            .headers(headers -> headers.frameOptions().disable())
            .csrf(csrf -> csrf.disable())
            .authorizeRequests(requests -> requests
                
                .antMatchers("/h2/**").permitAll() // New Line: allows us to access the h2 console without the need to authenticate. ' ** '  instead of ' * ' because multiple path levels will follow /h2.
                .antMatchers(HttpMethod.POST,SecurityConstants.REGISTER_PATH).permitAll()
                .anyRequest().authenticated())
                
//           vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv  
                .addFilter(new AuthenticationFilter())
//          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
// this line gonna add the filter we just implememnted              
                
             

            .sessionManagement(management -> management.sessionCreationPolicy(SessionCreationPolicy.STATELESS));
        return http.build();
    }
    
}
```
^login-step-2



3.1. Implement the custom authetication manager in `CustomAuthenticationManager`
inside `<YOUT-PROJECT>/security/manager/CustomAuthenticationManager.java`

>Note: the user isnt checked here cuz we added to `userService.getUser()` to user `Optional<User>` which gonna deal with it (see incoming notes for better understanding)
```java
@Component 
public class CustomAuthenticationManager implements AuthenticationManager{

    private UserService userService;
    private BCryptPasswordEncoder bCryptPasswordEncoder;

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
            
        // here we received the the user creds and fetched from records by name
        User user = userService.getUser(authentication.getName());
        //NOTE: we are not checking if the user is exist in records cuz we already put a Optional<User> in userService.getUser()
        //      and that in case of username was null it will throw a EntityNotFoundException and nothing reaches here

        // compare if hashed password of the record and request. if not matched then an excpetion will be thrown
        if(!bCryptPasswordEncoder.matches(authentication.getCredentials().toString(), user.getPassword())){
            throw new BadCredentialsException("Wrong password Nigga");
        }
		
        // if the flow reaches here then it means everything is great and user is authenticated 
        return new UsernamePasswordAuthenticationToken(authentication.getName(), user.getPassword());
    }

}

```
^login-step-3-1

3.2. edit the `authenticationFilter` to return creds to manager
```java
@AllArgsConstructor 
public class AuthenticationFilter extends UsernamePasswordAuthenticationFilter{

   
    private AuthenticationManager authenticationManager;

    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response)
            throws AuthenticationException {
        try{
                User user = new ObjectMapper().readValue(request.getInputStream(), User.class);
                
	    // ---------------- vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv --------------------------------
                // we need the authenticationManager to fetch the credintials to see them so here we passing 
                // user creditnals to it 
                Authentication authentication = new UsernamePasswordAuthenticationToken(user.getUsername(), user.getPassword());
                return authenticationManager.authenticate(authentication);
		// ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


        }catch(IOException e){
            // System.out.println(e.getMessage());
            throw new RuntimeException();
        }
        //----------VVVVVVV DELETED VVVVV-----------------
        //return super.attemptAuthentication(request, response);
        // ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    }
}
```
^login-step-3-2



4. add the `successfulAuthentication` which gonna invoked after returning the authentication object
inside `<YOUR-PROJECT>/security/filter/AuthenticationFilter.java`
>Note: here where token is given to user finally 
```java
  @Override
    // if you reach here that means your password matches the records, congrats
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain,
            Authentication authResult) throws IOException, ServletException {
                // our string token 
                String token = JWT.create()

                    // has our subject of authenticated user 
                    .withSubject(authResult.getName())

                    // time of expiration, which gonna be in milliseconds 
                    .withExpiresAt(new Date(System.currentTimeMillis() + SecurityConstants.TOKEN_EXPIRATION))

                    // sign our jwt token which holds the secret key using the HMAC512 algorithm
                    .sign(Algorithm.HMAC512(SecurityConstants.SECRET_KEY));

                // Adds the HTTP response header "Authorization: Bearer <jwt>" so the client
                // can store the token and send it back on later requests
                response.addHeader(SecurityConstants.AUTHORIZATION, SecurityConstants.BEARER + token);
    }
}
```
^login-step-4


5. some

```java
@Override
    protected void unsuccessfulAuthentication(HttpServletRequest request, HttpServletResponse response,
            AuthenticationException failed) throws IOException, ServletException {
        
        // we dont want to return (200 OK) so we gotta update this one
        response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);

        //just some contents to show cause of failure
        response.getWriter().write("you're not authorized");
        response.getWriter();
    }
```
^login-step-5


EXTRA STEP 1 : here's the point where i put user service to use `Optional<User>`
here i dealt with this problem earlier that if nothing returns there's a method that cheks
```java
@Service
@AllArgsConstructor
public class UserServiceImpl implements UserService {

// ---- code snippets -------
	// here it tries to get the user and if not found it will throw an exception
	static User unwrapUser(Optional<User> entity, Long id) {
        if (entity.isPresent()) return entity.get();
        else throw new EntityNotFoundException(id, User.class);
    }

    @Override
    public User getUser(String username) {
        // this gonna try to find the user from the repo 
        Optional<User> user = userRepository.findByUsername(username); // <<<<< this line
        return unwrapUser(user, 404L);
    }
    
```

EXTRA STEP 2: 
i also updated the exception to throw a `EntityNotFoundExcpeiton` when username doesnt exist
```java
public class ExceptionHandlerFilter extends OncePerRequestFilter{

    
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
        // -------vvvvvvvvvvvvvvvvvvvvvvvvvvvvvv----------------
            try{
                filterChain.doFilter(request, response);
                
            }catch(EntityNotFoundException e){
            // if username not exist
                System.out.println(e.getMessage());
                response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
                response.getWriter().write("ENTITY NOT FOUND");
                response.getWriter();
		// --------^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^-----------------
            }catch(RuntimeException e){
            // if fields was wrong
                System.out.println(e.getMessage());
                response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
                response.getWriter().write("BAD REQUEST");
                response.getWriter();

            }
    }
}
```

EXTRA Step3: this is the constants i used in the course so u dont get lost with them 
```java

public class SecurityConstants {
	 //Your secret should always be strong (uppercase, lowercase, numbers, symbols) so that nobody can potentially decode the signature.    
    public static final String SECRET_KEY = "bQeThWmZq4t7w!z$C&F)J@NcRfUjXn2r5u8x/A?D*G-KaPdSgVkYp3s6v9y$B&E)";
    
    public static final int TOKEN_EXPIRATION = 7200000; // 7200000 milliseconds = 7200 seconds = 2 hours.
    public static final String BEARER = "Bearer "; // Authorization : "Bearer " + Token 
    public static final String AUTHORIZATION = "Authorization"; // "Authorization" : Bearer Token
    public static final String REGISTER_PATH = "/user/register"; // Public path that clients can use to register.
}
```