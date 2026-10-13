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