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

## Client/Server Architecture (Quick & Deep)

### Basic Concept

A **server** is a program that waits and listens for requests. A **client** is a program that sends requests to the server.

### How Communication Works

**IP Address:** Identifies a computer on the network.

- `127.0.0.1` or `localhost` = your own machine
- `192.168.1.x` = local network addresses
- Public IPs = accessible from internet

**Port:** A number (0-65535) that identifies a specific application on that computer. Think of IP as a building address, port as an apartment number.

Common ports:

- `8080` = default Spring Boot
- `80` = HTTP
- `443` = HTTPS
- `3306` = MySQL

### The Request/Response Cycle

1. **Client initiates:** Browser (client) wants a webpage
2. **DNS Resolution:** Converts domain name to IP (if needed)
3. **TCP Connection:** Client connects to server's IP:Port
4. **HTTP Request:** Client sends request (GET, POST, etc.)
    
    ```
    GET /api/users HTTP/1.1Host: localhost:8080
    ```
    
5. **Server processes:** Spring Boot controller handles the request
6. **HTTP Response:** Server sends back data
    
    ```
    HTTP/1.1 200 OKContent-Type: application/json{"id": 1, "name": "John"}
    ```
    
7. **Connection closes** (or stays open for keep-alive)

### Spring Boot as Server

When you run Spring Boot:

- Embedded Tomcat starts
- Listens on `localhost:8080` by default
- Waits for HTTP requests
- Your `@RestController` or `@Controller` classes handle incoming requests

**Example flow:**

```
Browser (Client)           Spring Boot (Server)
     |                            |
     |---GET localhost:8080/----->|
     |                            | @GetMapping("/")
     |                            | method executes
     |<------HTML/JSON------------|
     |                            |
```

### Socket Level

Under HTTP, there's a **TCP socket** - a two-way communication channel.

**Server side:**

- ServerSocket binds to port 8080
- Listens for incoming connections
- Accepts connection = creates Socket for that client

**Client side:**

- Creates Socket
- Connects to server's IP:Port
- Sends/receives bytes through the socket

Spring Boot hides this complexity. Tomcat manages sockets, and you work with `HttpServletRequest` and `HttpServletResponse`.

### Multiple Clients

Server can handle multiple clients because:

1. Each client connection gets its own socket
2. Server uses threads (one thread per request by default in Tomcat)
3. Thread handles request → sends response → thread ends or returns to pool

---

## How to Add Dependency from Central Maven Repo into pom.xml

### Steps:

1. Go to https://mvnrepository.com/
2. Search for the library you need
3. Click on the library name
4. Click on the version you want
5. Copy the Maven dependency XML snippet
6. Open your `pom.xml` file in VSCode
7. Paste inside the `<dependencies>` section

### Example:

If you want to add MySQL connector:

```xml
<dependencies>
    <!-- Existing dependencies -->
    
    <!-- Add this -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.33</version>
    </dependency>
</dependencies>
```

After saving `pom.xml`, Maven automatically downloads the library. In VSCode, you might need to reload the Maven project (right-click pom.xml → Reload Project) or just wait a few seconds.

---

# MVC design
## What is MVC Architecture

**MVC** stands for Model-View-Controller. It's a **design pattern** (not monolithic - that's a deployment style) that separates your application into three parts:

- **Model**: Represents data and business logic (your entities, database operations)
- **View**: What the user sees (HTML pages, JSON responses)
- **Controller**: Handles requests and decides what to do (receives request, calls model, returns view)

**Monolithic vs MVC:** Monolithic describes how you deploy (one big application vs microservices). MVC describes how you organize code inside that application. A monolithic app can use MVC pattern.

## Request Journey in MVC

- Browser sends request to `localhost:8080/users`
- Request hits **DispatcherServlet** (Spring's front controller)
- DispatcherServlet looks at URL and finds matching **Controller** (the `@Controller` or `@RestController` class with `@GetMapping("/users")`)
- Controller method executes, calls **Model** layer (Service classes, Repository classes) to fetch data from database
- Model returns data to Controller
- Controller processes data and decides which **View** to return - either returns view name (like "users.html" for Thymeleaf template) or returns data directly (for `@RestController`, Spring converts object to JSON)
- DispatcherServlet sends the View back as HTTP response to browser

Quick flow: **Request → DispatcherServlet → Controller → Model (Service/Repository) → Controller → View → Response**

## How controller, model, view works together

as we now previously that view act as visuals elements of web page (e.g showing metadata), model is the actual data (real data), controller glues view and model.

so in action :
- **Controller** handles every web request
- when a user makes a request, the controller maps the request to a handler method (e.g GetGrades , GetEmployees ) the fetches data
-  fetched data stored in **model**
-  the **View** without data is useless , so the **controller** sends the **model** to the **view**
- now View has data and became useful
- then handler method returns the **view**

## Creating Controller (Code)
so starting with defining **Annotations** (@) 
which spring boot uses annotations as metadata tags to simplify application development by enabling auto-configuring 
such as : `@Controller  @GetMapping  @PostRequest  @RestController` etc..

but before going into building our first controller, i have to define what is and diffrence between Controller and RESTcontroller
- **RestController**: used to build RESTful web services that returns data directly in from of JSON or XML to client. so when it reaches to `return "hello"` it will return a hello on your page directly
- **Controller**: used in traditional spring MVC app to return **views** such as HTML files to the client, so when it reaches `return "hello"`  it will try to reach a resource in the project with the name "hello" (e.g Hello.html)

so starting to build out controller. all explanations will be in comments:
```
create a new folder under this path
src/main/java/com/example/demo/
```
Using RestController:
```java
@RestController  //Defines the controller is RESTful 
class GradeController {
	@GetMapping    // a get request annotation
	public String getGrades(){   //our method handler
	return "Hello user"    // returns message with "hello user"
	}
}
```
using Controller:
```java
@Controller  //Defines the controller  
class GradeController {
	@GetMapping    // a get request annotation
	public String getGrades(){   //our method handler
	return "Hello"    // returns a resource with the name file "hello" (e.g "Hello.html")
	}
}
```
## Creating Model (code)
the model as we explained previously is what holds the actual data.
so know am showing in-code how to fetch data , add it to model , merge to view then return it 

### Model Journey
1. **Spring receives request:** DispatcherServlet catches the incoming GET request to `/grades`
2. **Spring creates empty Model container:** Internally creates a `BindingAwareModelMap` object - an empty key-value container ready to hold data
3. **Spring calls handler method with empty Model:** Invokes your controller method `getGrades(Model model)` and passes the empty Model container as parameter
4. **Controller executes logic:** Your method runs, creates objects, and fills the Model using `model.addAttribute("key", value)` - Model now contains data
5. **Controller returns view name:** Since we're using `@Controller` (not `@RestController`), the method returns a String `"grades"` - this is the view name that tells Spring which template file to use
6. **ViewResolver locates template:** Spring's ViewResolver takes `"grades"` and finds the HTML file at `src/main/resources/templates/grades.html` (automatically adds `templates/` prefix and `.html` suffix)
7. **Model handed to Thymeleaf:** ViewResolver creates a ThymeleafView and passes both the Model (containing your data) and the HTML template to Thymeleaf engine
8. **Thymeleaf merges Model with View:** Thymeleaf processes the template, finds variable expressions like `${grade.name}`, retrieves data from Model, calls getter methods, and replaces expressions with actual values
9. **Final HTML sent to browser:** Thymeleaf returns the completed HTML with all data filled in, DispatcherServlet sends it as HTTP response to the browser, browser renders the page


Reviewing Steps:
1. the handler method has direct access to **Model** ; model is meant by `Repository + Service layers` which will be defined later
2. the handler method can use POJO (plain old java objects) to create data ; POJO is simple classes made by java that has attributes , constructors, setter and getters
3. the handler method can store the data in Model attribute

Starting with building code:

1. Accessing model:
```java 

@Controller
public class GradeController {

	@GetMapping("/hello")
	public String getMethodName(Model model) {  // here where model is accessed  
	return "Hello";
	}
}
```
2. making a simple POJO class (optional to see):
```java
class grade{
	private String name;   //attributes
	private String course;
	private String score;

	public grade(String name, String subject, string Score){   //constructor
		this.name=name;
		this.course=course;
		this.subject=subject
	}
	
	// some setters and getters and am lazy to write them 
}
```
then initialize an object to hold data and add it to model 
```java
@Controller
public class GradeController {

	@GetMapping("/grades")
	public String getMethodName(Model model) {  
		Grade grade = new Grade("harry","math","A+"); //used our POJO to be added to model
		model.addAttributes("grade",grade)    // pojo added to model 
		return "grades";
	}
}
```
- Note: a breakdown for the `model.addAttributes("grade",grade)` 
	- first parameter `"grade"`:  is the **key/name** you'll use in the HTML template to access this data
	- second parameter `grade`:  the **actual data/value** you're sending to the view \ or the object u just made
	  
**How it works:**
You're adding the `grade` object to the Model under the name `"grade"`, so in your `grades.html` (Thymeleaf template), you can access it like:
```html
<p th:text="${grade.name}"></p>      <!-- prints "harry" -->
<p th:text="${grade.subject}"></p>   <!-- prints "math" -->
<p th:text="${grade.score}"></p>     <!-- prints "A+" -->
```
The `${grade}` in HTML refers to the **key** you used (`"grade"`), not the variable name.

now u got ur data in the model and ready to be merged with the view!

## Thymleaf - Sending Model to view (code)
am skipping the point where i code the html file cuz am a backend engineer so i dont give a damn to html things , all am gonna do know is to tell how to merge model and view to get valuable web page 

**Thymeleaf**: combines your model data to the visual elements on the view using varible expressions
**Variable Expressions**: consists of Dollar sign, Curly brackets, Model attribute. example: `${age}`
How ?:
1. Thymeleaf receives your loaded model and your html file as example 
2. then thymeleaf looks for somethings like `${grade.name}` 
3. gets the `grade` object from the model
4. extracts name by calling `getName()` that found in the model class (the getter in the Grade class)
5. a name will returned let's say "Harry"
6. puts "Harry" The HTML 

Simple HTML where `grade.name` is called:
```html
<body>
    <p th:text="${grade.name}"></p>
</body>
```
The `th:text` attribute tells Thymeleaf: "replace the content of this tag with the value of `${grade.name}`"

result sent to browser:
```html
<body>
    <p>Harry</p>
</body>
```

>Important Note !!! : you gotta be carefull with misspelling cuz thymeleaf gonna hit error if something is different even if uppercase and lowercase letters. it's an annoying problem so be careful to match the exact same object names in both model and html 
# 
