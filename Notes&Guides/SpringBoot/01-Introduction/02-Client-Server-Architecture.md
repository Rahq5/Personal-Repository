## Client/Server Architecture (Quick & Deep)

### Basic Concept

A **server** is a program that waits and listens for requests. A **client** is a program that sends requests to the server.

### How Communication Works

**IP Address:** Identifies a computer on the network.

- `127.0.0.1` or `localhost` = your own machine
- `192.168.1.x` = local network addresses
- Public IPs = accessible from internet

**Port:** A number (0-65535) that identifies a specific application on that computer. Think of IP as a building address, port as an apartment number.

Common ports:

- `8080` = default Spring Boot
- `80` = HTTP
- `443` = HTTPS
- `3306` = MySQL

### The Request/Response Cycle

1. **Client initiates:** Browser (client) wants a webpage
2. **DNS Resolution:** Converts domain name to IP (if needed)
3. **TCP Connection:** Client connects to server's IP:Port
4. **HTTP Request:** Client sends request (GET, POST, etc.)
    
    ```
    GET /api/users HTTP/1.1Host: localhost:8080
    ```
    
5. **Server processes:** Spring Boot controller handles the request
6. **HTTP Response:** Server sends back data
    
    ```
    HTTP/1.1 200 OKContent-Type: application/json{"id": 1, "name": "John"}
    ```
    
7. **Connection closes** (or stays open for keep-alive)

### Spring Boot as Server

When you run Spring Boot:

- Embedded Tomcat starts
- Listens on `localhost:8080` by default
- Waits for HTTP requests
- Your `@RestController` or `@Controller` classes handle incoming requests

**Example flow:**

```
Browser (Client)           Spring Boot (Server)
     |                            |
     |---GET localhost:8080/----->|
     |                            | @GetMapping("/")
     |                            | method executes
     |<------HTML/JSON------------|
     |                            |
```

### Socket Level

Under HTTP, there's a **TCP socket** - a two-way communication channel.

**Server side:**

- ServerSocket binds to port 8080
- Listens for incoming connections
- Accepts connection = creates Socket for that client

**Client side:**

- Creates Socket
- Connects to server's IP:Port
- Sends/receives bytes through the socket

Spring Boot hides this complexity. Tomcat manages sockets, and you work with `HttpServletRequest` and `HttpServletResponse`.

### Multiple Clients

Server can handle multiple clients because:

1. Each client connection gets its own socket
2. Server uses threads (one thread per request by default in Tomcat)
3. Thread handles request → sends response → thread ends or returns to pool

---

