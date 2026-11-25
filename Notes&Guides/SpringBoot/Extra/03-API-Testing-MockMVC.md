## API Testing in Spring Boot — MockMVC & Integration Tests

> These notes cover how to write integration tests for a REST API in Spring Boot using MockMVC, JSONPath, and ObjectMapper. This matters because shipping untested endpoints is shipping broken trust — tests are your contract proof.

**What we'll cover:**

- Test lifecycle: `@BeforeEach` / `@AfterEach`
- Mocking HTTP requests with MockMVC
- Asserting HTTP status, content type, and JSON body
- JSONPath queries for single objects and arrays
- Sending a request body using ObjectMapper (POJO → JSON)
- Validating error cases (404, 400)

---

#### Test Lifecycle Setup

Before any test runs, you need data in the repository. After each test, you clean it out — so tests don't bleed into each other.

```java
@BeforeEach
void setup() {
    for (int i = 0; i < contacts.length; i++) {
        contactRepository.saveContact(contacts[i]);
    }
}

@AfterEach
void clear() {
    contactRepository.getContacts().clear();
}
```

> **`@BeforeEach`** — runs once before every single test method in the class.  
> **`@AfterEach`** — runs once after every single test method, used here to reset state.

---

#### Test 1 — GET a single contact by ID

Mock a `GET /contact/1` and assert the response.

```java
@Test
void getContactByIdTest() throws Exception {
    mockMvc.perform(get("/contact/1"))
        .andExpect(status().isOk())                          // 200
        .andExpect(content().contentType(MediaType.APPLICATION_JSON))
        .andExpect(jsonPath("$.name").value("Rayan"))
        .andExpect(jsonPath("$.phoneNumber").value("5334221234"));
}
```

> **JSONPath** — a query language for JSON, similar to XPath for XML.  
> **`$`** — refers to the root element of the JSON document.  
> **`$.name`** — grabs the `name` field at the root level.

So if the response body is:

```json
{
  "id": "1",
  "name": "Rayan",
  "phoneNumber": "5334221234"
}
```

Then `jsonPath("$.name").value("Rayan")` passes. ✅

---

#### Test 2 — GET all contacts

Mock a `GET /contact/all` and assert the array response.

```java
@Test
void getAllContactsTest() throws Exception {
    mockMvc.perform(get("/contact/all"))
        .andExpect(status().isOk())
        .andExpect(content().contentType(MediaType.APPLICATION_JSON))
        .andExpect(jsonPath("$.size()").value(3))
        .andExpect(jsonPath("$.[?(@.id == \"2\" && @.name == \"Tyrion Lannister\" && @.phoneNumber == \"4145433332\")]").exists());
}
```

> **`$.size()`** — returns the number of elements in a root-level JSON array.  
> **`$.[?(@.field == "value")]`** — filters array elements by condition. `@` refers to the current item being evaluated.  
> **`.exists()`** — asserts that at least one matching element was found.

The full expected JSON array:

```json
[
  { "id": "1", "name": "Jon Snow",          "phoneNumber": "6135342524" },
  { "id": "2", "name": "Tyrion Lannister",  "phoneNumber": "4145433332" },
  { "id": "3", "name": "The Hound",         "phoneNumber": "3452125631" }
]
```

---

#### Test 3 — GET a contact that doesn't exist (404)

Mock a `GET /contact/4` — ID 4 doesn't exist, so we expect a 404.

```java
@Test
void contactNotFoundTest() throws Exception {
    mockMvc.perform(get("/contact/4"))
        .andExpect(status().isNotFound()); // 404
}
```

> **404 Not Found** — the HTTP status code meaning the requested resource doesn't exist on the server.

This test verifies that your global exception handler is working. If you throw `PlaceNotFoundException` (or similar) and your `@ControllerAdvice` maps it to 404, this test proves it.

---

#### Test 4 — POST a valid contact (201 Created)

To send a request body, you need to serialize a Java object into JSON string. That's what `ObjectMapper` does.

```java
@Autowired
ObjectMapper objectMapper;

@Test
void validContactCreation() throws Exception {
    Contact newContact = new Contact(null, "Rayan", "5334221234");

    mockMvc.perform(post("/contact")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(newContact)))
        .andExpect(status().isCreated()); // 201
}
```

> **`ObjectMapper`** — a Spring/Jackson class that converts Java objects (POJOs) to JSON strings and vice versa.  
> **`writeValueAsString(obj)`** — serializes the Java object into a JSON string to be sent as the request body.  
> **`contentType(MediaType.APPLICATION_JSON)`** — tells the server "I'm sending JSON".  
> **201 Created** — HTTP status code meaning the resource was successfully created.

The JSON being sent:

```json
{
  "id": null,
  "name": "Rayan",
  "phoneNumber": "5334221234"
}
```

---

#### Test 5 — POST an invalid contact (400 Bad Request)

Send a contact with blank/whitespace-only fields. The server should reject it.

```java
@Test
void invalidContactCreation() throws Exception {
    Contact invalidContact = new Contact(null, "   ", "     ");

    mockMvc.perform(post("/contact")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(invalidContact)))
        .andExpect(status().isBadRequest()); // 400
}
```

> **400 Bad Request** — HTTP status code meaning the client sent something the server can't process because the data is malformed or invalid.

This test verifies that your `@Valid` / `@NotBlank` Bean Validation is wired and active on the controller. If validation fails → Spring returns 400 automatically.

---

#### HTTP Status Codes Quick Reference

|Code|Name|Meaning|
|---|---|---|
|200|OK|Request succeeded|
|201|Created|Resource was created successfully|
|400|Bad Request|Invalid input from client|
|404|Not Found|Resource doesn't exist|

---

#### Big Picture Flow

```
Test class
    │
    ├── @BeforeEach ──────────► populate repository with test data
    │
    ├── Test 1: GET /contact/1 ──► 200 + assert name & phone via JSONPath
    ├── Test 2: GET /contact/all ► 200 + assert size + assert specific doc exists
    ├── Test 3: GET /contact/4 ──► 404 (not found path)
    ├── Test 4: POST /contact ───► 201 (valid body via ObjectMapper)
    └── Test 5: POST /contact ───► 400 (blank fields, validation rejected)
    │
    └── @AfterEach ───────────► clear repository, reset state
```

---

**Official References:**

- MockMVC docs: https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/MockMvc.html
- JSONPath syntax: https://github.com/json-path/JsonPath
- Jackson ObjectMapper: https://fasterxml.github.io/jackson-databind/javadoc/2.7/com/fasterxml/jackson/databind/ObjectMapper.html
- Spring `@Valid` + Bean Validation: https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/requestbody.html
