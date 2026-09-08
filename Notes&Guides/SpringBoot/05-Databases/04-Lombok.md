## Lombok 
Project Lombok is a Java library that plugs into your editor and build tools to spice up your Java. It automatically generates repetitive code (boilerplate) during compilation to keep your classes clean.

* **What it fixes**: Java requires a lot of code for simple data objects (Getters, Setters, Constructors). This makes files long and hard to read. Lombok removes this clutter.
* **Steps to use**:
    1. Add the dependency to your `pom.xml`.
    2. Enable "Annotation Processing" in VSCode/IDE settings.
    3. Annotate your classes.

---

### Core Annotations

> **Boilerplate**: Sections of code that are repeated in many places with little to no variation (like Getters/Setters).

#### Data Access & Mutators
* **@Getter / @Setter**: Generates the `getX()` and `setX()` methods for your fields.
* **@ToString**: Generates a string representation of the object including all fields.
* **@EqualsAndHashCode**: Generates `equals(Object other)` and `hashCode()` methods based on the fields.

#### Constructors
* **@NoArgsConstructor**: Creates a constructor with no arguments (needed for JPA/Hibernate).
* **@AllArgsConstructor**: Creates a constructor with one argument for every field in the class.
* **@RequiredArgsConstructor**: Creates a constructor for all `final` fields or fields marked with `@NonNull`. Often used for **Dependency Injection** in Spring Boot.

#### Bundles & Utilities
* **@Data**: A shortcut annotation that combines `@Getter`, `@Setter`, `@ToString`, `@EqualsAndHashCode`, and `@RequiredArgsConstructor`.
* **@Value**: The immutable version of `@Data`. All fields are made `private final`, and no setters are generated.
* **@Builder**: Implements the Builder Pattern, allowing you to create objects like: `User.builder().name("Abu Baraa").build();`.
* **@Slf4j**: Automatically creates a logger field (`log`) so you can log information without writing `Logger log = ...`.

---

### Big Picture Flow

`Write Java Code with Annotations` —> `Lombok Plugin (Compile Time)` —> `Generated .class file with full Boilerplate` —> `Clean, Readable Source Code`
