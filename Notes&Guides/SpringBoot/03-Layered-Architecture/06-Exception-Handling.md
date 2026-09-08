### ExceptionHandling

#### Try-Catch and Difference Between Checked and Unchecked Exceptions

##### Try-Catch
A way to **handle errors gracefully** instead of crashing the app.

```java
try {
    // risky code
} catch (SomeException e) {
    // handle it
}
````

---

##### Checked vs Unchecked

**The only real difference:**

- **Checked** → method declares `throws` → Java **forces** you to handle it at compile time
- **Unchecked** → no declaration → Java stays silent, crashes at **runtime** if triggered

||Checked|Unchecked|
|---|---|---|
|Extends|`Exception`|`RuntimeException`|
|Java forces handling?|✅ Yes|❌ No|
|When it fails|Compile time|Runtime|
|Fault|Outside world (file, db, network)|Your code (bad index, null, bad id)|

##### Checked Exception

```java
// declared on the method — Java forces callers to handle it
private int findIndexById(String id) throws NoContactException {
    return list.stream()
        .findFirst()
        .orElseThrow(() -> new NoContactException());
}

// caller MUST catch it
try {
    Contact contact = contactService.getContactById(id);
    return new ResponseEntity<>(contact, HttpStatus.OK);
} catch (NoContactException e) {
    return new ResponseEntity<>(HttpStatus.NOT_FOUND); // 404
}
```

##### Unchecked Exception

```java
// extends RuntimeException — no throws declaration needed
public class ContactNotFoundException extends RuntimeException {
    public ContactNotFoundException(String id) {
        super("The id '" + id + "' does not exist");
    }
}

// just throw it — no try-catch required anywhere
.orElseThrow(() -> new ContactNotFoundException(id));
```

---

#### ErrorResponse

A simple class to structure the error body returned to the client.

```java
public class ErrorResponse {
    private String message;

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "dd-MM-yyyy hh:mm:ss")
    private LocalDateTime timestamp; // formatted timestamp in JSON response

    public ErrorResponse(String message) {
        this.message = message;
        this.timestamp = LocalDateTime.now();
    }
}
```

Response the client receives:

```json
{
  "message": "The id '123' does not exist",
  "timestamp": "04-03-2026 06:30:00"
}
```

---

#### @ControllerAdvice + @ExceptionHandler

Instead of try-catch in every controller method → define **one global handler** for the whole app.

Flow of how the exception flys from service to the exception being thrown 
```
[Service Class] orElseThrow(() -> new ContactNotFoundException(id))
        ↓
[Spring Internals] catches it (@ControllerAdvice is watching every request)
        ↓
[Spring Internals] looks for @ExceptionHandler(ContactNotFoundException.class)
        ↓
[ApplicationExceptionHandler] handleContactNotFoundException(ContactNotFoundException ex) gets called
        ↓
[ApplicationExceptionHandler] returns ResponseEntity with 404 + ErrorResponse body to client
```

code example
```java
@ControllerAdvice // global scope
public class ApplicationExceptionHandler {

    @ExceptionHandler(ContactNotFoundException.class) // catches this specific exception
    public ResponseEntity<Object> handleContactNotFoundException(ContactNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(ex.getMessage()); // build error body
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND); // 404 + body
    }
}
```
