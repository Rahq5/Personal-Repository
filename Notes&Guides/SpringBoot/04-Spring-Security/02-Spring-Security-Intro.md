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
	   Example: Lets say you requested to login but you didnt log your credentials, this will cause the server to reponse with 401 (UNAUTHORIZED).
	   And if happened and logged successfully is it will send a 201

4. Authorization: spring security determines what the client has access to 
	   after logging in successfully, the server should who you are and edit your access.
		   Example: if your role was a "USER" and attempted to send Post request for admin role things, you will be responded with a 403 (FORBIDDEN).