## Cascade
### The Problem — Deleting a Grade That Has Students

Try to delete a `Grade` that has students attached to it. You'd expect it to just disappear. Instead, the database throws an error and refuses.

This happens because of the **foreign key constraint**. The `student` table has a `grade_id` column pointing at the `grade` table. The database enforces a rule: _"a foreign key must always point to something that exists."_ If you delete the grade, those `grade_id` values in the student rows would point to nothing — a broken reference. The database blocks the deletion to protect data integrity.

```
DELETE grade WHERE id = 1  ← you ask this

Database checks:
student table has rows where grade_id = 1
→ deleting grade 1 would orphan those rows
→ ❌ ERROR: foreign key constraint violation
```

> **Orphan** — a child row whose foreign key points to a parent that no longer exists.

So the question becomes: what should happen to the students when their grade is deleted? In most real scenarios, the answer is — delete them too. That's what cascade is for.

---

### What Cascade Does

> **Cascade** — a JPA instruction that tells Hibernate: _"when an operation is performed on the parent entity, automatically perform the same operation on its children."_

Without cascade, JPA operations (save, delete, etc.) are isolated — they only affect the entity you explicitly call them on. With cascade, the operation **flows down** from parent to children automatically.

`CascadeType.ALL` covers every operation:

|Operation|What it means|
|---|---|
|`PERSIST`|saving parent also saves its children|
|`MERGE`|updating parent also updates its children|
|`REMOVE`|deleting parent also deletes its children|
|`REFRESH`|refreshing parent also refreshes its children|
|`DETACH`|detaching parent also detaches its children|

`ALL` is the most common choice. You can pass individual types if you only want specific behavior, but `ALL` covers the full lifecycle.

---

### Adding Cascade to the Code

Cascade is added to the `@OneToMany` annotation on the **parent** side — the `Grade` entity. The `Student` entity stays unchanged.

```java
// Grade.java
@Entity
public class Grade {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String gradeName;

    @OneToMany(mappedBy = "grade", cascade = CascadeType.ALL)
    @JsonIgnore
    private List<Student> students;

    // getters and setters
}
```

Now when you delete a `Grade`, Hibernate first deletes all `Student` rows associated with it, then deletes the `Grade` row itself. The foreign key constraint is never violated because the children are gone before the parent is removed.

```
deleteById(1) called on gradeRepository
        ↓
Hibernate sees CascadeType.ALL on students list
        ↓
DELETE FROM student WHERE grade_id = 1   ← children first
        ↓
DELETE FROM grade WHERE id = 1           ← parent second
        ↓
✅ No constraint violation
```

---

### Big Picture Flow

```
No Cascade
gradeRepository.deleteById(1)
    → tries DELETE FROM grade WHERE id = 1
    → ❌ FK constraint: student rows still reference grade 1

With CascadeType.ALL
gradeRepository.deleteById(1)
    → Hibernate checks Grade.students (cascade = ALL)
    → DELETE FROM student WHERE grade_id = 1  (children removed first)
    → DELETE FROM grade WHERE id = 1          (parent removed after)
    → ✅ clean deletion, no orphans, no constraint errors
```
