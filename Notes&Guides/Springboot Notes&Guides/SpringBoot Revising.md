
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

# MVC Part
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
## Field Validation
### Define 
in this section u gonna learn how to show bad input messages like in case of user entered a non-valid or empty input

so the process of validating fields:
1. Place annotation that needs validating
```java
// in model class 
public person{
	
	private String name;
	@Min(18)   // annotation to validate that minimum number is 18
	private int Age;
}
```
2. pass a bad input (as a user)
3. handler  method validates field 
	- spring makes a container to save object's data , while validating the container if any things violated the  validations ,will be passed to `BindingResult`
4. Binding result carries the result of the validation, if negative result (something violated) it will force user to stay in the form (same page and not leaving it)
5. Thymeleaf catches the error message from binding result and display them to user

some annotations you may need :

| Annotation | Description                 |
| ---------- | --------------------------- |
| `@Max()`   | cannot be more than maximum |
| `@Min()`   | cannot be less than minimum |
| ..         | ..                          |
### How to handle validations (code)
so steps we gonna do is:
1. apply annotations on the model class 
2. add `@Valid` next  to parameter model in handler method
3. Check `Binding result` for validating result

First step: apply validations 
```java 
  
public class Grade {
@NotBlank(message = "name canno be blank")
private String name;

private String subject;

@Min(60)
private String score;
private String id;
```

second Step : add `@Valid` next to model class in handler method parameters 
```java
@PostMapping("/grades")
public String getGrades(@Valid Grade grade) { // <------- here
model.addAttribute("grades", grade);
return "grades";
}
```
This step  will apply  the action of "validating" 

Third Step: apply binding results to check validation results
**important**: 
- adding BindingResult Object MUST be next to validated object in handler method parameter; otherwise Spring will error
- in this example am showing multiple things:
	- where to put the binding result in parameters
	- how to check for violations 
	- how to treat violations (by staying in the same page as example)
	- how to send binding result error message to thymeleaf
```Java
@PostMapping("/grades")
public String getGrades(@Valid Grade grade, BindingResult result) { // where to put Binding result in parameters , next to POJO class passed
	
	System.out.println("has error: "+result.hasErrors()) // check for errors
	
	if(result.hasErrors()) return "form"; // sending back the same page if error occured , this also let thymeleaf catches error message
	
	int index = getGradeIndex(grade.getID());
	if(index == Constants.Not_Found){
		StudentGrades.add(grade);
	}else{
		studentGrades.set(index,grade)
	}
	return "redirect:/grades"
}
```




### How to make Custom Constraints
in this part you will make your own custom constraint. Let's say you have a grade submission app and you want to validate grade to be (A+,A,B+ .... F), anything other than that will be assumed as bad input 

Process:
1. Define annotation using `@interface`
2. Connect annotation to validation logic 

**First Step: Defining annotation**
Steps:
1. Make a new class , name your annotation and put `@interface`
2. for every annotation define Target , target means where does this annotation be applied
3. Define Retention on runtime , cuz we want the validation to retain on runtime

Code:
```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface score{
// 
}
```

Now to define the logic of the validation you have to add an extra field and file called `@Constraint`
that will be in next step
```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Constraint(ValidatedBy = ScoreValidator.class)
public @interface score{
// 
}
```

---
**Second Step: Connecting annotation to validation logic**
previously you made an interface file that points to `ScoreValidator` class

in the validator logic class you have to define:
1. implement `ConstraintValidator` interface
2. Annotation Type (Annotation name `score`)
3. Validation Type (What datatype you validating `String`)
4. Add a method called `isValid`, this is mandatory due to using an interface (the point that u must implement the methods declared in the interface)
Code:
```java
// ScoreValidator.java

public class ScoreValidator implements ConstraintValidator<Score,String>{

	@Override
	public boolean isValid(String Value, ConstraintValidatorContext context){
		//here's the heart of the validtion 
		//this is where you put your own constrints by your hands
	}
}
```

Full Code example:
```java

public class ScoreValidator implements ConstraintValidator<Score,String>{

	List<String> scores = new Arraylist<>(Arrays.asList(
	"A+","A","B+",
	"B","C+","C",
	"D+","D","F"))
	@Override
	public boolean isValid(String Value, ConstraintValidatorContext context){
	// logic to search if the value passed matches with any element of 
	// real scores 
	
		for (String str : scores){
			if(Value.equals(str)) return True ;
		}
		return False ;
	}
}
```


### Code must written inside interface of validator (IMPORTANT)
this step is important to mention
according to Spring boot documentation you have to navigate to your Score interface and put this code below.
```
    String message() default "Invalid Data";
	Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
```
as example
```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Constraint(ValidatedBy = ScoreValidator.class)
public @interface score{

	String message() default "Invalid Data";
	Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
    
}
```


# Three-Layered Architecture in Spring Boot

## What is Layered Architecture?

Layered architecture is a design pattern that organizes code into separate horizontal layers, where each layer has a specific responsibility and communicates only with adjacent layers.

>Note: all prevoius skills learnt in MVC part are also can be applied here but the reason we studied MVC is for fully educating reason , while layerd arch is way better in real working fields

## The Three Layers

 1. Presentation Layer
**Components:**
- Controllers
- Model and View
**Behavior:**
- Handles HTTP requests and responses
- Manages user interaction
- Validates input data
- Returns formatted responses

2. Business Layer
**Components:**
- Service part only
**Behavior:**
- Executes business logic
- Applies processing rules
- Transforms data between layers
- Manages transactions

3. Data Access Layer
**Components:**
- All CRUD operations (Create, Read, Update, Delete)
**Behavior:**
- Communicates with database
- Executes queries
- Maps data to objects
- Handles persistence

Flow Pattern

```
Controller (Presentation) → Service (Business) → Repository (Data Access) → Database
```

## Beans and dependency injection
### Introduction Beans part
in this section you will learn about beans and what they do
first let me show how a bean is created

Process:
1. any `@Component` (any annotaion) class is automatically discoverd by spring 
   so when u run your spring application , first thing spring does is `@ComponentScan` to scan all annotations in the project
2. spring makes an object of each `@Component` class it finds 
3. spring manages and stores all of his made object into spring container (applicationContext)

now we can basically define what a Bean is 
**Bean**: an object lives inside spring container

>Note: all annotations are same and makes spring able to see them what ever the annotation was (component,service,repo,etc...) they all serve the same purpose but the "look" different for developer ease of use.  
### Introduction Dependency Injection part & deeper vision
so one of the problems in building projects is creating an object from a class directly like this example 
```java
@Controller
class GradeConroller{
	Grade grades = new Grade(); // this is what i mean 
}
```
so this causes a problem called tight Coupling which lead to difficulty in unit testing and makes the project tight as fuck. so before defining D.I and tight coupling let's first define the process
Process:
1. annotate the class using `@component` (as example Service class)
2. Spring boot registers `@Component` class as a bean and store it inside the ApplicationContext (or spring container)
3. `@AutoWired` injects the bean into the dependent class
so you end up in two files like these:
```java
@Controller
class GradeController{
	@AutoWired
	Grade g ;
}
```
```java
@Component
class Grade{
//some body code
}
```
>Note : see the "Deeper vision" section for better understanding

Definitions:
- **Tight Coupling**:  Tight coupling happens when a class directly creates and owns its dependencies using the `new` keyword. This makes the class **locked** to specific implementations, making it hard to change, test, or reuse the class without modifying its code.
  
- **Dependency Injection** is a design pattern where a class receives its dependencies from an external source (like Spring Container) instead of creating them itself. This makes the class **flexible** because it doesn't control how its dependencies are created - someone else (Spring) provides them.
### Deeper vision on difference between "D.I" and "No D.I" (Optional)
in here i will define why injecting dependencies is a must step

 With D.I (Dependency Injection):
```
Spring Container:
┌─────────────┐      ┌─────────────┐
│ Controller  │      │   Grade     │
│             │──────│             │
│ (can swap)  │ link │             │
└─────────────┘      └─────────────┘
      ↑                     ↑
      └─────────────────────┘
   Both managed by Spring
   Controller uses Grade through a link
   You can change the link to point to FakeGrade for testing
```
The Controller doesn't contain Grade inside it. It just has a **reference/link** to Grade. Spring can change where this link points.

---
Without D.I:
```
Spring Container:
┌─────────────┐      ┌─────────────┐
│ Controller  │      │   Grade     │ (unused, wasted)
│  ┌───────┐  │      └─────────────┘
│  │ Grade │  │ 
│  │(new)  │  │  Grade is INSIDE Controller
│  └───────┘  │  Controller created it with "new"
└─────────────┘  Can't replace it for testing
      
Controller is LOCKED to this Grade object
```
Here, the Controller **owns** the Grade object inside it. It's not a link - it's the actual object built into the Controller.



### how to use @Bean
#### defining 
**Define:**
spring when runs he takes all classes noted with annotations and put them in his container and each class with an annotation is a **bean**

**Using Annotations:**
all annotations like `@service, @Repository, @controller` are all is a derived from `@Component` and they serve the same purpose we defined in beans defination, unless some annotations have slight different behavior like `@Controller , @RestConstroller`, the reason why are different is since they serve the same purpose is basically to make it easier for developer to use instead of filling the whole project with `@Component` which make it confusing a bit .

so we conclude that all annotations can be regsiterd as beans with two ways:
1. @Component and it's derivatives (@Service , @Repository)
2. @Bean

**UseCase of using @Bean:**
- **problem**: 
	-  Scenario: when u try to test your programs using logger library (log4j) first thing you do is place a component annotation above your controller class and that's ok and nothing is wrong here. but when coming to registering logger class into spring container (assuming you editted the class) you CANT put a component annotation above class like incoming example (see block num1) that will make spring make a different object instead of taking logger class from `log4j` (it will take yours like if it was any other class) and that's not good

- **Solution:** 
	- basically , any class belongs to your project (gradeController , Grade , GradeService , etc..) this will be annotated with @component , and any class from other codebase like libraries in our case , you should annotate it with @Bean (see block num2).

block num1 : placing component on everything
```java
@Component //this works and nothing wrong with it 
class GradeService {
	//some body code and it will work perfect
}


import log4j
@Component
class log4j {
	// cant edit here !
	// this will make spring take logger object from here instead of the original library
}
```

block num2: using bean and component correctly
```java
@Component //this works and nothing wrong with it 
class GradeService {
	@Autowired
	private Logger logger

	//some body code and it will work perfect
}

//---------------------------------------------------------------

import log4j
@Configurations
class Logconfig {
	@Bean
	public Logger logger(){
	
	} 
	//this will work fine since u annotated with bean
	// spring can accept edits and take his object of logger class from library with your edits without losing or causing problems
}
```

another full example:
```java
//file LogCongifg.java

@Configuration
public class LogConfig{
	@Bean //this loads logger into spring's container
	public Logger gradeLogger(){
		return LogMannager("GradeLogger");
	}
}

//--------------------
//file GradeService.java

@Service 
public class GradeService{

	@Autowired 
	private Logger logger;
	
	public void submitGrade(String stu, String grade){
		logger.info("newgrade"+stu+grade)
		//some code to approve that spring used the log4j class and not your local one
	}
}
```

**last thing !**
@Bean is placed above methods and not classes :)
i said it cuz i noticed it late 
####  Historical Bean Configuration: XML Approach (Optional)
this section tells a skill can be used if faced a some legacy spring boot systems especially on defining beans back days. 

**Spring beans can be configured using XML files instead of annotations, but annotations are better for modern projects.**

**context step by step:**
**1. Historical context:**
- Old Spring projects used XML files to define beans
- Now we use annotations (`@Service`, `@Repository`, etc.)

**2. How XML configuration works:**
- Create `app-config.xml` file under resources folder
- Define beans using `<bean>` tags instead of annotations
- Each `<bean>` tag = one bean in Spring container

**Example comparison:**
```java
// Modern way (annotations)
@Service
public class GradeService { }

@Repository  
public class GradeRepository { }
```

XML (Old school)
```xml
<!-- Old way (XML) -->
<bean id="gradeService" class="com.example.GradeService"></bean>
<bean id="gradeRepository" class="com.example.GradeRepository"></bean>
```

**3. Why learn this?**
- You might work on old projects that use XML
- Helps you understand how Spring evolved

**4. The lesson's message:** "Use annotations in new projects, but know XML exists for legacy code"

**In one sentence:**
This lesson shows you the old XML way of creating beans so you understand Spring's history and can work with older codebases, but you should use annotations in modern projects.

#### Practical use case for @Bean
####
### Dependency Injection and Unit Testing 
#### Introduction 

- **Why Dependency Injection Matters for Testing**
- You can't use tightly coupled classes in service classes because it makes unit testing difficult or impossible
- This is why you learned Dependency Injection

- **How Unit Testing Works**
    - You create a dedicated test class for each class you want to test
    - Example: `GradeService` class gets `GradeServiceTest` class
    
- **The Problem: Spring Doesn't Run During Tests**
    - When running unit tests, Spring is not running
    - No Spring container, no dependency injection, no bean creation
    - Only your test class and the tested class exist
    - Challenge: how to handle classes marked with `@Autowired` when Spring isn't running to inject them
      
- **The Solution: Mock Objects**
    - You create mock objects (fake versions) of the injected dependencies inside your test class
    - Mock objects simulate the behavior of real dependencies
    - You control what they return
    - This allows you to test your class logic in isolation without needing actual databases or external services

#### Setting up Testing classes (@Mock and @InjectMock)
>Note: in this part i will only define injections in test class , no testing methods yet , testing methods will be in the second part

it's irresponsible to make code without testing , business class is the most classes with bugs so you have to test them. 
Our goal in this section is to make a unit test class for `GradeService`

**Process**
- make a class called `GradeServiceTest` that only tests `GradeService`
	- this class will test all methods of `GradeService` class
	  
- add `@RunWith` using `MokitoJUnitRunner` to run every unit test
	- class that run tests is called runner class
	- get the JUnit Dependency in the `pom.xml` file
	  
- use `@Mock` to create a mock of the repository ==(will explained later)==
	- add `@Mock` to the dependencies in the `gradeService` class to mimic the GradeRepo while having no logic in it 
	  
- use `@InjectMock` to create the object you want to test
	- injectMock will create an object of your tested class (`GradeService` in our case) then injects all `@Mock` inside of the GradeSerivce class 

**Coding Process and exapliaining**:
1. **First, here's the real `GradeService` class with dependencies:**
```java

public class GradeService {
    
    // Dependency that GradeService needs
    @Autowired
    private GradeRepository gradeRepository;
    
    // Method to get a grade by ID
    public Grade getGrade(Long id) {
        return gradeRepository.findById(id);
    }
    
    // Method to save a grade
    public void saveGrade(Grade grade) {
        gradeRepository.save(grade);
    }
}
```

2. **Create a test class called `GradeServiceTest`:**
```java
// This class will only test GradeService
// All methods of GradeService will be tested here
public class GradeServiceTest {
    
}
```

3. **Add `@RunWith` using `MockitoJUnitRunner` to run every unit test:**
```java
import org.junit.runner.RunWith;
import org.mockito.junit.MockitoJUnitRunner;

// This runner class will run all our unit tests
// It initializes Mockito annotations like @Mock and @InjectMocks
@RunWith(MockitoJUnitRunner.class)
public class GradeServiceTest {
    
}
```

> Note: You need this dependency in `pom.xml`:
```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <scope>test</scope>
</dependency>
```


4. **Use `@Mock` to create a mock of the repository:**
```java
@RunWith(MockitoJUnitRunner.class)
public class GradeServiceTest {
    
    // Creates a fake GradeRepository with no real logic
    // This mocks the dependency that GradeService needs
    // We will control what this mock returns in our tests
    @Mock
    private GradeRepository gradeRepository;
}
```


5. **Use `@InjectMocks` to create the object you want to test:**
```java
@RunWith(MockitoJUnitRunner.class)
public class GradeServiceTest {
    
    // Creates a fake GradeRepository
    @Mock
    private GradeRepository gradeRepository;
    
    // Creates a real GradeService object
    // Then automatically injects the mocked gradeRepository into it
    // Now gradeService has the fake repository inside it and is ready for testing
    @InjectMocks
    private GradeService gradeService;
}
```

---

6. **Full example (complete test class setup):**

```java
package com.ltp.gradesubmission.service;

import org.junit.runner.RunWith;
import org.mockito.junit.MockitoJUnitRunner;
import org.mockito.Mock;
import org.mockito.InjectMocks;
import com.ltp.gradesubmission.repository.GradeRepository;

@RunWith(MockitoJUnitRunner.class)
public class GradeServiceTest {
    
    @Mock
    private GradeRepository gradeRepository;
    
    @InjectMocks
    private GradeService gradeService;
    
    // Now you can write test methods here
    // Each test method will use the gradeService with the mocked repository
}
```

--- 
##### Explaining difference between @Mock and @Inject Mock

- **`@InjectMocks`** = acts like the Spring container
    - It takes all the `@Mock` objects
    - Injects them into the class you're testing
    - Just like Spring does with `@Autowired`
      
- **`@Mock`** = the injected dependency
    - Acts like a bean that would normally be injected by Spring
    - But it's a fake version you control

#### Setting up Testing Classes (testing methods)
in this section our goal is to proceed what we have done in previous section ( part1 )
and apply unit Testing on GradeService class.

**Process:**
- `@Test` tells JUnit to run the test 
- three steps to write any unit test:
	- **Arrange**: mock the data needed to carry out the unit test
	- **Act** : call the method that you want to test
	- **Assert**: Check if the method is behaving correctly 

now proceeding with the three steps one by one

**1.Arrange**
in this step i will create the testing method and mock the data to be tested 
>Note: the nice thing the mock does when told what data to return is readen like english 
>	as example: 
>	{**when** the service calls, **gradeReposiroty.getGrades()**, **then** it should **return** a **list** of **grades**}
>	becomes:
>	`when gradeRepository.getGrade() thenReturn(Grades.asList((PASS MOCK DATA HERE))`
```java
@Test
public void getGradesFromRepoTest(){
	when(gradeRespository.getGrades()).thenReturn(Arrays.asList(
		new Grade("Harry" , "Java" , "A++"),
		new Grade("ABDU" , "SQL" , "S++"),
		new Grade("bahia" , "Poerty" , "S++")
	))
}
```

**2.Act**
here you gonna test if tested class is actually able to retrieve grades from repo 
```java
@Test
public void getGradesFromRepoTest(){
	when(gradeRespository.getGrades()).thenReturn(Arrays.asList(
		new Grade("Harry" , "Java" , "A++"),
		new Grade("ABDU" , "SQL" , "S++"),
		new Grade("bahia" , "Poerty" , "S++")
	));
	
	//check if class is able to retrive grades
	List<Grade> result = gradeService.getGrades();
}
```

**3.Assert**
in this step you gonna notice that the testing will be done by your mind (you will get it) 
so the step is to use `AssertEquals` and put the expected data and result data. the expected data is the point where you put "from mind" test results and that's what i mean by "done by your mind".

syntax:
```
assertEquals(Expected_Output , Actual_Result)
```

```java
@Test
public void getGradesFromRepoTest(){
	when(gradeRespository.getGrades()).thenReturn(Arrays.asList(
		new Grade("Harry" , "Java" , "A++"),
		new Grade("ABDU" , "SQL" , "S++"),
		new Grade("bahia" , "Poerty" , "S++")
	));
	
	
	List<Grade> result = gradeService.getGrades();
	
	// here is the core testing point + look at previous syntax to understand it
	assertEquals("Harry" , result.get(0).getName());
	assertEquals("SQL" , result.get(1).getSubject());
	
}

```

final code :
```java
@RunWith(MockitoJUnitRunner.class)
public class GradeServiceTest {

    @Mock 
    private GradeRepository gradeRepository;
    
    @InjectMocks 
    GradeService gradeService;

    @Test
    public void getGradesFromRepoTest(){
       
        // ========== ARRANGE (SETUP PHASE) ==========
        // This is NOT the test yet, just preparation
        
        // Tell the MOCK: "when gradeRepository.getGrades() is called, return this fake data"
        // This gradeRepository is the MOCK (fake), not real database
        when(gradeRepository.getGrades()).thenReturn(Arrays.asList(
            new Grade("Harry","Java","A++"),
            new Grade("Rawi","Web","C+"),
            new Grade("Bahia","poetry","S")
        ));
        // Now the mock is ready with fake data
        
        
        // ========== ACT (TESTING PHASE) ==========
        // THIS is where real testing starts
        
        // Call the method you're testing: gradeService.getGrades()
        // What happens inside:
        //   1. gradeService.getGrades() runs
        //   2. Inside it, it calls gradeRepository.getGrades()
        //   3. But gradeRepository is MOCK, so it returns fake data from above
        //   4. gradeService returns that fake data
        List<Grade> grades = gradeService.getGrades();
        // Now 'grades' contains the fake data (Harry, Rawi, Bahia)
        
        
        // ========== ASSERT (VERIFICATION PHASE) ==========
        // Check if gradeService handled the data correctly
        
        // Check first grade's name is "Harry"
        assertEquals("Harry", grades.get(0).getName());
        
        // Check third grade's subject is "poetry" (not "S")
        // Note: getSubject() returns subject, not grade
        assertEquals("poetry", grades.get(2).getSubject());
        
        // If these match, test PASSES ✅
        // If not, test FAILS ❌
    }
}
```

to verify how many times the method was called:
```
verify(mock , times).method()
```

```java
verify(gradeRepository , times(1)).submitGrade();
```
1. Mock
2. times: how many times expected for the method to be called
3. method 


### Integration Testing

#### Intro to integration testing
as we learnt before that unit testing is meant to check every each component is working correctly as expected , while integration test maps the request and response life cycle.
for quick intro i will show differences between unit and integration testing

**Unit Testing**:
- Tests individual units (methods/classes) in isolation
- No Spring context needed , uses mocks instead of real dependencies
- Failed test = broken unit that needs fixing
  
**Integration Testing**:
- you will need spring context when integration testing 
  (which means spring will really run unlike unit testing)
- maps the entire request and response life cycle, more explaining....
	- the test starts with mocking a request to the programs , passes through view , service, database layers
	- last it will make assertions like 
		- Successful response?
		- correct view ?
		- correct model ?
		- and so on....
#### Integration Testing part 1 (injecting beans & AutoConfigureMockMvc)
navigating to `Test/GradeSubmissionApplicationTests` 
When you run a test class with `@SpringBootTest`, Spring loads the entire application context and creates all beans (components, services, controllers, etc.) in the container.

**Verifying beans are created:**
```java
@SpringBootTest
class GradeSubmissionApplicationTests {
	
	@Autowired 
	private GradeController controller;
	
	@Test
	void contextLoads() {
		assertNotNull(controller);  // Checks if controller bean exists
	}
}
```

##### MockMvc - Testing Without Real Server
`MockMvc` lets you test your web layer (controllers, endpoints) without deploying the application or starting an actual HTTP server. You simulate HTTP requests and verify responses.

To use `MockMvc`, add `@AutoConfigureMockMvc` which registers the `MockMvc` bean in the Spring container:
```java
@SpringBootTest
@AutoConfigureMockMvc  // Creates MockMvc bean
class GradeSubmissionApplicationTests {
	
	@Autowired 
	private GradeController controller;
	
	@Autowired
	private MockMvc mockMvc;  // Inject MockMvc
	
	@Test
	void contextLoads() {
		assertNotNull(mockMvc);     // Verify MockMvc exists
		assertNotNull(controller);   // Verify controller exists
	}	
}
```


#### Integration Testing part 2 (building mock methods for testing)
in this part we gonna write integration test but before that i will identify our requests which are :
- `/` + optional Id String
- `/grades`
- `/handlesubmit`

##### Process: mock and assert a web request 

before explaining , all the 3 tests have these parts:
**Flow:**
1. **Build** the request → `MockMvcRequestBuilders.get()`
2. **Mock/Execute** the request → `mockMvc.perform()`
3. **Assert** the response → `.andExpect()`
###### mocking `/` endpoint (get)
we will mock a request on `/` with an id
the following code is the same as we noted so far on `GradeSubmissionApplicationTests.java`
```java

@SpringBootTest
@AutoConfigureMockMvc  // Creates MockMvc bean
class GradeSubmissionApplicationTests {
	
	@Autowired 
	private GradeController controller;
	
	@Autowired
	private MockMvc mockMvc;  // Inject MockMvc
	
	@Test
	void contextLoads() {
		assertNotNull(mockMvc);     // Verify MockMvc exists
		assertNotNull(controller);   // Verify controller exists
	}	
	
	// vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv
	// this is the part were you gonna test "/" url
	@Test
	public void testShowGradeForm() throws Expception{
		RequestBuilder requestBuilder = MockMvcRequestBuilders.get("/?id=123");
		
		mockMvc.perform(request)
			.andExpect(status().is2xxSuccesfull())
			.andExpect(view().name("form"))
			.andExpect(model().attributeExists("grade"));	
		
	}
}

```

Explaining `testShowGradeForm`:
- `RequestBuilder requestBuilder = MockMvcRequestBuilders.get("/?id=123");` : Creates a GET request to the endpoint "/" with query parameter "id=123" (simulates: GET /?id=123)

- `mockMvc.perform(request)` : Executes the HTTP request against your controller without starting a real server
  
- `.andExpect(status().is2xxSuccessful())` : Verifies the HTTP response status code is in the 2xx range (200-299, means success)

- `.andExpect(view().name("form"))` : Verifies the controller returns a view named "form" (Spring will look for form.html template)

- `.andExpect(model().attributeExists("grade"))` : Verifies the Model object contains an attribute with key "grade" (checks if controller added "grade" to the model before returning the view)

###### mocking `/handleSubmit` endpoint (post)
the tested method in service class expects to pass a grade object 
so here am expecting if i passed a grade object i will have a 3xxRedirection response
```java
public void testSuccesfullSubmission() throws Expception{
		RequestBuilder requestBuilder = MockMvcRequestBuilders.post("/handleSubmit")
		.param("name" , "Harry")
		.param("subject" , "potions")
		.param("score","C-");
		
		mockMvc.perform(request)
			.andExpect(status().is3xxRedirection);
}
```
- `.post("/handleSubmit")` : Creates a POST request to "/handleSubmit" endpoint (instead of GET)
    
- `.param("name", "Harry")` : Adds form parameter "name" with value "Harry" to the request body (simulates form input field)
    
- `.andExpect(status().is3xxRedirection())` : Verifies response status is 3xx (redirect), means controller returned `redirect:/somewhere`

###### mocking `grade` endpoint 

here we expecting  a view returned of grades list 
which means the view:"grades" and model:"grades"
```java
@Test
	public void testGetGrades() throws Expception{
		RequestBuilder requestBuilder = MockMvcRequestBuilders.get("/grade");
		
		mockMvc.perform(request)
			.andExpect(status().is2xxSuccesfull())
			.andExpect(view().name("grades"))
			.andExpect(model().attributeExists("grades"));	
		
	}
```


###
## REST API

# Extra 
## break point Sessions
