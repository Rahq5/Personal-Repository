## Unidirectional: many to one

This article explains how to link two database tables where multiple records (Many) in a child table point to a single record (One) in a parent table. In this unidirectional setup, the child can access the parent, but the parent remains independent and unaware of its children.

**What you'll cover:**

- Structual meaning of Many-to-One
    
- Roles of Parent and Child tables
    
- Foreign Key (FK) logic
    
- Implementation with `@ManyToOne`
    
- `@JoinColumn` and `referencedColumnName` parameters
    

---

### Theory Part

#### What does Many-to-One Mean

Many rows in Table A (child) belong to one row in Table B (parent).

> **Example**: Many Students belong to one Grade level (e.g., Grade 10).

- Many students are enrolled in the same grade.
    
- One specific grade contains many different students.
    
- Navigation flows from Student → Grade only.
    

#### Parent and Child Tables

- **Parent Table (Grade)**:
    
    - Holds the primary data (Grade 10, Grade 11).
        
    - One row represents one category.
        
    - Does **NOT** store the link to students.
        
- **Child Table (Student)**:
    
    - Holds the **Foreign Key** column.
        
    - Many rows point to the same Parent row.
        
    - Navigation flows **from** child **to** parent.
        

#### What's a Foreign Key

A column in the child table that stores the **Primary Key (PK)** value of a parent table row.

**Student Table Layout:**

|**student_id (PK)**|**name**|**grade_id (FK)**|
|---|---|---|
|1|Ahmed|10|
|2|Fatima|10|
|3|Omar|11|

The `grade_id` links the student to a row in the `grade` table.

---

### Code Part

#### Parent Class: Grade

```java
@Entity
@Table(name = "grade")
public class Grade {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(name = "grade_name", nullable = false)
    private String gradeName;

    // Getters and Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    
    public String getGradeName() { return gradeName; }
    public void setGradeName(String gradeName) { this.gradeName = gradeName; }
}
```

#### Child Class: Student

```java
@Entity
@Table(name = "student")
public class Student {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long studentId;
    
    @Column(name = "name", nullable = false)
    private String name;
    
    @ManyToOne(optional = false)
    @JoinColumn(name = "grade_id", referencedColumnName = "id")
    private Grade grade;

    // Getters and Setters
    public Long getStudentId() { return studentId; }
    public void setStudentId(Long studentId) { this.studentId = studentId; }
    
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    
    public Grade getGrade() { return grade; }
    public void setGrade(Grade grade) { this.grade = grade; }
}
```

---

### Annotation Breakdown

- **@ManyToOne(optional = false)**:
    
    - `@ManyToOne`: Defines the relationship (Many Students -> One Grade).
        
    - `optional = false`: A Student **MUST** have a Grade assigned. The database will enforce a `NOT NULL` constraint.
        
- **@JoinColumn**:
    
    - `name = "grade_id"`: The name of the column in the `student` table that holds the FK.
        
    - `referencedColumnName = "id"`: The column in the `grade` table that this FK points to.
        

---

### Usage Example

```java
// 1. Get the Parent from DB
Grade grade10 = gradeRepository.findById(10L).orElseThrow();

// 2. Create Child and Link to Parent
Student student = new Student();
student.setName("Abu Baraa");
student.setGrade(grade10); 

// 3. Save the Child
studentRepository.save(student);
```

---

### Key Points to Remember

- **Ownership**: The Child (`Student`) owns the relationship because it holds the `@ManyToOne` annotation.
    
- **Unidirectional**: You can call `student.getGrade()`, but you cannot call `grade.getStudents()` because the Grade class has no reference to Student.
    
- **Integrity**: `optional = false` ensures no student exists without a grade in the database.
    

---

