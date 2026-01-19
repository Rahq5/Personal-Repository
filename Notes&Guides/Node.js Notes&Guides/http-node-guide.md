

## Core Concepts (Theory)

### Buffers

**What are Buffers?**
Buffers are objects that represent raw binary data (bytes). They store data in its most basic form - as sequences of numbers (0-255).

**Example:**
- Text "Hello" in Buffer: `<Buffer 48 65 6c 6c 6f>`
- Each number represents one character in binary form

**Why Buffers exist:**
Network communication works with binary data, not text. When data travels over the internet, it's sent as raw bytes. Buffers let Node.js work with this binary data efficiently.

**Converting between Buffer and String:**
```javascript
const buffer = Buffer.from('Hello');  // String to Buffer
const text = buffer.toString();       // Buffer to String → 'Hello'
```

### Chunks

**What are chunks?**
Chunks are small pieces of data that arrive separately over the network. Instead of receiving all data at once (like receiving a whole book), you receive it page by page.

**Why chunks exist:**
Data traveling over the network is split into small packets because:
- Network bandwidth is limited
- Large files need to be broken down
- Makes communication more efficient

**What chunks look like:**
Each chunk is a Buffer object containing a portion of the total data.

```
Total data: "Hello World, this is a long message"

Chunk 1: <Buffer 48 65 6c 6c 6f>           → "Hello"
Chunk 2: <Buffer 20 57 6f 72 6c 64>        → " World"
Chunk 3: <Buffer 2c 20 74 68 69 73>        → ", this"
```

**Processing chunks:**
1. Collect all chunks in an array
2. Combine them using `Buffer.concat()`
3. Convert to string if needed

### Streams and Events

**What are Streams?**
Streams are objects that let you read or write data piece by piece (in chunks), rather than all at once. Both request and response objects in Node.js HTTP are streams.

Think of a stream like water flowing through a pipe - it flows continuously in small amounts, not all at once.

**Official Documentation - Streams:**
https://nodejs.org/api/stream.html

**Stream Events:**

Events are signals that fire when something happens. Streams emit several events:

**'data' event:**
- Fires each time a new chunk arrives
- Called multiple times (once per chunk)
- Provides one chunk (Buffer) as parameter

**'end' event:**
- Fires once when all data has been received
- Called only one time
- No parameters
- Safe to process the complete data now

**'error' event:**
- Fires if something goes wrong
- Provides error object as parameter
- Examples: network failure, invalid data, connection refused

**Timeline example:**
```
Start receiving data
    ↓
'data' event → chunk 1
'data' event → chunk 2
'data' event → chunk 3
    ↓
'end' event → complete
```

### HTTP Headers

**What are headers?**
Headers are metadata (information about data) sent along with HTTP requests and responses. They don't contain the actual content, but describe it.

Think of headers like the information written on an envelope - it tells you about what's inside without opening it.

**Two types of headers:**

**1. Request Headers** (Client → Server)
Information the client sends to tell the server what it wants or what it's sending.

**2. Response Headers** (Server → Client)
Information the server sends to tell the client about the response.

**Common standard headers:**
- `Content-Type`: Format of data (we'll discuss MIME types below)
- `Content-Length`: Size of data in bytes
- `Authorization`: Authentication token
- `Cache-Control`: How long to cache the response
- `Set-Cookie`: Cookie data to save in browser

**Custom headers:**
Headers made by developers for specific needs:
- Often start with `X-` prefix (though this is deprecated)
- Not part of HTTP standard
- Examples: `X-Powered-By`, `X-Request-ID`

**Official Documentation - HTTP Headers:**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers

**Official Registry:**
https://www.iana.org/assignments/http-fields/http-fields.xhtml

### MIME Types (Content-Type Header)

**What are MIME types?**
MIME (Multipurpose Internet Mail Extensions) types tell the receiver what format the data is in. They're used in the `Content-Type` header.

**Format:** `type/subtype`

**Common MIME types:**
- `application/json` - JSON data
- `text/plain` - Plain text
- `text/html` - HTML document
- `image/jpeg` - JPEG image
- `image/png` - PNG image
- `application/pdf` - PDF file
- `application/octet-stream` - Generic binary file (unknown type)

**Example usage:**
```javascript
// Telling receiver "I'm sending JSON"
res.setHeader('Content-Type', 'application/json');

// Telling receiver "I'm sending an image"
res.setHeader('Content-Type', 'image/jpeg');
```

**Why it matters:**
The receiver needs to know what format the data is in to process it correctly. Without the correct MIME type, browsers and clients might misinterpret the data.

**We'll see MIME types used throughout the code examples in the Server and Client sections.**

**Official Documentation - MIME Types:**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types

### HTTP Response Structure

Every HTTP response has two parts that arrive separately:

**Part 1: Headers** (arrives first)
```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 27
Date: Sat, 18 Oct 2025
                        ← Empty line separates headers from body
```

**Part 2: Body** (arrives after headers)
```
{"message": "Hello World"}
```

This separation is important because it affects when callbacks and events fire in your code.

### Data Flow Between Client and Server

Understanding how data flows helps you know when to use chunks, buffers, and events.

**Client Sending to Server:**
```
CLIENT SIDE:
1. JavaScript object: { name: 'Ahmed' }
2. JSON.stringify() → '{"name":"Ahmed"}'
3. req.write(string) → Node.js converts to Buffer
4. req.end() → sends request
   ↓
   ═══ Network (data split into chunks) ═══
   ↓
SERVER SIDE:
5. 'data' event fires → receives Buffer chunks
6. Store chunks in array
7. 'end' event fires → all received
8. Buffer.concat(chunks) → one Buffer
9. .toString() → '{"name":"Ahmed"}'
10. JSON.parse() → { name: 'Ahmed' }
```

**Server Responding to Client:**
```
SERVER SIDE:
1. JavaScript object: { status: 'ok' }
2. JSON.stringify() → '{"status":"ok"}'
3. res.setHeader() → set headers
4. res.end(string) → Node.js converts to Buffer, sends headers then body
   ↓
   ═══ Network (data split into chunks) ═══
   ↓
CLIENT SIDE:
5. Callback fires → headers received
6. Access statusCode, headers
7. 'data' event fires → receives Buffer chunks
8. Store chunks in array
9. 'end' event fires → all received
10. Buffer.concat(chunks) → one Buffer
11. .toString() → '{"status":"ok"}'
12. JSON.parse() → { status: 'ok' }
```

**Key principle:** Data always travels as Buffers (binary), gets split into chunks, and must be collected and combined before processing.

---

## Server Concepts (Deep Dive)

### How createServer Works

`http.createServer()` creates a server object that listens for incoming HTTP requests.

**The requestListener callback:**
- Takes two parameters: `(request, response)`
- Executes **every single time** a request arrives
- Each request gets its own separate execution
- The callback doesn't know about previous or future requests

**Official Documentation:**
https://nodejs.org/api/http.html#httpcreateserveroptions-requestlistener

### The Request Object (Server Side)

The request object (`req`) is a **Readable Stream** containing all information about the client's request.

**Key properties:**
- `req.method` - HTTP method (GET, POST, PUT, DELETE)
- `req.url` - Path requested (/login, /api/users)
- `req.headers` - Object with all request headers (keys are lowercase)
- `req.httpVersion` - HTTP version (1.1, 2.0)

**Official Documentation:**
https://nodejs.org/api/http.html#class-httpincomingmessage

### The Response Object (Server Side)

The response object (`res`) is a **Writable Stream** used to send data back to the client.

**Key methods:**
- `res.setHeader(name, value)` - Sets a response header (before sending body)
- `res.statusCode` - Property to set HTTP status code
- `res.write(data)` - Sends a chunk of body (can be called multiple times)
- `res.end([data])` - Finishes response, closes connection (MUST be called)

**Important rule:** You must call `res.end()` for every request, or the client hangs forever.

### Receiving Request Body

When clients send data (POST/PUT), it arrives as chunks through events.

**The pattern:**
1. Listen to 'data' event - collect Buffer chunks in array
2. Listen to 'end' event - combine chunks, convert to string, parse if JSON
3. Listen to 'error' event - handle failures

### Routing

Routing means directing requests to different handlers based on URL and/or method.

Use conditional statements (`if`, `switch`) to check `req.url` and `req.method`.

---

## Server Code Examples

### Echo Server (Complete Example with Data Flow)

This server receives data and sends it back unchanged - perfect for understanding the flow.

```javascript
import http from 'http';

const server = http.createServer((req, res) => {
  
  if (req.url === '/echo' && req.method === 'POST') {
    
    let body = [];  // Array to collect chunks
    
    // Event: each time a chunk arrives
    req.on('data', (chunk) => {
      body.push(chunk);  // Store the chunk
      console.log('Chunk received:', chunk.length, 'bytes');
    });
    
    // Event: all data received
    req.on('end', () => {
      // Combine all chunks into one Buffer
      body = Buffer.concat(body);
      
      console.log('Total received:', body.length, 'bytes');
      
      // Echo: send back exactly what was received
      res.setHeader('Content-Type', 'text/plain');
      res.end(body);  // Send Buffer directly
    });
    
    // Event: error occurred
    req.on('error', (err) => {
      console.error('Error:', err);
      res.statusCode = 500;
      res.end('Error receiving data');
    });
  }
});

server.listen(8000, () => {
  console.log('Server running on port 8000');
});
```

**Data Flow Diagram for Echo Server:**
```
CLIENT                          SERVER
  |                               |
  | POST /echo                   |
  | Body: "Hello World"          |
  |----------------------------->|
  |                              | 'data' event → chunk: <Buffer 48 65...>
  |                              | Store in array
  |                              | 'data' event → chunk: <Buffer 6c 6c...>
  |                              | Store in array
  |                              | 'end' event
  |                              | Combine chunks → full Buffer
  |                              | Prepare response
  |                              |
  |     Headers (200 OK)         |
  |<-----------------------------|
  |     Body: "Hello World"      |
  |<-----------------------------|
  |                              |
```

### Basic Server with Routing

```javascript
import http from 'http';

const server = http.createServer((req, res) => {
  
  // Route based on URL
  switch (req.url) {
    case '/':
      res.end('Home Page');
      break;
      
    case '/api/data':
      // Route based on method
      if (req.method === 'GET') {
        res.setHeader('Content-Type', 'application/json');
        res.end(JSON.stringify({ data: [1, 2, 3] }));
      } else {
        res.statusCode = 405;  // Method not allowed
        res.end('Only GET allowed');
      }
      break;
      
    default:
      res.statusCode = 404;
      res.end('Not Found');
  }
});

server.listen(8000);
```

### Receiving JSON Data

```javascript
// Only the important POST handling part
if (req.url === '/api/users' && req.method === 'POST') {
  
  let body = [];
  
  req.on('data', chunk => body.push(chunk));
  
  req.on('end', () => {
    try {
      // Combine chunks and convert to string
      body = Buffer.concat(body).toString();
      
      // Parse JSON string to object
      const userData = JSON.parse(body);
      
      console.log('User:', userData.name, userData.age);
      
      // Send JSON response
      res.setHeader('Content-Type', 'application/json');
      res.end(JSON.stringify({
        success: true,
        message: 'User created'
      }));
      
    } catch (error) {
      // Handle invalid JSON
      res.statusCode = 400;
      res.end('Invalid JSON');
    }
  });
}
```

### Complete Server Example

```javascript
import http from 'http';

const server = http.createServer((req, res) => {
  
  console.log(`${req.method} ${req.url}`);
  
  switch (req.url) {
    case '/':
      res.setHeader('Content-Type', 'text/plain');
      res.end('Welcome!');
      break;
      
    case '/api/data':
      if (req.method === 'GET') {
        // GET: Return data
        res.setHeader('Content-Type', 'application/json');
        res.end(JSON.stringify({ items: [1, 2, 3] }));
        
      } else if (req.method === 'POST') {
        // POST: Receive data
        let body = [];
        
        req.on('data', chunk => body.push(chunk));
        
        req.on('end', () => {
          try {
            // Parse received JSON
            body = Buffer.concat(body).toString();
            const data = JSON.parse(body);
            
            console.log('Received:', data);
            
            // Respond with success
            res.setHeader('Content-Type', 'application/json');
            res.end(JSON.stringify({ 
              success: true,
              received: data 
            }));
          } catch (err) {
            res.statusCode = 400;
            res.end('Invalid JSON');
          }
        });
        
        req.on('error', (err) => {
          console.error('Error:', err);
          res.statusCode = 500;
          res.end('Server Error');
        });
        
      } else {
        res.statusCode = 405;
        res.end('Method Not Allowed');
      }
      break;
      
    default:
      res.statusCode = 404;
      res.end('404 Not Found');
  }
});

server.listen(8000, () => {
  console.log('Server running on port 8000');
});
```

---

## Client Concepts (Deep Dive)

### How http.request() Works

`http.request()` creates an HTTP request to a server and returns a request object.

**Parameters:**
- `options` (Object) - Configuration: hostname, port, path, method, headers
- `callback` (Function) - Called when response headers arrive (NOT when body complete)

**Official Documentation:**
https://nodejs.org/api/http.html#httprequestoptions-callback

**Official Guide:**
https://nodejs.org/en/learn/modules/making-http-requests-with-nodejs

### When the Callback is Called

The callback `(response) => { }` runs when the server sends back the **response headers**, NOT when the response body is complete.

**Timeline:**
```
1. req.end() → Request sent
2. Server processes
3. Server sends headers
   ↓
   ⚡ CALLBACK FIRES (can access statusCode, headers)
   ↓
4. Server sends body chunks
   ↓
   'data' events fire
   ↓
5. Body complete
   ↓
   'end' event fires
```

### The Response Object (Client Side)

When making a request, the response object is a **Readable Stream** containing the server's response.

**Key properties:**
- `response.statusCode` - HTTP status (200, 404, 500)
- `response.headers` - Object with all response headers

### The Request Object (Client Side)

The request object returned by `http.request()` is a **Writable Stream** used to send data.

**Key methods:**
- `req.write(data)` - Sends data (can call multiple times)
- `req.end([data])` - Finishes request, **actually sends it** (MUST call)

**Critical rule:** You MUST call `req.end()` or nothing gets sent.

### Request Options

The `options` object configures the request:

**Required:**
- `hostname` - Server address (domain or IP, no protocol)
- `port` - Port number
- `path` - URL path (starts with `/`)
- `method` - HTTP method (uppercase: 'GET', 'POST', etc.)

**Optional:**
- `headers` - Object with request headers
- `timeout` - Milliseconds before timeout

---

## Client Code Examples

### Basic GET Request

```javascript
import http from 'http';

const options = {
  hostname: 'localhost',
  port: 8000,
  path: '/api/data',
  method: 'GET'
};

const req = http.request(options, (res) => {
  console.log('Status:', res.statusCode);
  
  let data = [];
  
  // Collect response chunks
  res.on('data', chunk => data.push(chunk));
  
  // Process complete response
  res.on('end', () => {
    data = Buffer.concat(data).toString();
    console.log('Response:', data);
  });
});

// Handle errors
req.on('error', err => console.error('Error:', err.message));

// Send request (no body for GET)
req.end();
```

### POST Request with JSON

```javascript
import http from 'http';

// Prepare JSON data to send
const postData = JSON.stringify({
  name: 'Ahmed',
  age: 25
});

const options = {
  hostname: 'localhost',
  port: 8000,
  path: '/api/users',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': Buffer.byteLength(postData)
  }
};

const req = http.request(options, (res) => {
  console.log('Status:', res.statusCode);
  
  let responseData = [];
  
  res.on('data', chunk => responseData.push(chunk));
  
  res.on('end', () => {
    responseData = Buffer.concat(responseData).toString();
    console.log('Server response:', responseData);
  });
});

req.on('error', err => console.error('Error:', err.message));

// Send data and close request
req.write(postData);
req.end();
```

### Complete Client Example

```javascript
import http from 'http';

// Data to send
const data = JSON.stringify({
  username: 'ahmed',
  email: 'ahmed@example.com'
});

// Configure request
const options = {
  hostname: 'localhost',
  port: 8000,
  path: '/api/login',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': Buffer.byteLength(data)
  }
};

// Create and send request
const req = http.request(options, (res) => {
  
  // Callback fires when headers arrive
  console.log('Status:', res.statusCode);
  console.log('Content-Type:', res.headers['content-type']);
  
  let responseData = [];
  
  // Collect response body chunks
  res.on('data', (chunk) => {
    responseData.push(chunk);
  });
  
  // Process complete response
  res.on('end', () => {
    responseData = Buffer.concat(responseData).toString();
    
    try {
      // Try parsing as JSON
      const jsonResponse = JSON.parse(responseData);
      console.log('Parsed response:', jsonResponse);
    } catch (e) {
      console.log('Response (not JSON):', responseData);
    }
  });
});

// Handle errors
req.on('error', (err) => {
  console.error('Request failed:', err.message);
});

// Set timeout (optional)
req.setTimeout(5000, () => {
  console.log('Request timeout');
  req.destroy();
});

// Write data and send
req.write(data);
req.end();
```

---

## Best Practices

### Always Handle Errors

```javascript
// Server
req.on('error', (err) => {
  res.statusCode = 500;
  res.end('Error');
});

// Client  
req.on('error', (err) => {
  console.error('Failed:', err);
});
```

### Always Call end()

```javascript
res.end();  // Server - close response
req.end();  // Client - send request
```

### Use Buffer.concat() Not String Concatenation

```javascript
// ❌ Wrong
let body = '';
req.on('data', chunk => body += chunk.toString());

// ✅ Correct
let body = [];
req.on('data', chunk => body.push(chunk));
req.on('end', () => body = Buffer.concat(body).toString());
```

### Validate JSON Parsing

```javascript
try {
  const data = JSON.parse(body);
  // Process data
} catch (error) {
  res.statusCode = 400;
  res.end('Invalid JSON');
}
```

### Set Appropriate Status Codes

```javascript
res.statusCode = 200;  // OK
res.statusCode = 201;  // Created
res.statusCode = 400;  // Bad Request
res.statusCode = 404;  // Not Found
res.statusCode = 500;  // Server Error
```

---

## Additional Resources

### Official Node.js Documentation

**HTTP Module:**
https://nodejs.org/api/http.html

**Making HTTP Requests Guide:**
https://nodejs.org/en/learn/modules/making-http-requests-with-nodejs

**Anatomy of HTTP Transaction:**
https://nodejs.org/en/learn/modules/anatomy-of-an-http-transaction

**Streams:**
https://nodejs.org/api/stream.html

### Additional References

**HTTP Headers (MDN):**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers

**HTTP Status Codes (MDN):**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Status

**MIME Types (MDN):**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types

**IANA Registry:**
https://www.iana.org/assignments/http-fields/http-fields.xhtml