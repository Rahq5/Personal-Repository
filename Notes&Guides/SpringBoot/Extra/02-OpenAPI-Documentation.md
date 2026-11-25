## OpenAPI Documentation — Contact API

These notes cover how to document a Spring Boot REST API using OpenAPI/Swagger annotations. Mastering this matters because undocumented APIs are unusable by other developers — documentation _is_ part of the product.

**What's covered:**

- What OpenAPI is and how Spring Boot auto-generates it
- Configuring the OpenAPI bean (title, version, description)
- Grouping and describing operations with `@Tag` and `@Operation`
- Documenting responses with `@ApiResponse`
- Specifying media types with `produces`

---

### What is OpenAPI?

> **OpenAPI** — A standard, machine-readable format that describes the capabilities of a REST API (paths, parameters, responses, schemas).

Spring Boot can auto-generate an OpenAPI spec for your app. Two auto-generated endpoints become available when the dependency is added:

|URL|What it gives you|
|---|---|
|`/v3/api-docs`|Raw JSON OpenAPI spec|
|`/swagger-ui/index.html`|Human-friendly interactive docs UI|

The spec detects **every operation** (controller method) automatically — no manual work needed just to get the base output.

---

### Task 1 — Dependency & Auto-generation

The OpenAPI dependency must be added to `pom.xml`. Unlike Spring Boot starters, this one **requires an explicit version** — the parent POM does not manage it.

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.x.x</version>
</dependency>
```

> **Parent POM** — The `spring-boot-starter-parent` that manages versions for all official Spring Boot starters. Third-party libs like springdoc are NOT covered by it.

Run the app → visit `/v3/api-docs` → you'll see a JSON spec with all your endpoints already detected.

---

### Task 2 & 3 — OpenAPI Bean Configuration

Create a config class that defines the API's identity.

```
src/main/java/
└── config/
    └── OpenApiConfig.java
```

```java
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class OpenApiConfig {

    @Bean
    public OpenAPI openAPI() {
        return new OpenAPI()
                .info(new Info()
                        .title("Contact API")
                        .version("1.0")
                        .description("API for managing contacts"));
    }
}
```

> **`@Configuration`** — Marks this class as a source of `@Bean` definitions. Spring scans it at startup and registers the beans into the application context.

> **`@Bean`** — Tells Spring: "call this method and register its return value as a managed bean." Here, it registers the `OpenAPI` object so springdoc picks it up to populate the Swagger UI header.

This updates the Swagger UI title, version, and description fields at the top of the page.

---

### Task 4 & 5 — `@Tag`: Grouping Operations

> **`@Tag`** — An annotation that assigns a name (and optionally a description) to group operations together in the Swagger UI.

Apply at the **class level** so every method in the controller inherits it:

```java
import io.swagger.v3.oas.annotations.tags.Tag;

@RestController
@Tag(name = "Contact Controller", description = "Create and retrieve contacts")
public class ContactController {
    // ...
}
```

Without `@Tag`, Spring auto-generates a tag from the class name (`contact-controller`). With it, you control the display name and description shown in the UI.

---

### Task 6 — `@Operation`: Describing Each Endpoint

> **`@Operation`** — Applied at the **method level** to give an endpoint a `summary` (short label) and `description` (longer explanation).

```java
import io.swagger.v3.oas.annotations.Operation;

@GetMapping("/contact/all")
@Operation(summary = "Retrieves contacts", description = "Provides a list of all contacts")
public List<Contact> getContacts() { ... }

@GetMapping("/contact/{id}")
@Operation(summary = "Get contact by Id", description = "Returns a contact based on an ID")
public Contact getContact(@PathVariable String id) { ... }

@PostMapping("/contact")
@Operation(summary = "Create Contact", description = "Creates a contact from the provided payload")
public Contact createContact(@RequestBody Contact contact) { ... }
```

---

### Task 7 — `@ApiResponse`: Documenting Responses

> **`@ApiResponse`** — Documents what the client should expect back for a given HTTP status code, including the response body schema.

#### `getContacts` — returns a JSON array

```java
import io.swagger.v3.oas.annotations.media.*;
import io.swagger.v3.oas.annotations.responses.ApiResponse;

@ApiResponse(
    responseCode = "200",
    description = "Successful retrieval of contacts",
    content = @Content(array = @ArraySchema(schema = @Schema(implementation = Contact.class)))
)
@GetMapping(value = "/contact/all", produces = MediaType.APPLICATION_JSON_VALUE)
public List<Contact> getContacts() { ... }
```

#### `getContact` — can return 200 or 404

```java
import io.swagger.v3.oas.annotations.responses.ApiResponses;

@ApiResponses(value = {
    @ApiResponse(responseCode = "200", description = "Successful retrieval of contact",
        content = @Content(schema = @Schema(implementation = Contact.class))),
    @ApiResponse(responseCode = "404", description = "Contact doesn't exist",
        content = @Content(schema = @Schema(implementation = ErrorResponse.class)))
})
@GetMapping(value = "/contact/{id}", produces = MediaType.APPLICATION_JSON_VALUE)
public Contact getContact(@PathVariable String id) { ... }
```

#### `createContact` — can return 201 or 400

```java
@ApiResponses(value = {
    @ApiResponse(responseCode = "201", description = "Successful creation of contact"),
    @ApiResponse(responseCode = "400", description = "Bad request: unsuccessful submission",
        content = @Content(schema = @Schema(implementation = ErrorResponse.class)))
})
@PostMapping(value = "/contact", produces = MediaType.APPLICATION_JSON_VALUE)
public Contact createContact(@RequestBody Contact contact) { ... }
```

> **`@ArraySchema`** — Used when the response body is a JSON array, not a single object. Wraps `@Schema` to tell Swagger the array element type.

> **`@Content`** — Specifies the media type and schema of the response/request body. Without it, Swagger shows no schema preview for that response.

---

### Task 8 — `produces` on `@GetMapping`

Adding `produces = MediaType.APPLICATION_JSON_VALUE` to the mapping annotation explicitly declares that the endpoint returns `application/json`. This is reflected in the Swagger UI and helps HTTP clients (and the spec) understand the content type.

```java
@GetMapping(value = "/contact/all", produces = MediaType.APPLICATION_JSON_VALUE)
```

> **`MediaType.APPLICATION_JSON_VALUE`** — A Spring constant equal to the string `"application/json"`. Declares the MIME type this endpoint produces.

---

### Big Picture Flow

```
pom.xml (springdoc dependency)
        |
        v
Spring Boot starts
        |
        v
springdoc scans @RestController methods
        |
        v
OpenApiConfig @Bean → sets title / version / description
        |
        v
@Tag on class → groups endpoints in Swagger UI
        |
        v
@Operation on method → adds summary + description per endpoint
        |
        v
@ApiResponse on method → documents each possible HTTP response + schema
        |
        v
produces = APPLICATION_JSON_VALUE → declares content type
        |
        v
/v3/api-docs  ─────────→ Raw JSON spec
/swagger-ui   ─────────→ Human-friendly interactive UI
```
