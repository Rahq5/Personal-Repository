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

```python
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9****.****eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNzA4MzQ1MTIzLCJleHAiOjE3MDgzNTUxMjN9****.****SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
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

complete from the same header name 