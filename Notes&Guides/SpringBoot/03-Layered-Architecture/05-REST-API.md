## REST API

### Introduction

our goal to learn RestAPI
**API:** is a mediator between consumer(user) and system

REST Guidlines:
- **Resource**: piece of data you can name
- **URI**: identifies the location of source like `localhost:8080/employess` 
- **Operations**: essential operation of REST which is (GET,PUT,DELETE,POST)
- **JSON**: mostly used presentation in REST 
- **Collection** : collection of multiple JSON pieces into one bundle , uses square brackets like here `[obj1 , obj2 , etc...]`

### @RequestParam vs @PathVariable
Both grab data from the URL, but from **different parts** of it.
**`@RequestParam`** reads after the `?` `localhost:8080/hi?name=Harry`

java
```java
@GetMapping("/hi")
public void handlerMethod(@RequestParam String name)
```
---

**`@PathVariable`** reads from the path itself `localhost:8080/hi/Harry`
java
```java
@GetMapping("/hi/{name}")
public void handlerMethod(@PathVariable String name)
```
**When to use which:**
- Pointing to a specific resource → `@PathVariable` → `/users/5`
- Filtering or searching → `@RequestParam` → `/users?role=admin`
> `@PathVariable` is preferred in REST because the URL should point directly to a resource.

### REST API: GET operations
simply gets data 

WorkFlow:
1. Client sends HTTP GET request to a URL like `/users`
2. Spring receives it and routes it to the matching `@GetMapping` method inside a `@RestController` class
3. Spring automatically converts the Java object to **JSON**
4. JSON is sent back to the client as the HTTP response

```java
@RestController
public class ContactController {

    @Autowired
    private ContactService contactService;

    @GetMapping("/contact/{id}")
    @ResponseBody
    public ResponseEntity<Contact> getContact(@PathVariable String id){
        Contact contact = contactService.getContact(id);
        return new ResponseEntity<>(contact , HttpStatus.Ok); // 200 OK + contact as JSON
    }
}
```
**Definitions and explaining**:
- **`@RestController`**: contains `@Controller` + `@ResponseBody` , marks the class as a REST handler
- **`@ResponseBody`**: serializes (converts) the return value into JSON automatically
- **`ResponseEntity<T>`**: represents the full HTTP response = **data** + **HTTP status code** (like 200, 404)
- **`@PathVariable`**: extracts the value from the URL , like `/contact/5` → `id = "5"`
- **`ResponseEntity.ok(contact)`**: wraps the contact object with HTTP status **200 OK** and returns it as the response


### REST API: POST Operations
simply sends data to be saved

WorkFlow:
1. Client sends HTTP POST request to a URL like `/contact` with a **JSON body** (the data)
2. Spring receives it and routes it to the matching `@PostMapping` method inside a `@RestController` class
3. Spring automatically converts the **JSON body** into a Java object
4. The method processes it and sends back an HTTP status code as the response

```java
@RestController
public class ContactController {

    @Autowired
    private ContactService contactService;

    @PostMapping("/contact")
    public ResponseEntity<HttpStatus> createContact(@RequestBody Contact contact){

        if(contact != null){
            System.out.println(contact.getName());
            contactService.saveContact(contact);
            return new ResponseEntity<>(HttpStatus.CREATED); // 201 CREATED
        } else {
            return new ResponseEntity<>(HttpStatus.NO_CONTENT); // 204 NO CONTENT
        }
    }
}
```

**Definitions and explaining**:
- **`@PostMapping("/contact")`**: listens for POST requests at `/contact` and routes them to this method
- **`@RequestBody`**: deserializes (converts) the incoming JSON from the request body into a Java object (`Contact`). **Without it**, Spring won't read the request body at all — the `contact` object will be `null` or empty, and your data is lost
- **`ResponseEntity<HttpStatus>`**: represents the full HTTP response — here it only carries a **status code**, no body data
- **`HttpStatus.CREATED`**: status code **201**, means the resource was successfully created
- **`HttpStatus.NO_CONTENT`**: status code **204**, means the request was received but there was nothing to process

### REST API: PUT Operations
Simply **updates** an existing contact by its ID

**WorkFlow:**
1. Client sends HTTP PUT request to a URL like `/contact/456` (the ID is in the URL)
2. Spring receives it and routes it to the matching `@PutMapping` method
3. Spring extracts `"456"` from the URL and injects it into `id` parameter
4. Spring converts the JSON body into a `Contact` Java object
5. The method passes both to the service to update, then fetches and returns the updated contact

```java
@PutMapping("/contact/{id}")
public ResponseEntity<Contact> updateContact(@PathVariable String id, @RequestBody Contact contact) {

    Contact updated = contactService.updateContact(id, contact);
    return new ResponseEntity<>(updated, HttpStatus.OK);
}
```

**Definitions and explaining:**

- `@PutMapping("/contact/{id}")`: listens for PUT requests at `/contact/{id}` — the `{id}` is a **URL placeholder** that captures whatever value the client puts there (e.g. `/contact/456` → id = `"456"`)
- `@PathVariable String id`: extracts the `{id}` value from the URL and binds it to this parameter. Without it, Spring won't know to pull the value out of the URL
- `@RequestBody Contact contact`: converts the incoming JSON body into a `Contact` Java object — same as POST
- `ResponseEntity<Contact>`: the response carries both a status code **and** a body (the updated contact object), Spring serializes it back to JSON automatically
- `HttpStatus.OK`: status code 200, means the request succeeded and the updated resource is returned in the body

### REST API: DELETE Operations
Simply **deletes** an existing contact by its ID

**WorkFlow:**
1. Client sends HTTP DELETE request to a URL like `/contact/456` (the ID is in the URL)
2. Spring receives it and routes it to the matching `@DeleteMapping` method
3. Spring extracts `"456"` from the URL and injects it into `id` parameter
4. The method passes the ID to the service to delete, then returns no body — just a status code
```java
@DeleteMapping("/contact/{id}")
public ResponseEntity<HttpStatus> deleteContact(@PathVariable String id) {
    contactService.deleteContact(id);
    return new ResponseEntity<HttpStatus>(HttpStatus.NO_CONTENT);
}
```

**Definitions and explaining:**

- `@DeleteMapping("/contact/{id}")`: listens for DELETE requests at `/contact/{id}` — the `{id}` is a **URL placeholder** that captures whatever value the client puts there (e.g. `/contact/456` → id = `"456"`)
- `@PathVariable String id`: extracts the `{id}` value from the URL and binds it to this parameter. Without it, Spring won't know to pull the value out of the URL
- `ResponseEntity<HttpStatus>`: the response carries **only** a status code with no body — there is nothing to return since the resource was deleted
- `HttpStatus.NO_CONTENT`: status code 204, means the request succeeded but there is intentionally no body in the response

