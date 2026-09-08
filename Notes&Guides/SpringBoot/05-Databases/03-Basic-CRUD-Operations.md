## Basic Operations in Databases (get,save,delete)

> These notes cover how Spring Boot saves data to a database — from how a Java class becomes a table, to the repository pattern, to the full flow of a POST request.

**What's covered:**

- How a POJO class initializes a table
- What `JpaRepository` is and how to create one
- The repository interface signature explained
- Pre-defined methods you get for free
- How the repository connects to the service layer
- Full POST request flow end to end
### retrieve student 

> Covers how Spring retrieves a single row from the database by ID — what happens at the SQL level, how the service layer handles it, and the full GET request flow.

**What's covered:**

- What retrieval looks like at the SQL level
- How `findById()` works in Java
- What `Optional` is and why Spring uses it
- Wiring retrieval through the service layer
- Full GET request flow

---

#### What Retrieval Looks Like at the SQL Level

When you ask for a student by ID, the database runs this behind the scenes:

```sql
SELECT * FROM student WHERE id = 1;
```

> **`SELECT`** — SQL command that reads rows from a table. **`WHERE`** — filters which rows to return based on a condition.

This returns the one row whose `id` column equals `1`. You never write this yourself — JpaRepository generates it for you. But this is what's actually happening in the database.

---

#### How to Retrieve a Student in Java

`JpaRepository` gives you `findById()` out of the box:

```java
Optional<Student> result = studentRepository.findById(1L);
```

**Why `Optional` and not just `Student`?**

Because the student might not exist. If you ask for ID `99` and no row has that ID, the database returns nothing. `Optional` is Java's way of representing _"this might have a value, or it might be empty"_ — instead of returning `null` and risking a `NullPointerException`.

> **`Optional<T>`** — a Java wrapper that either contains a value (`Optional.isPresent() = true`) or is empty (`Optional.isEmpty() = true`). Forces you to handle the "not found" case explicitly.

To get the actual student out of the `Optional`:

```java
// Safe way — throws a custom exception if not found
Student student = studentRepository.findById(id)
        .orElseThrow(() -> new RuntimeException("Student not found"));
```

`.orElseThrow()` means: _"give me the Student if it exists, otherwise throw this exception."_

---

#### Wiring Retrieval Through the Service Layer

**Repository** — already has `findById()` from `JpaRepository`, nothing to add.

**Service class:**

```java
@Service
public class StudentService {

    private final StudentRepository studentRepository;

    public StudentService(StudentRepository studentRepository) {
        this.studentRepository = studentRepository;
    }

    public Student getStudentById(Long id) {
        return studentRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Student not found"));
    }
}
```

**Controller:**

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    private final StudentService studentService;

    public StudentController(StudentService studentService) {
        this.studentService = studentService;
    }

    @GetMapping("/{id}")
    public Student getStudent(@PathVariable Long id) {
        return studentService.getStudentById(id);
    }
}
```

> **`@PathVariable`** — extracts the `{id}` value from the URL and passes it as a method parameter. A GET to `/students/3` gives you `id = 3`.

---

#### Full GET Request Flow

```
Client sends GET /students/3
        ↓
@RestController receives request
        ↓
@PathVariable extracts id = 3
        ↓
Calls studentService.getStudentById(3)
        ↓
Service calls studentRepository.findById(3)
        ↓
JpaRepository proxy runs:
SELECT * FROM student WHERE id = 3
        ↓
Row found → wrapped in Optional → unwrapped via .orElseThrow()
        ↓
Returns Student object to service → to controller
        ↓
Controller serializes Student to JSON
        ↓
Client receives 200 response with student data

--- if id doesn't exist ---

findById() returns empty Optional
        ↓
.orElseThrow() fires the exception
        ↓
Client receives error response
```

---

**Official docs:**

- `CrudRepository.findById`: https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html#findById(ID)
- Java `Optional`: https://docs.oracle.com/en/java/docs/api/java.base/java/util/Optional.html
### deleting student
> Covers how Spring deletes a row from the database by ID — what happens at the SQL level, how the service layer handles it, and the full DELETE request flow.

**What's covered:**

- What deletion looks like at the SQL level
- How `deleteById()` works in Java
- Why you should check existence before deleting
- Wiring deletion through the service layer
- Full DELETE request flow

---

#### What Deletion Looks Like at the SQL Level

When you delete a student by ID, the database runs this behind the scenes:

```sql
DELETE FROM student WHERE id = 1;
```

> **`DELETE`** — SQL command that removes rows from a table permanently. **`WHERE`** — targets which row to remove. Without it, every row in the table gets deleted.

One matching row found → it's gone. No undo. JpaRepository generates this SQL for you automatically.

---

#### How to Delete a Student in Java

`JpaRepository` gives you `deleteById()` out of the box:

```java
studentRepository.deleteById(1L);
```

**The problem with using it directly:**

If the ID doesn't exist, `deleteById()` throws an `EmptyResultDataAccessException` — an unhelpful, low-level Spring error. Better practice is to check existence first and throw your own meaningful exception.

```java
// Check first
if (!studentRepository.existsById(id)) {
    throw new RuntimeException("Student not found");
}

// Then delete
studentRepository.deleteById(id);
```

> **`existsById(id)`** — returns `true` if a row with that ID exists, `false` otherwise. Runs `SELECT COUNT(*) FROM student WHERE id = ?` under the hood.

---

#### Wiring Deletion Through the Service Layer

**Service class:**

```java
@Service
public class StudentService {

    private final StudentRepository studentRepository;

    public StudentService(StudentRepository studentRepository) {
        this.studentRepository = studentRepository;
    }

    public void deleteStudent(Long id) {
        if (!studentRepository.existsById(id)) {
            throw new RuntimeException("Student not found");
        }
        studentRepository.deleteById(id);
    }
}
```

**Note:** return type is `void` — deletion doesn't return data, it just confirms the action happened (or throws if it couldn't).

**Controller:**

```java
@DeleteMapping("/{id}")
public void deleteStudent(@PathVariable Long id) {
    studentService.deleteStudent(id);
}
```

> **`@DeleteMapping`** — maps HTTP DELETE requests to this method. A DELETE to `/students/3` triggers this with `id = 3`.

---

#### Full DELETE Request Flow

```
Client sends DELETE /students/3
        ↓
@RestController receives request
        ↓
@PathVariable extracts id = 3
        ↓
Calls studentService.deleteStudent(3)
        ↓
Service calls studentRepository.existsById(3)
        ↓
JpaRepository proxy runs:
SELECT COUNT(*) FROM student WHERE id = 3

--- if id doesn't exist ---
existsById() returns false
        ↓
Service throws RuntimeException("Student not found")
        ↓
Client receives error response

--- if id exists ---
existsById() returns true
        ↓
Service calls studentRepository.deleteById(3)
        ↓
JpaRepository proxy runs:
DELETE FROM student WHERE id = 3
        ↓
Row removed from database
        ↓
Controller returns 200 response (empty body)
```

---

**Official docs:**

- `CrudRepository.deleteById`: https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html#deleteById(ID)
- `CrudRepository.existsById`: https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html#existsById(ID)
### saving student

#### How a Table is Initialized via a POJO Class

> **POJO (Plain Old Java Object)** — a simple Java class with fields, getters, and setters. No special logic, just data.

When you annotate a POJO with `@Entity`, Hibernate reads its fields on startup and generates the matching database table automatically.

```java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private int grade;

    // getters and setters
}
```

**What Hibernate does with this at startup:**

```
Student.java (POJO + @Entity)
        ↓
Hibernate reads all fields
        ↓
Creates table: student (id PK, name, grade)
        ↓
Table is now live in H2
```

One field = one column. The class name becomes the table name (lowercase by default). You wrote zero SQL.

---

#### What is JpaRepository and How to Create a Repo

Spring Data JPA provides a built-in interface called `JpaRepository`. When you extend it, Spring automatically generates a full working database layer for you — no implementation code needed.

> **`JpaRepository`** — a Spring Data interface that provides pre-built methods for all standard database operations (save, find, delete, etc.) without writing any SQL or implementation.

**How to create a repository:**

```java
@Repository
public interface StudentRepository extends JpaRepository<Student, Long> {
}
```

That's the entire file. The body is empty — Spring fills in everything at runtime.

---

#### Repository Signature Explained

```java
JpaRepository<Student, Long>
```

This takes two type parameters — both matter:

|Parameter|Value|Meaning|
|---|---|---|
|First|`Student`|The `@Entity` class this repo manages — tells Spring which table to talk to|
|Second|`Long`|The type of the primary key (`@Id` field) in that entity — tells Spring how to look rows up by ID|

So `JpaRepository<Student, Long>` means: _"manage the `student` table, where rows are identified by a `Long` id."_

If your entity used `Integer` as the ID type, you'd write `JpaRepository<Student, Integer>` instead.

---

#### Pre-defined Methods You Get for Free

By extending `JpaRepository`, your repository instantly has these methods — no code needed:

|Method|What it does|
|---|---|
|`save(entity)`|Inserts a new row, or updates if ID already exists|
|`findById(id)`|Returns one row by its primary key|
|`findAll()`|Returns every row in the table|
|`deleteById(id)`|Deletes the row with that ID|
|`existsById(id)`|Returns true/false whether a row with that ID exists|
|`count()`|Returns total number of rows|

Spring generates the actual SQL for all of these under the hood. You call `studentRepository.save(student)` and Spring translates that into `INSERT INTO student ...` automatically.

---

#### Why an Interface — Not a Class

You might wonder: why is the repository an `interface` and not a `class`?

Because Spring uses a mechanism called **dynamic proxy** — at runtime, Spring generates a real class that implements your interface for you. You define _what_ you need (`JpaRepository`), Spring builds _how_ it works.

This means:

- You write zero implementation code
- Spring handles all SQL generation
- You only add custom methods to the interface if the pre-defined ones aren't enough

> **Dynamic Proxy** — Spring generates a concrete implementation of your repository interface at runtime, wiring it to the actual database.

---

#### Connecting Repo to Service Layer

The repository doesn't get called directly from the controller. It goes through a **service class** — this is the three-layer architecture at work.

**Repository interface:**

```java
@Repository
public interface StudentRepository extends JpaRepository<Student, Long> {
}
```

**Service class (implementation):**

```java
@Service
public class StudentService {

    private final StudentRepository studentRepository;

    // Spring injects the repo automatically (dependency injection)
    public StudentService(StudentRepository studentRepository) {
        this.studentRepository = studentRepository;
    }

    public Student saveStudent(Student student) {
        return studentRepository.save(student);
    }
}
```

**What `@Service` does:** marks this class as a service bean — Spring manages its lifecycle and injects it wherever needed.

**What constructor injection does:** Spring sees `StudentRepository` as a parameter → finds its generated implementation → injects it automatically. You never call `new StudentRepository()` yourself.

---

#### Full POST Request Flow

```
Client sends POST /students (JSON body: name, grade)
        ↓
@RestController receives request
        ↓
Deserializes JSON → Student object
        ↓
Calls studentService.saveStudent(student)
        ↓
Service calls studentRepository.save(student)
        ↓
JpaRepository (Spring-generated proxy) runs:
INSERT INTO student (name, grade) VALUES (?, ?)
        ↓
Database auto-generates and assigns the id
        ↓
Returns saved Student object (now with id filled in)
        ↓
Controller returns 200/201 response with saved Student as JSON
```

The `id` field in your Student object starts as `null` when it enters the service. By the time `save()` returns, the database has filled it in and the returned object carries the real generated ID.

---

**Official docs:**

- Spring Data JpaRepository: https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html
- Spring Data Repositories guide: https://docs.spring.io/spring-data/jpa/reference/repositories/definition.html

