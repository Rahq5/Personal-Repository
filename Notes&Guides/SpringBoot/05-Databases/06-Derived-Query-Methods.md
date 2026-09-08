## Derived Query method (custom queries by JPA)
### What is a Derived Query Method

When using `JpaRepository`, you get pre-built methods like `findById()` and `deleteById()`. But sometimes you need to query by a field that isn't the primary key — like finding a student by name, or finding all students above a certain grade. Writing raw SQL for every case defeats the purpose of JPA.

**Derived Query Methods** solve this: you write a method signature using a specific naming convention directly inside your repository interface, and JPA reads the name, understands the intent, and generates the SQL query automatically at runtime — no implementation needed.

---

### How to Write It (Code)

Place the method inside your repository interface. Nothing else — no `@Query`, no SQL, no method body.

```java
@Repository
public interface StudentRepository extends JpaRepository<Student, Long> {

    // derived query method — JPA generates the SQL from the name
    List<Student> findByName(String name);

}
```

That's the entire thing. JPA reads `findByName` at startup, maps `Name` to the `name` field in your `Student` entity, and builds the query.

---

### How It Works Under the Hood

When Spring starts, it scans every method in your repository interface that doesn't already exist in `JpaRepository`. For each one, it parses the method name using a fixed keyword grammar and translates it into a JPQL query (which Hibernate then converts to SQL).

```
Method name (your code)
        ↓
Spring Data parses the name into tokens
        ↓
Tokens mapped to entity fields and conditions
        ↓
JPQL query assembled internally
        ↓
Hibernate translates JPQL → SQL
        ↓
SQL runs against the database
```

> **JPQL (Java Persistence Query Language)** — a query language that targets entity class names and field names instead of table names and column names. Hibernate translates it to the actual SQL dialect of your database.

You never see the SQL yourself — but it's being generated and executed every time the method is called.

---

### Anatomy of a Derived Query Method Signature

A derived query method is built from three parts assembled in order:

```
[Subject keyword] + [By] + [Field name] + [Condition keyword] + [And/Or + Field...]
```

|Part|Role|Examples|
|---|---|---|
|**Subject keyword**|What operation to perform|`findBy`, `countBy`, `deleteBy`, `existsBy`|
|**By**|Required separator — marks where conditions start|always `By`|
|**Field name**|Entity field to filter on — must match exactly (case-sensitive first letter)|`Name`, `Age`, `Score`|
|**Condition keyword**|Optional modifier on the field|`GreaterThan`, `LessThan`, `Containing`, `Between`, `IsNull`|
|**And / Or**|Chain multiple conditions|`AndAge`, `OrScore`|

> The field name after `By` must match the **exact field name** declared in your `@Entity` class, with the first letter capitalized. If the field is `phoneNumber`, you write `PhoneNumber`.

---

### Examples

#### Example 1 — Find by a single field

```java
List<Student> findByName(String name);
```

SQL generated:

```sql
SELECT * FROM student WHERE name = ?
```

---

#### Example 2 — Find with a condition modifier

```java
List<Student> findByAgeGreaterThan(int age);
```

SQL generated:

```sql
SELECT * FROM student WHERE age > ?
```

---

#### Example 3 — Multiple conditions chained

```java
List<Student> findByAgeGreaterThanAndScoreBetweenAndNameContaining(int age, double minScore, double maxScore, String keyword);
```

SQL generated:

```sql
SELECT * FROM student
WHERE age > ?
  AND score BETWEEN ? AND ?
  AND name LIKE '%' || ? || '%'
```

> **`Containing`** — translates to a SQL `LIKE` with wildcards on both sides. `findByNameContaining("ar")` matches "Harry", "Baraa", "Tariq".

> **`Between`** — expects **two parameters** in the method signature, representing the lower and upper bounds. JPA maps them to the `BETWEEN ? AND ?` clause in order.

---

### Quick Reference — Common Condition Keywords

|Keyword|SQL equivalent|
|---|---|
|`GreaterThan`|`> ?`|
|`LessThan`|`< ?`|
|`Between`|`BETWEEN ? AND ?`|
|`Containing`|`LIKE '%?%'`|
|`StartingWith`|`LIKE '?%'`|
|`EndingWith`|`LIKE '%?'`|
|`IsNull`|`IS NULL`|
|`IsNotNull`|`IS NOT NULL`|
|`OrderByFieldAsc`|`ORDER BY field ASC`|

Official reference → https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html

