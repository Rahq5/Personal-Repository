## Object Relational Mapper (ORM)

> These notes cover what an ORM is, why it exists, how a Java class becomes a database table, and how primary keys work — all theory-first, no SQL knowledge assumed.

**What's covered:**

- What a database table looks like conceptually
- What ORM is and the problem it solves
- Turning a Java class into a table with `@Entity`
- What a primary key is and why every table needs one
- Applying `@Id` and `@GeneratedValue` to an entity

---

### What is a Database Table (Quick Context)

Before anything — a database table is just a **grid**, like a spreadsheet.

```
| id | name     | grade |
|----|----------|-------|
| 1  | Ahmad    | 90    |
| 2  | Sara     | 85    |
```

Each **row** = one record (one student). Each **column** = one field (one piece of data).

That's all a table is. Keep this image in mind.

---

### The Problem ORM Solves

In Java, you store data in **objects**:

```java
Student student = new Student();
student.setName("Ahmad");
student.setGrade(90);
```

In a database, data lives in **tables** (rows and columns).

These are two completely different worlds. Normally, you'd have to manually write SQL commands to push your Java object into the database and pull it back out. That's tedious, error-prone, and forces you to think in two languages at once.

> **ORM (Object Relational Mapper)** — a tool that automatically maps Java objects to database tables (and back), so you never have to write raw SQL yourself.

The "mapping" means:

- Your **Java class** → becomes a **table**
- Each **field** in the class → becomes a **column**
- Each **object instance** → becomes a **row**

Hibernate is the ORM that Spring Boot uses. JPA is the set of rules Hibernate follows.

---

### How to Create a Table — The `@Entity` Annotation

You create a database table by writing a plain Java class and putting `@Entity` on it. That's the signal to Hibernate: _"turn this class into a table."_

```java
@Entity
public class Student {

    private String name;
    private int grade;

}
```

**What happens under the hood when the app starts:**

Hibernate reads this class → generates the SQL to create a table called `student` → runs it against H2 automatically. You never touched SQL. The table now exists.

> **`@Entity`** — marks a Java class as a persistent entity, meaning Hibernate will create and manage a matching database table for it.

One class = one table. Always.

---

### What is a Primary Key

Every table needs a way to **uniquely identify each row**. Imagine two students both named "Ahmad" with the same grade — how does the database tell them apart?

That's what a primary key is for.

> **Primary Key** — a column whose value is **unique for every row** and **never null**. It's the row's permanent identity inside the table.

Think of it like a national ID number — two people can share a name, but no two people share the same ID number.

```
| id | name     | grade |   ← id is the primary key
|----|----------|-------|
| 1  | Ahmad    | 90    |   ← unique
| 2  | Ahmad    | 85    |   ← different row, different id
```

Without a primary key, the database has no reliable way to find, update, or delete a specific row.

---

### Applying a Primary Key — `@Id`

You tell Hibernate which field is the primary key using `@Id`:

```java
@Entity
public class Student {

    @Id
    private Long id;

    private String name;
    private int grade;

}
```

**What happens under the hood:**

Hibernate sees `@Id` on `id` → generates the table with `id` marked as the primary key column → the database enforces uniqueness on that column automatically.

> **`@Id`** — marks a field as the primary key of the entity's table.

---

### Auto-Generating the ID — `@GeneratedValue`

If you use `@Id` alone, _you_ are responsible for setting a unique id on every object before saving it. That's a problem — you'd have to track which IDs are already taken.

The fix: let the **database** generate the ID for you automatically.

```java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private int grade;

}
```

**What happens under the hood:**

`GenerationType.IDENTITY` tells Hibernate: _"delegate ID generation to the database."_ The database keeps an internal counter. Every time a new row is inserted, it increments the counter and assigns the next number as the ID — 1, 2, 3, and so on. You never touch the `id` field yourself; the database fills it in after the insert.

> **`@GeneratedValue(strategy = GenerationType.IDENTITY)`** — instructs the database to auto-increment the primary key value on every new row insertion.

**Why `IDENTITY` specifically?** There are multiple strategies (SEQUENCE, TABLE, AUTO). `IDENTITY` is the simplest — it relies on the database's own auto-increment feature, which H2 and PostgreSQL both support natively.

---

### Big Picture Flow

```
Java class with @Entity
        ↓
Hibernate reads the class on startup
        ↓
Generates + runs SQL: CREATE TABLE student (id, name, grade)
        ↓
@Id marks the primary key column
        ↓
@GeneratedValue → database auto-increments id on each insert
        ↓
You save a Student object → becomes a new row in the table
        ↓
id is filled in automatically by the database
```

---

**Official docs:**

- JPA `@Entity`: https://jakarta.ee/specifications/persistence/3.1/apidocs/jakarta.persistence/jakarta/persistence/entity
- `@GeneratedValue` strategies: https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#identifiers-generated
