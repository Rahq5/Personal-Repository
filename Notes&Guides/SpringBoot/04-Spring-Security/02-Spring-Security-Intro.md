# Introduction

## Authentication and Authorization 

**Basic Define of Basic Auth:** is to steps of defining user identity and access
- **Authentication:** defines user identity
- **Authorization:** defines what user can access

Explaining: 
HTTP has a two sections: Header and Body. The Header holds metadata of the request while body contains the content being delivered (only used in POST,PUT requests). 

so the process of Basic Auth in steps:
1. The API requires Authentication (user identity) before processing client's request
   
2. The client's HTTP request needs to include credentials in the header 
	   HTTP headers has a parameter called "Authorization" which contains the method of Auth which can be basic, JWT, bearer Token, etc. Also before sending the credentials it will be encoded in form Base64 

3. Authentication is where spring security validates the identity of the client
	   Example: Lets say you requested to login but you didnt log your credentials, this will cause the server to response with 401 (UNAUTHORIZED).
	   And if happened and logged successfully is it will send a 201

4. Authorization: spring security determines what the client has access to 
	   after logging in successfully, the server should know who you are and edit your access.
		   Example: if your role was a "USER" and attempted to send Post request for admin role things, you will be responded with a 403 (FORBIDDEN).


# Basic Auth 

## Flow Diagram of Authentication
![[Screenshot From 2026-10-01 00-09-09.png|300|]]

1. **Unauthenticated request** :
	1. hits a protected URL. Browser sends `GET /dashboard` with no login info.
	   
	2. AuthorizationFilter blocks it. It sees no authenticated user and rejects the request.

	3. ExceptionTranslationFilter catches the rejection. It's the component that turns a security failure into an HTTP response instead of crashing.
	   
	4. LoginUrlAuthenticationEntryPoint takes over. It saves the original URL you wanted, then sends a `302` redirect to `/login`.
	
	5. now user is redirected to the Login Form.
	   
2. **Authenticated request:**
	1. You land on the login form. This closes the first scenario.
	   
	2. You submit the form. Browser sends `POST /login` with username and password.
	   
	3. UsernamePasswordAuthenticationFilter catches the submission. It packages your input into an unauthenticated token (who + proof, no permissions yet).
	   
	4. ProviderManager routes the token. It picks the right provider for this token type — here, `DaoAuthenticationProvider`.
	   
	5. DaoAuthenticationProvider does the real check. It asks `UserDetailsService` to fetch the stored user, then asks `PasswordEncoder` to compare your password against the stored hash.
	   
3. **Two outcomes split here:**
    - **Success (3a):** a new, fully-authenticated token is built. `SecurityContextHolder` stores it, and it's saved into your `HttpSession` (this is the step `STATELESS` would skip). You're redirected back to `/dashboard`, and a session cookie is set.
      
    - **Failure (3b):** `AuthenticationFailureHandler` redirects you back to `/login?error` with a generic message — it won't say _why_ it failed, so attackers can't tell if the username or password was wrong. An event is also published internally, which other code can listen for (e.g. to count failed attempts or trigger a lockout).

## Implementing SecurityConfig
```java
@Configuration
@AllArgsConstructor
public class SecurityConfig {

    private BCryptPasswordEncoder bCryptPasswordEncoder; // encoder bean from ContactsApplication.java

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable() // turns off CSRF token checks — needed or Postman DELETE fails
            .authorizeRequests()
            .antMatchers(HttpMethod.DELETE, "/delete/*/contact").hasRole("ADMIN")
            .antMatchers(HttpMethod.GET).permitAll() // allows all users (auth and un-auth) to send GET
            .anyRequest().authenticated() // everything else just needs to be logged in
            .and()
            .httpBasic() // enables Basic Auth header checking
            .and()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
            // no session created — credentials re-checked on every single request

        return http.build();
    }

    @Bean
	public UserDetailsService users() {                 // Spring Security calls this to load users by username
	    UserDetails admin = User.builder()               // Spring's built-in user object builder
	        .username("admin")                           // login name
	        .password(bCryptPasswordEncoder.encode("12345")) // hash the password, never store it raw
	        .roles("ADMIN")                               // case-sensitive: stored as ROLE_ADMIN
	        .build();                                     // creates the immutable UserDetails

        // CAUTION: never hardcode real passwords in source code, even hashed ones — this is only for practice

	    UserDetails user = User.builder()
	        .username("user")
	        .password(bCryptPasswordEncoder.encode("12345"))
	        .roles("User")                                 // bug: stored as ROLE_User, not ROLE_USER
	        .build();

    return new InMemoryUserDetailsManager(admin, user); // stores users in RAM, lost on restart
}
}
```

**What changed and why:**

1. **CSRF disabled.** Without this, `CsrfFilter` blocked every DELETE/POST/PUT with no CSRF token — even from the admin. Testing from Postman, so disabling is fine here, but it's a just for experimenting code, dont do that in production.
   
2. ```.authorizeRequests()```: opens the section where you declare URL rules. It does not authenticate anyone and does nothing when a request arrives. Deprecated (marked as outdated), see "Old vs new".
    
3. **New rule: `antMatchers(DELETE).hasRole("ADMIN")`.** Before, any logged-in user could delete. Now only `admin` can — `user` gets 403, no login gets 401. Weak point: no path given, so it applies to every DELETE on any URL, not just `/contact/{id}`. Rule order matters — specific rules must come before `anyRequest()`, since the first match wins.
   
	1. `.antMatchers(HttpMethod.DELETE, "/delete/*/contact").hasRole("ADMIN")`: also this works when i specialize a path that only a role can use it, the aestrick `"/*/"` in the path that to replace parts which security cant read or know what is between curly braces like in our case `/{id}/` so u replace it with an asetrick
    
4. **`STATELESS` added.** This is the fix for the earlier cookie bug: after one correct login, Spring kept a session, so even a wrong password on a later request still returned 200. `STATELESS` stops Spring from creating or using that session, so every request must prove its credentials again. The cookie isn't deleted — it's just ignored now.
    
5. **Password hashing at startup.** `"12345"` gets hashed once when the app boots, so only the hash is held in memory. Hashes differ between restarts because of the random salt.
    
6. **Role casing bug carried over.** `.roles("User")` stores `ROLE_User`, not `ROLE_USER`. Any future `hasRole("USER")` check will reject this account. Fix is either renaming the role to `"USER"` or using `hasAnyRole("ADMIN", "User")`.
    
7. **Users still in-memory.** Fine for practice, lost on restart, not for production.
   
8. **Fix to note:** `.roles()` is case-sensitive — Spring only adds the `ROLE_` prefix, it doesn't normalize the case you pass in. So `"ADMIN"` → `ROLE_ADMIN`, but `"User"` → `ROLE_User`, not `ROLE_USER`. Any check like `hasRole("USER")` would reject this account, since it's a plain string match under the hood. Keep role names consistently uppercase (`"USER"`, `"ADMIN"`) to avoid this.



## CSRF

> **CSRF (Cross-Site Request Forgery):** an attacker tricks an already-logged-in user's browser into sending an unwanted request to a trusted site. The browser attaches the credentials automatically.

- The attacker never learns the password, and the admin is a real but unaware (unwitting) person. Only the browser is tricked into firing the request.
- Defense: the server requires a secret CSRF token (header `X-CSRF-TOKEN`) that the attacker's page cannot read.
- `CsrfFilter` checks only **unsafe methods** (POST, PUT, PATCH, DELETE). GET is safe and skipped, which is why GET always worked and DELETE was blocked, even for the admin.
- It runs **before** `BasicAuthenticationFilter`, so your role rule was never even reached.
- `.csrf().disable()` lowers the shield. Spring's docs say it is reasonable for services used only by non-browser clients. Browsers cache Basic Auth credentials and resend them automatically, so if a browser ever calls this API the risk returns.

## BCrypt

> **Hashing (BCrypt):** a one-way transformation. You cannot reverse a hash back to the password. Login hashes the typed password and compares it with the stored hash. BCrypt **hashes**, it does not encrypt (encryption can be reversed with a key).

> **Salt:** random data added per password, so two users with the same password get different hashes.

> **Cost factor:** BCrypt is deliberately slow, which makes brute-force guessing expensive.


