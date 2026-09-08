# SpringBoot Databases
## Spring Databases — H2 & JPA Introduction

> These notes cover how Spring Boot connects to databases, what H2 and JPA are, and how to wire them up with a REST API — the foundation before touching real databases like PostgreSQL.

**What's covered:**

- Why databases matter in Spring apps
- What H2 is and why we use it
- What JPA is and its role
- Configuring `application.properties`
- Accessing the H2 console via browser

---

#### What Are We Learning Here

When you build a REST API, your data has to live _somewhere_. Spring Boot gives you a clean way to connect to a database — and before dealing with a real database like PostgreSQL, developers use **H2** to move fast and test locally without any setup.

---

#### What is H2

> **H2** — a lightweight, in-memory relational database written in Java. It runs _inside_ your Spring app, no installation needed.

- It lives in RAM — data is gone when the app stops
- It comes bundled with Spring Boot (just add the dependency)
- It has a **browser-based console** so you can inspect tables visually
- Used for: dev, testing, and learning — not production

---

#### What is JPA

> **JPA (Java Persistence API)** — a _specification_ (a set of rules/interfaces) that defines how Java objects map to database tables.

> **Hibernate** — the most common _implementation_ of JPA. Spring Boot uses it under the hood by default.

Think of it this way:

- JPA = the contract ("here's how persistence should work")
- Hibernate = the actual worker that does the job
- Spring Data JPA = Spring's wrapper on top that makes it even easier (gives you `JpaRepository`)

You write Java classes → JPA/Hibernate turns them into SQL tables automatically.

---

#### REST API + H2 — How They Connect

```
HTTP Request
     ↓
Controller  (@RestController)
     ↓
Service     (business logic)
     ↓
Repository  (JpaRepository)
     ↓
JPA / Hibernate
     ↓
H2 In-Memory Database
```

Your `@Entity` classes define the table structure. Spring Boot creates the tables automatically on startup using H2.

---

#### application.properties Configuration

```properties
# Enable the H2 browser console
spring.h2.console.enabled=true

# The URL path to access the console
spring.h2.console.path=/h2

# The in-memory database name (you choose the name after "mem:")
spring.datasource.url=jdbc:h2:mem:grade-submission
```

**Line by line:**

- `console.enabled=true` → turns on the visual web UI for H2
- `console.path=/h2` → the route you hit in the browser: `localhost:8080/h2`
- `datasource.url` → tells Spring _which_ H2 database to connect to. `mem:` means in-memory. `grade-submission` is just the database name you gave it.

---

#### Logging Into H2 Console

1. Run your Spring Boot app
2. Open browser → `http://localhost:8080/h2`
3. You'll see a login form — paste your datasource URL in the **JDBC URL** field:

```
jdbc:h2:mem:grade-submission
```

4. Leave username as `sa`, password empty (defaults)
5. Click **Connect**

You're now inside the database — you can run SQL queries and see your tables.

---

#### Big Picture Flow

```
application.properties
        ↓
Spring Boot reads datasource config
        ↓
Hibernate creates tables from @Entity classes
        ↓
App starts → H2 database is live in memory
        ↓
REST API ←→ JpaRepository ←→ H2
        ↓
Browser → localhost:8080/h2 → console access
```

---

**Official docs:**

- H2 Console: https://www.h2database.com/html/tutorial.html#tutorial_starting_h2_console
- Spring Data JPA: https://docs.spring.io/spring-data/jpa/docs/current/reference/html/
- Spring Boot datasource config: https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.data

