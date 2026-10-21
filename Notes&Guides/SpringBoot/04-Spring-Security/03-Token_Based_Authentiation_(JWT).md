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

Goal: prove who you are, then receive a JWT.

Steps:

1. Client sends username + password to `/login` (open endpoint).
2. The app builds `UsernamePasswordAuthenticationToken(username, password)` and calls `authenticationManager.authenticate(...)`.
3. `DaoAuthenticationProvider` loads the user through `UserDetailsService`, then runs `passwordEncoder.matches(rawPassword, storedHash)`. Plain text is never compared.
4. **Match:** the server generates a JWT signed with the secret and returns it with `200 OK`.
5. **No match:** `BadCredentialsException`. You map it to `401`. **No token is issued.**

```java
@PostMapping("/login")
public ResponseEntity<AuthResponse> login(@RequestBody LoginRequest req) {
    // This unauthenticated token only carries the raw credentials INTO the AuthenticationManager.
    // Internally: UserDetailsService loads the user, then BCrypt matches raw password vs stored hash.
    // On failure it THROWS (BadCredentialsException), so the next line never runs.
    authenticationManager.authenticate(
        new UsernamePasswordAuthenticationToken(req.username(), req.password()));

    // Only reached if the credentials were correct: now it is safe to issue a token.
    String token = jwtService.generateToken(req.username());
    return ResponseEntity.ok(new AuthResponse(token));
}
```

Token generation and key setup (jjwt 0.12.x):

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

> **Check your course:** some courses implement login with a custom authentication filter instead of a controller. Both end the same way: verify credentials, then generate a token.

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

## 6. Corrected Misunderstandings

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

## Register Implmentation
### Recap 
we had an endpoint which is `/register` which permits all requests in order to be able to take user credentials without the need to authenticate. Then credentials will be executed (password hashed) then saved on the database.

So the point here is to make an endpoint open for anyone.

### implementation

steps:
1. we gonna add the permitAll to register endpoint
2. save user credentials to the database 
3. encrypting password on saving to database; so no to plain passwords on databases


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

### implementation
steps :
1. 
