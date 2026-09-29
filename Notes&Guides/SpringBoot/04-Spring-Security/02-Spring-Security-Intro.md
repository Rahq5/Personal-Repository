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

# Building SecurityChainFilter

## Spring Security: Filter Chain, Roles, BCrypt, Sessions and CSRF

These notes cover how the Contacts app is secured: the filter chain, in-memory users, BCrypt, stateless sessions, CSRF, and the first role rule (only admins can DELETE). They matter because the code looks like a list of settings, but each line changes what happens to every request, and the order of things is easy to forget after a few weeks.

What will be covered (in this order):

1. Intro: what we are learning (this section)
2. Code explained line by line: `SecurityConfig.java` and `ContactsApplication.java` (what each line does, what changed after adding it)
3. Theory: how everything works under the hood (startup vs request, filters, authentication vs authorization, BCrypt, sessions, CSRF)
4. Old vs new syntax (deprecated code vs Spring Security 6 code)
5. Big-picture flow diagram and official docs

> **Course goal (5 rules):** 
> 1) DELETE `/contact/{id}` needs authentication. 
> 2) Only admins can DELETE `/contact/{id}`. 
> 3) POST `/contact` needs authentication. 
> 4) Admins and users can POST `/contact`. 
> 5) GET is open to everybody. Part 1 is done, part 2 is in progress.

---

### Code 1: SecurityConfig.java

```java
package com.ltp.contacts.security;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;

import lombok.AllArgsConstructor;

@Configuration
@AllArgsConstructor
public class SecurityConfig {

    private BCryptPasswordEncoder bCryptPasswordEncoder;

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeRequests()
            .antMatchers(HttpMethod.DELETE).hasRole("ADMIN")
            .anyRequest().authenticated()
            .and()
            .httpBasic()
            .and()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
            // STATELESS: Spring never creates or uses a session, so credentials are checked on every request

        return http.build();
    }

    @Bean
    public UserDetailsService users() {
        UserDetails admin = User.builder()
            .username("admin")
            .password(bCryptPasswordEncoder.encode("12345"))
            .roles("ADMIN")
            .build();

        UserDetails user = User.builder()
            .username("user")
            .password(bCryptPasswordEncoder.encode("12345"))
            .roles("User")
            .build();

        return new InMemoryUserDetailsManager(admin, user);
    }
}
```


- ```private BCryptPasswordEncoder bCryptPasswordEncoder;```: the encoder bean that is declared in ```ContactsApplication.java```. Better practice is to type it as the ```PasswordEncoder``` interface.

- ```@Bean public SecurityFilterChain filterChain(HttpSecurity http) throws Exception```: runs **once at startup**, not per request.
    - What it does: Spring gives you an empty ```HttpSecurity``` builder, you write rules on it, and the returned chain is registered.
    - Before: with no such bean, Spring Boot's default secures everything with a form login page, HTTP Basic, and a random password printed in the console log.
    - After: your rules replace the default.

- ```.csrf().disable()```: turns the CSRF protection filter off.
    - Before: ```CsrfFilter``` blocked every DELETE (also POST, PUT, PATCH) that had no CSRF token, even for the admin. You saw ```401``` in Postman (Spring normally answers a missing token with ```403```, so the 401 is unexplained; next time check the response body and TRACE logs).
    - After: the admin's DELETE worked. Practice API tested from Postman, so acceptable here, but disabling is a decision, not a fix.

- ```.authorizeRequests()```: opens the section where you declare URL rules. It does not authenticate anyone and does nothing when a request arrives. Deprecated (marked as outdated), see "Old vs new".

- ```.antMatchers(HttpMethod.DELETE).hasRole("ADMIN")```: the new rule for course part 2.
    - What it does: every DELETE request needs the authority ```ROLE_ADMIN```. ```hasRole``` adds the ```ROLE_``` prefix for you.
    - Before: any logged-in user could DELETE.
    - After: ```admin``` gets success, ```user``` gets ```403``` (known but forbidden), no credentials gets ```401``` (unknown).
    - Weak point: no path is given, so it covers **every** DELETE on any URL. The course wants ```/contact/{id}```: ```.antMatchers(HttpMethod.DELETE, "/contact/**")```.
    - Rule order matters: the first matching rule wins, so specific rules go **before** ```anyRequest()```.

- ```.anyRequest().authenticated()```: the catch-all rule, it must be **last**. Everything not matched above needs a logged-in user. It is only a rule, the actual login is done by ```.httpBasic()```.
    - Open issue: course rule 5 (GET open to everybody) conflicts with this line. GET currently needs login, so a ```permitAll``` rule for GET will have to be added before it.

- ```.and()```: goes back to the ```HttpSecurity``` object so the chain can continue. Deprecated in newer versions.

- ```.httpBasic()```: adds ```BasicAuthenticationFilter```, which reads the ```Authorization: Basic base64(username:password)``` header on each request.
    - Before: Boot's default also showed a login form. Since you never call ```formLogin()```, only Basic exists now.
    - After: a request without valid credentials gets ```401``` with a ```WWW-Authenticate: Basic``` header.

- ```.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)```: the fix for the cookie trap.
    - Before: after the first successful login Spring created a session and Postman stored the ```JSESSIONID``` cookie. Later requests reused the session, so a wrong password still returned ```200```.
    - After: no session is created or used, so the password is checked on every request. The cookie is not deleted, it is simply ignored. (The old comment in your code said "deletes the cookie", which is not accurate.)

- ```return http.build();```: builds the chain once and returns it as a bean. It is a ```DefaultSecurityFilterChain```: a request matcher plus an ordered list of filters.

- ```@Bean public UserDetailsService users()```: Spring Security asks this bean to look up a user by username during login.
    - Before: Boot generated one user named ```user``` with a random password shown in the log.
    - After: because you define this bean, that default backs off and your two users are used.

- ```UserDetails admin = User.builder()```: Spring's own ```User``` class (not your entity), used to build an account record.

- ```.username("admin")```: the login name.

- ```.password(bCryptPasswordEncoder.encode("12345"))```: hashes the password when the app starts, so only the hash is kept in memory.
    - Before (plaintext ```"12345"```): login threw ```There is no PasswordEncoder mapped for the id "null"```, because Spring expects a ```{bcrypt}```-style prefix.
    - After: the hash is stored, and login hashes the typed password and compares. Hashes differ each startup because of the random salt.

- ```.roles("ADMIN")```: stores the authority ```ROLE_ADMIN```.

- ```.build()```: creates the immutable (cannot be changed after creation) ```UserDetails```.

- ```.roles("User")``` on the second user: becomes ```ROLE_User```, **not** ```ROLE_USER```. Case matters, so a future ```hasRole("USER")``` rule would return 403 for this account. Course rule 4 will need ```hasAnyRole("ADMIN", "User")```, or the role renamed to ```"USER"```.

- ```return new InMemoryUserDetailsManager(admin, user);```: keeps the users in a RAM map. They are lost on restart, fine for practice, not for production (use a database-backed ```UserDetailsService```).

- Warning from your own comment: never hardcode real passwords in source code.

### Code 2: ContactsApplication.java

```java
package com.ltp.contacts;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

import com.ltp.contacts.pojo.Contact;
import com.ltp.contacts.repository.ContactRepository;

import lombok.AllArgsConstructor;

@SpringBootApplication
//@AllArgsConstructor
public class ContactsApplication implements CommandLineRunner {

    private final ContactRepository contactRepository;

    public ContactsApplication(ContactRepository contactRepository) {
        this.contactRepository = contactRepository;
    }

    public static void main(String[] args) {
        SpringApplication.run(ContactsApplication.class, args);
    }

    @Bean
    public BCryptPasswordEncoder bCryptPasswordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Override
    public void run(String... args) throws Exception {
        contactRepository.getContacts().add(new Contact("1", "Jon Snow", "6123456432"));
        contactRepository.getContacts().add(new Contact("2", "Tyrion Lannister", "3125466472"));
        contactRepository.getContacts().add(new Contact("3", "The Hound", "5126476532"));
    }
}
```


- ```@Bean public BCryptPasswordEncoder bCryptPasswordEncoder()```: registers one encoder bean.
    - Before: no encoder bean, plaintext passwords failed.
    - After: ```SecurityConfig``` receives it by injection, and Spring's login check uses it to compare hashes.
    - Why here and not in ```SecurityConfig```? As far as I know, declaring it in the class that also injects it can cause a circular dependency (A needs B, but B is created by A), so it is commonly kept outside.

---

### startup vs request time

`filterChain()` runs **once at startup**. It is a blueprint (a detailed plan of how something will be built), not something that runs per request. When the first request arrives, your method has already finished, and requests pass through the **filters inside the object** that `http.build()` produced.

> **SecurityFilterChain:** an ordered list of servlet filters every HTTP request passes through before reaching your controller.

> **FilterChainProxy:** the single Spring entry point that picks the matching chain for each request and runs its filters.

### what is inside http.build()

It is a Java object (`DefaultSecurityFilterChain`), not JSON. A simplified view (the exact list depends on the Spring Security version):

```
requestMatcher: any request
filters (in this order):
  SecurityContextHolderFilter   loads the security context (from a session, if one exists)
  HeaderWriterFilter            adds security response headers
  CsrfFilter                    checks the CSRF token on POST/PUT/PATCH/DELETE   (removed by csrf().disable())
  LogoutFilter
  BasicAuthenticationFilter     reads the Authorization header                   (added by httpBasic())
  AnonymousAuthenticationFilter marks unauthenticated users as "anonymous"
  ExceptionTranslationFilter    turns security errors into 401 / 403
  FilterSecurityInterceptor     checks your URL rules (authorizeHttpRequests() uses AuthorizationFilter instead)
```

- `HttpSecurity` is only a **builder**. It holds your configuration, not headers or body. Headers and body live in `HttpServletRequest`.
- To see them: put a breakpoint inside `BasicAuthenticationFilter.doFilterInternal` and read `request.getHeader("Authorization")`. To see the chain: set `logging.level.org.springframework.security=TRACE`, or step over `http.build()` and inspect the `filters` field.


### BCrypt

> **Hashing (BCrypt):** a one-way transformation. You cannot reverse a hash back to the password. Login hashes the typed password and compares it with the stored hash. BCrypt **hashes**, it does not encrypt (encryption can be reversed with a key).

> **Salt:** random data added per password, so two users with the same password get different hashes.

> **Cost factor:** BCrypt is deliberately slow, which makes brute-force guessing expensive.

### the session and cookie trap

- Expected: every request re-checks the Basic Auth header.
- Actual (before `STATELESS`): the first successful login created a session, Postman stored the `JSESSIONID` cookie, and later requests restored the logged-in user from that session. The password check was skipped, so even a wrong password with the same username returned `200`.
- Manual workaround: clear the cookie in Postman. Real fix: `STATELESS`.
- After the fix: an old cookie may still exist in Postman, but Spring ignores it. Restart the app after changing this, because old sessions live in RAM.

> **Stateless:** the server keeps no session, so every request must prove who it is on its own.

### How it works: CSRF

> **CSRF (Cross-Site Request Forgery):** an attacker tricks an already-logged-in user's browser into sending an unwanted request to a trusted site. The browser attaches the credentials automatically.

- The attacker never learns the password, and the admin is a real but unaware (unwitting) person. Only the browser is tricked into firing the request.
- Defense: the server requires a secret CSRF token (header `X-CSRF-TOKEN`) that the attacker's page cannot read.
- `CsrfFilter` checks only **unsafe methods** (POST, PUT, PATCH, DELETE). GET is safe and skipped, which is why GET always worked and DELETE was blocked, even for the admin.
- It runs **before** `BasicAuthenticationFilter`, so your role rule was never even reached.
- `.csrf().disable()` lowers the shield. Spring's docs say it is reasonable for services used only by non-browser clients. Browsers cache Basic Auth credentials and resend them automatically, so if a browser ever calls this API the risk returns.

### How it works: rule order

- Rules are checked top to bottom and the **first match wins**.
- Specific rules first, `anyRequest()` last.
- Current state: DELETE needs ADMIN, everything else needs login. Still to do: POST for admins and users, GET open to everyone.

### Old vs new syntax (Spring Security 6)

Old (what the project uses now, Spring Security 5 style):

```java
http
    .csrf().disable()
    .authorizeRequests()
    .antMatchers(HttpMethod.DELETE, "/contact/**").hasRole("ADMIN")
    .anyRequest().authenticated()
    .and()
    .httpBasic()
    .and()
    .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
```

New (lambda style, needs `import org.springframework.security.config.Customizer;`):

```java
http
    .csrf(csrf -> csrf.disable())
    .authorizeHttpRequests(auth -> auth
        .requestMatchers(HttpMethod.DELETE, "/contact/**").hasRole("ADMIN")
        .anyRequest().authenticated())
    .httpBasic(Customizer.withDefaults())
    .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS));
```

- `authorizeRequests()` is replaced by `authorizeHttpRequests(...)`.
- `antMatchers` is removed in Spring Security 6 and replaced by `requestMatchers`.
- `.and()` chaining is deprecated, each section takes a lambda instead.
- Both snippets use the path `/contact/**` so they match the course rule, unlike the current project code.

### Big picture: flow diagram

```
STARTUP (once)
main() -> Spring context loads
   -> @Bean BCryptPasswordEncoder                       (ContactsApplication)
   -> SecurityConfig created, encoder injected
   -> users(): admin + user, passwords hashed -> InMemoryUserDetailsManager
   -> filterChain(http): csrf off, DELETE->ADMIN, any->authenticated, basic, stateless
        -> http.build() -> DefaultSecurityFilterChain registered
   -> run(): 3 contacts added

REQUEST (every time): DELETE /contact/2 with admin credentials
Client
  |
  v
FilterChainProxy -> matching chain -> filters in order:
  CsrfFilter (skipped, disabled)
  BasicAuthenticationFilter -> reads Authorization header
      -> ProviderManager -> DaoAuthenticationProvider
      -> users() lookup + BCrypt matches()
      -> wrong/missing credentials -> 401
      -> ok -> Authentication stored in SecurityContext (no session: STATELESS)
  ExceptionTranslationFilter
  URL rule check (DELETE needs ROLE_ADMIN)
      -> user role -> 403
      -> admin -> continue
  |
  v
DispatcherServlet -> Controller -> response
```

---

Official docs:

- Architecture: https://docs.spring.io/spring-security/reference/servlet/architecture.html
- Authentication architecture: https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html
- HTTP Basic: https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/basic.html
- In-memory users: https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/in-memory.html
- Password storage / BCrypt: https://docs.spring.io/spring-security/reference/features/authentication/password-storage.html
- Session management: https://docs.spring.io/spring-security/reference/servlet/authentication/session-management.html
- Authorize HTTP requests: https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html
- CSRF: https://docs.spring.io/spring-security/reference/servlet/exploits/csrf.html