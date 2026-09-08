### Field Validation and Error Handling 


The goal here is to validate incoming request body fields _before_ they even reach the service layer. Spring gives us a clean way to do this using annotations + a global exception handler that catches validation failures and returns structured error responses.

---

#### Step 1 — Add the Validation Dependency

In `pom.xml`, add:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

This pulls in **Jakarta Bean Validation** (formerly Javax) — the library that powers all the constraint annotations like `@NotBlank`, `@NotNull`, etc.

---

#### Step 2 — Apply Constraints on the Model

On the fields you want to protect, annotate them with validation constraints:

```java
@NotBlank(message = "Name cannot be blank")
private String name;

@NotBlank(message = "Number cannot be blank")
private String phoneNumber;
```

> `@NotBlank` — rejects `null`, empty strings `""`, and whitespace-only strings like `" "`. It's stricter than `@NotNull`.

---

#### Step 3 — Trigger Validation in the Controller

`@Valid` tells Spring: _"before you pass this object to my method, validate it against its constraints."_

```java
@PostMapping("/contact")
public ResponseEntity<Contact> addContact(@RequestBody @Valid ContactRequest request) {
    // only reaches here if all fields passed validation
}
```

Without `@Valid`, the annotations on the model do nothing at runtime.

---

#### Step 4 — Handle Validation Failures Globally

When `@Valid` finds a violation, Spring throws `MethodArgumentNotValidException`. We catch it in the global handler.

The handler must **extend `ResponseEntityExceptionHandler`** — a Spring base class that already knows about common Spring MVC exceptions. We override one of its methods to customize the response:

```java
@RestControllerAdvice
public class ApplicationExceptionHandler extends ResponseEntityExceptionHandler {

		// vvvvvv the method signature is fixed (from extended class i guess) vvvvvv
    @Override
    protected ResponseEntity<Object> handleMethodArgumentNotValid(
            MethodArgumentNotValidException ex,
            HttpHeaders headers,
            HttpStatusCode status,
            WebRequest request) {

		// vvvv this part is where you handle your error (written by you and your own mind) vvvv
		
        // BindingResult holds the outcome of the validation process
        // getAllErrors() returns a List of every constraint that was violated
        for (ObjectError error : ex.getBindingResult().getAllErrors()) {
            System.out.println(error.getDefaultMessage()); // prints the message= value
        }

        return new ResponseEntity<>(HttpStatus.BAD_REQUEST); // 400
    }
}
```

> **`BindingResult`** — the object Spring uses internally to collect all validation errors after running `@Valid`. Think of it as a report card of what failed.

At this stage: the terminal prints the error messages, and the client gets a `400` with no body.

---

#### Step 5 — Return Structured Error Responses

Instead of printing to terminal and returning an empty body, we want to collect all messages and send them back as JSON.

**Refactor `ErrorResponse.java` — change `message` from `String` to `List<String>`:**

```java
public class ErrorResponse {
    private LocalDateTime timestamp;
    private List<String> message; // was String, now List

    public ErrorResponse(List<String> message) {
        this.message = message;
        this.timestamp = LocalDateTime.now();
    }
}
```

**Fix `handleContactNotFoundException`** — it used to pass a plain string, now wrap it:

```java
ErrorResponse error = new ErrorResponse(Arrays.asList(ex.getLocalizedMessage()));
```

> `Arrays.asList(...)` — wraps a single item into a `List` so the constructor still works.

**Update `handleMethodArgumentNotValid`** — collect all messages into a list, then return:

```java
@Override
protected ResponseEntity<Object> handleMethodArgumentNotValid(...) {

    List<String> errors = new ArrayList<>();

    for (ObjectError error : ex.getBindingResult().getAllErrors()) {
        errors.add(error.getDefaultMessage());
    }

    ErrorResponse errorResponse = new ErrorResponse(errors);
    return new ResponseEntity<>(errorResponse, HttpStatus.BAD_REQUEST);
}
```

---

#### What the Response Looks Like

When both fields are blank, Spring serializes the `List<String>` into a **JSON array**:

```json
{
  "timestamp": "2024-01-01T12:00:00",
  "message": [
    "Name cannot be blank",
    "Number cannot be blank"
  ]
}
```

Spring's Jackson serializer automatically maps `List<String>` → JSON array. No extra config needed.

---

#### The Full Picture

```
Request Body (invalid)
        ↓
@Valid in Controller   ← triggers validation
        ↓
MethodArgumentNotValidException thrown
        ↓
handleMethodArgumentNotValid() in GlobalHandler
        ↓
BindingResult.getAllErrors() → collect messages
        ↓
ErrorResponse(List<String>) → 400 BAD REQUEST
```


