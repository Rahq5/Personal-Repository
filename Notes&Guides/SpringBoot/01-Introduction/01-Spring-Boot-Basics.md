# Introduction 
## Introduction

Spring Boot is a framework that makes building Java web applications faster by removing most of the setup work. Instead of writing lots of configuration files, Spring Boot gives you defaults that work out of the box. You can start coding your application logic immediately.

---

## What is Spring Boot

Spring Boot is built on top of the Spring Framework. It takes care of:

- Auto-configuration (sets things up automatically)
- Embedded server (Tomcat runs inside your app, no separate server needed)
- Starter dependencies (pre-packaged groups of libraries)

You don't need XML configuration files. Most configuration happens through annotations and application.properties file.

---

## What is Maven / What it does with Spring Boot

Maven is a build tool and dependency manager for Java projects.

**What Maven does:**

- Downloads libraries (dependencies) your project needs
- Compiles your code
- Runs tests
- Packages your application into a JAR file

**With Spring Boot:** Maven uses the `pom.xml` file to manage everything. Spring Boot provides "starter" dependencies - these are pre-configured sets of libraries. For example, `spring-boot-starter-web` includes everything needed for a web application (Tomcat, Spring MVC, JSON handling, etc.).

---

## Domain of Project

Full example:

```
com.example.demo
```

**Breaking it down:**

- **`com`**: Top-level domain, usually your organization type (com for commercial, org for organization, etc.)
    
- **`example`**: Your company/organization name or your personal identifier
    
- **`demo`**: Your project/application name
    

This reverse domain naming prevents conflicts between different projects. If two developers create a "demo" project, `com.example.demo` and `org.another.demo` won't clash.

---

## Difference between `mvn clean spring-boot:run` and `mvn spring-boot:run`

- **`mvn spring-boot:run`**: Compiles and runs your application directly. Uses existing compiled files if they exist.
    
- **`mvn clean spring-boot:run`**: The `clean` part deletes the `target/` folder (where compiled files live) first, then compiles everything fresh and runs. Use this when you want to ensure no old compiled files cause issues.
    

**When to use clean:** After major changes, when something feels "stuck", or when builds act weird.

---

