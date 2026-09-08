## Bidirectional: One to Many

### The Problem — Navigation Only Goes One Way

In the previous section, you built a **unidirectional Many-to-One** relationship. The `Student` entity holds a `@ManyToOne` reference to `Grade`, which means there is a **foreign key** (`grade_id`) living inside the `student` table.

This gives you one direction of navigation:

```
Student → Grade  ✅  (student.getGrade() works)
Grade → Student  ❌  (grade.getStudents() doesn't exist)
```

In real terms: if you query a student, you can see which grade they belong to. But if you query a grade, you get nothing about which students are enrolled in it. The `grade` table has no column pointing back to students — it never did, and it shouldn't. That's not how relational databases work.

The problem isn't in the database. The database is fine. The problem is on the **Java side** — the `Grade` entity has no field representing its students, so there's no way to navigate from a grade to its list of students in your code.

---

### The Fix — Bidirectional Mapping

To fix this, you add a `List<Student>` field to the `Grade` entity and annotate it with `@OneToMany`. This tells Hibernate: _"one grade can have many students."_

Now navigation works in both directions:

```
Student → Grade  ✅  (student.getGrade())
Grade → Student  ✅  (grade.getStudents())
```

The database structure does **not change**. No new columns, no new tables. The foreign key is still only in the `student` table. You're just teaching Java about a path that already exists in the data.

---

### `mappedBy` — The Most Important Part

When you add `@OneToMany` to the `Grade` entity, Hibernate needs to know one critical thing: **who owns this relationship?**

Without being told, Hibernate assumes you want a brand new join table — a third table in the database that maps grade IDs to student IDs. This is the **absolute NO**.

```
❌ What Hibernate does if you forget mappedBy:

grade_students (join table — created by mistake)
┌──────────┬────────────┐
│ grade_id │ student_id │
├──────────┼────────────┤
│    1     │     1      │
│    1     │     2      │
└──────────┴────────────┘
```

You already have a foreign key in the `student` table that handles this relationship perfectly. Creating a join table duplicates that information and pollutes your database with a table you never asked for.

`mappedBy` prevents this. It tells Hibernate: _"don't create anything new — this side of the relationship is already managed by the `grade` field in the `Student` class."_

```java
// Grade.java — the "one" side
@Entity
public class Grade {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String gradeName;

    @OneToMany(mappedBy = "grade")   // "grade" = field name in Student class
    private List<Student> students;

    // getters and setters
}
```

The value you pass to `mappedBy` must be the **exact field name** in the `Student` class that holds the `@ManyToOne` annotation. If you named it `private Grade grade` in `Student`, then `mappedBy = "grade"`. If you named it `private Grade gradeLevel`, then `mappedBy = "gradeLevel"`. It's a direct reference.

```
mappedBy = "grade"
              ↓
    Student.java → private Grade grade;  ← must match exactly
```

**Student.java stays the same** — no changes needed. The `@ManyToOne` side is always the owner and always holds the foreign key column.

```java
// Student.java — unchanged
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToOne(optional = false)
    @JoinColumn(name = "grade_id", referencedColumnName = "id")
    private Grade grade;   // <-- this name is what mappedBy references

    // getters and setters
}
```

---

### The Infinite Loop Problem — Why `@JsonIgnore` Exists

Once the relationship is bidirectional, a new problem surfaces the moment you try to serialize either entity to JSON.

Jackson (the library Spring uses to convert objects to JSON) starts walking the object graph:

```
Serialize Grade
    → has List<Student>
        → serialize Student[0]
            → has Grade
                → serialize Grade again
                    → has List<Student>
                        → serialize Student[0] again
                            → ...forever
```

This loop never terminates. The response body grows infinitely until the server crashes or returns a stack overflow error. The endpoint that used to work now breaks the moment bidirectionality is introduced.

> **Serialization** — the process of converting a Java object into JSON (or another format) to send it over HTTP.

`@JsonIgnore` is the fix. You place it on one side of the relationship to tell Jackson: _"when serializing, stop here — don't follow this field."_

```java
// Grade.java
@OneToMany(mappedBy = "grade")
@JsonIgnore                    // Jackson stops here, no infinite loop
private List<Student> students;
```

Now when you serialize a `Grade`, Jackson sees the `students` field, reads `@JsonIgnore`, and simply skips it. When you serialize a `Student`, it serializes the nested `Grade` normally — but that `Grade` won't trigger a second loop because `students` is ignored.

```
Serialize Grade
    → List<Student> → @JsonIgnore → skipped ✅

Serialize Student
    → Grade → serialize Grade
        → List<Student> → @JsonIgnore → skipped ✅
```

The choice of which side to put `@JsonIgnore` on depends on what you want in your API responses. If GET `/student/{id}` should return the student's grade info, put `@JsonIgnore` on the `Grade` side's list. If GET `/grade/{id}` should return students, put it on the `Student` side's reference instead. Usually, `@JsonIgnore` goes on the `@OneToMany` side (the parent's list) because fetching a list of students per grade can be a separate endpoint.

---

### Final Structure

```java
// Grade.java
@Entity
public class Grade {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String gradeName;

    @OneToMany(mappedBy = "grade")
    @JsonIgnore
    private List<Student> students;

    // getters and setters
}
```

```java
// Student.java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToOne(optional = false)
    @JoinColumn(name = "grade_id", referencedColumnName = "id")
    private Grade grade;

    // getters and setters
}
```

---

### Big Picture Flow

```
Database (unchanged)
student table: id | name | grade_id (FK)
grade table:   id | grade_name

Java (new — bidirectional)
Grade ──@OneToMany(mappedBy="grade")──► List<Student>  [read only, no FK here]
Student ──@ManyToOne──► Grade                          [owns the FK]

HTTP GET /student/1
    → serialize Student
        → serialize nested Grade
            → List<Student> → @JsonIgnore → stop ✅
            → response: { id, name, grade: { id, gradeName } }

HTTP GET /grade/1
    → serialize Grade
        → List<Student> → @JsonIgnore → stop ✅
        → response: { id, gradeName }
        (students not in response — fetch via separate endpoint if needed)
```

