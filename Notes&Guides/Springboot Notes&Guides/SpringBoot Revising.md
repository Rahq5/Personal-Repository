
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

### Introduction

our goal to learn RestAPI
**API:** is a mediator between consumer(user) and system

REST Guidlines:
- **Resource**: piece of data you can name
- **URI**: identifies the location of source like `localhost:8080/employess` 
- **Operations**: essential operation of REST which is (GET,PUT,DELETE,POST)
- **JSON**: mostly used presentation in REST 
- **Collection** : collection of multiple JSON pieces into one bundle , uses square brackets like here `[obj1 , obj2 , etc...]`

### @RequestParam vs @PathVariable
Both grab data from the URL, but from **different parts** of it.
**`@RequestParam`** reads after the `?` `localhost:8080/hi?name=Harry`

java
```java
@GetMapping("/hi")
public void handlerMethod(@RequestParam String name)
```
---

**`@PathVariable`** reads from the path itself `localhost:8080/hi/Harry`
java
```java
@GetMapping("/hi/{name}")
public void handlerMethod(@PathVariable String name)
```
**When to use which:**
- Pointing to a specific resource → `@PathVariable` → `/users/5`
- Filtering or searching → `@RequestParam` → `/users?role=admin`
> `@PathVariable` is preferred in REST because the URL should point directly to a resource.

### REST API: GET operations
simply gets data 

WorkFlow:
1. Client sends HTTP GET request to a URL like `/users`
2. Spring receives it and routes it to the matching `@GetMapping` method inside a `@RestController` class
3. Spring automatically converts the Java object to **JSON**
4. JSON is sent back to the client as the HTTP response

```java
@RestController
public class ContactController {

    @Autowired
    private ContactService contactService;

    @GetMapping("/contact/{id}")
    @ResponseBody
    public ResponseEntity<Contact> getContact(@PathVariable String id){
        Contact contact = contactService.getContact(id);
        return new ResponseEntity<>(contact , HttpStatus.Ok); // 200 OK + contact as JSON
    }
}
```
**Definitions and explaining**:
- **`@RestController`**: contains `@Controller` + `@ResponseBody` , marks the class as a REST handler
- **`@ResponseBody`**: serializes (converts) the return value into JSON automatically
- **`ResponseEntity<T>`**: represents the full HTTP response = **data** + **HTTP status code** (like 200, 404)
- **`@PathVariable`**: extracts the value from the URL , like `/contact/5` → `id = "5"`
- **`ResponseEntity.ok(contact)`**: wraps the contact object with HTTP status **200 OK** and returns it as the response


### REST API: POST Operations
simply sends data to be saved

WorkFlow:
1. Client sends HTTP POST request to a URL like `/contact` with a **JSON body** (the data)
2. Spring receives it and routes it to the matching `@PostMapping` method inside a `@RestController` class
3. Spring automatically converts the **JSON body** into a Java object
4. The method processes it and sends back an HTTP status code as the response

```java
@RestController
public class ContactController {

    @Autowired
    private ContactService contactService;

    @PostMapping("/contact")
    public ResponseEntity<HttpStatus> createContact(@RequestBody Contact contact){

        if(contact != null){
            System.out.println(contact.getName());
            contactService.saveContact(contact);
            return new ResponseEntity<>(HttpStatus.CREATED); // 201 CREATED
        } else {
            return new ResponseEntity<>(HttpStatus.NO_CONTENT); // 204 NO CONTENT
        }
    }
}
```

**Definitions and explaining**:
- **`@PostMapping("/contact")`**: listens for POST requests at `/contact` and routes them to this method
- **`@RequestBody`**: deserializes (converts) the incoming JSON from the request body into a Java object (`Contact`). **Without it**, Spring won't read the request body at all — the `contact` object will be `null` or empty, and your data is lost
- **`ResponseEntity<HttpStatus>`**: represents the full HTTP response — here it only carries a **status code**, no body data
- **`HttpStatus.CREATED`**: status code **201**, means the resource was successfully created
- **`HttpStatus.NO_CONTENT`**: status code **204**, means the request was received but there was nothing to process

### REST API: PUT Operations
Simply **updates** an existing contact by its ID

**WorkFlow:**
1. Client sends HTTP PUT request to a URL like `/contact/456` (the ID is in the URL)
2. Spring receives it and routes it to the matching `@PutMapping` method
3. Spring extracts `"456"` from the URL and injects it into `id` parameter
4. Spring converts the JSON body into a `Contact` Java object
5. The method passes both to the service to update, then fetches and returns the updated contact

```java
@PutMapping("/contact/{id}")
public ResponseEntity<Contact> updateContact(@PathVariable String id, @RequestBody Contact contact) {

    Contact updated = contactService.updateContact(id, contact);
    return new ResponseEntity<>(updated, HttpStatus.OK);
}
```

**Definitions and explaining:**

- `@PutMapping("/contact/{id}")`: listens for PUT requests at `/contact/{id}` — the `{id}` is a **URL placeholder** that captures whatever value the client puts there (e.g. `/contact/456` → id = `"456"`)
- `@PathVariable String id`: extracts the `{id}` value from the URL and binds it to this parameter. Without it, Spring won't know to pull the value out of the URL
- `@RequestBody Contact contact`: converts the incoming JSON body into a `Contact` Java object — same as POST
- `ResponseEntity<Contact>`: the response carries both a status code **and** a body (the updated contact object), Spring serializes it back to JSON automatically
- `HttpStatus.OK`: status code 200, means the request succeeded and the updated resource is returned in the body

### REST API: DELETE Operations
Simply **deletes** an existing contact by its ID

**WorkFlow:**
1. Client sends HTTP DELETE request to a URL like `/contact/456` (the ID is in the URL)
2. Spring receives it and routes it to the matching `@DeleteMapping` method
3. Spring extracts `"456"` from the URL and injects it into `id` parameter
4. The method passes the ID to the service to delete, then returns no body — just a status code
```java
@DeleteMapping("/contact/{id}")
public ResponseEntity<HttpStatus> deleteContact(@PathVariable String id) {
    contactService.deleteContact(id);
    return new ResponseEntity<HttpStatus>(HttpStatus.NO_CONTENT);
}
```

**Definitions and explaining:**

- `@DeleteMapping("/contact/{id}")`: listens for DELETE requests at `/contact/{id}` — the `{id}` is a **URL placeholder** that captures whatever value the client puts there (e.g. `/contact/456` → id = `"456"`)
- `@PathVariable String id`: extracts the `{id}` value from the URL and binds it to this parameter. Without it, Spring won't know to pull the value out of the URL
- `ResponseEntity<HttpStatus>`: the response carries **only** a status code with no body — there is nothing to return since the resource was deleted
- `HttpStatus.NO_CONTENT`: status code 204, means the request succeeded but there is intentionally no body in the response

### ExceptionHandling

#### Try-Catch and Difference Between Checked and Unchecked Exceptions

##### Try-Catch
A way to **handle errors gracefully** instead of crashing the app.

```java
try {
    // risky code
} catch (SomeException e) {
    // handle it
}
````

---

##### Checked vs Unchecked

**The only real difference:**

- **Checked** → method declares `throws` → Java **forces** you to handle it at compile time
- **Unchecked** → no declaration → Java stays silent, crashes at **runtime** if triggered

||Checked|Unchecked|
|---|---|---|
|Extends|`Exception`|`RuntimeException`|
|Java forces handling?|✅ Yes|❌ No|
|When it fails|Compile time|Runtime|
|Fault|Outside world (file, db, network)|Your code (bad index, null, bad id)|

##### Checked Exception

```java
// declared on the method — Java forces callers to handle it
private int findIndexById(String id) throws NoContactException {
    return list.stream()
        .findFirst()
        .orElseThrow(() -> new NoContactException());
}

// caller MUST catch it
try {
    Contact contact = contactService.getContactById(id);
    return new ResponseEntity<>(contact, HttpStatus.OK);
} catch (NoContactException e) {
    return new ResponseEntity<>(HttpStatus.NOT_FOUND); // 404
}
```

##### Unchecked Exception

```java
// extends RuntimeException — no throws declaration needed
public class ContactNotFoundException extends RuntimeException {
    public ContactNotFoundException(String id) {
        super("The id '" + id + "' does not exist");
    }
}

// just throw it — no try-catch required anywhere
.orElseThrow(() -> new ContactNotFoundException(id));
```

---

#### ErrorResponse

A simple class to structure the error body returned to the client.

```java
public class ErrorResponse {
    private String message;

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "dd-MM-yyyy hh:mm:ss")
    private LocalDateTime timestamp; // formatted timestamp in JSON response

    public ErrorResponse(String message) {
        this.message = message;
        this.timestamp = LocalDateTime.now();
    }
}
```

Response the client receives:

```json
{
  "message": "The id '123' does not exist",
  "timestamp": "04-03-2026 06:30:00"
}
```

---

#### @ControllerAdvice + @ExceptionHandler

Instead of try-catch in every controller method → define **one global handler** for the whole app.

Flow of how the exception flys from service to the exception being thrown 
```
[Service Class] orElseThrow(() -> new ContactNotFoundException(id))
        ↓
[Spring Internals] catches it (@ControllerAdvice is watching every request)
        ↓
[Spring Internals] looks for @ExceptionHandler(ContactNotFoundException.class)
        ↓
[ApplicationExceptionHandler] handleContactNotFoundException(ContactNotFoundException ex) gets called
        ↓
[ApplicationExceptionHandler] returns ResponseEntity with 404 + ErrorResponse body to client
```

code example
```java
@ControllerAdvice // global scope
public class ApplicationExceptionHandler {

    @ExceptionHandler(ContactNotFoundException.class) // catches this specific exception
    public ResponseEntity<Object> handleContactNotFoundException(ContactNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(ex.getMessage()); // build error body
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND); // 404 + body
    }
}
```
### Field Validation and Error Handling 


The goal here is to validate incoming request body fields _before_ they even reach the service layer. Spring gives us a clean way to do this using annotations + a global exception handler that catches validation failures and returns structured error responses.

---

#### Step 1 — Add the Validation Dependency

In `pom.xml`, add:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

This pulls in **Jakarta Bean Validation** (formerly Javax) — the library that powers all the constraint annotations like `@NotBlank`, `@NotNull`, etc.

---

#### Step 2 — Apply Constraints on the Model

On the fields you want to protect, annotate them with validation constraints:

```java
@NotBlank(message = "Name cannot be blank")
private String name;

@NotBlank(message = "Number cannot be blank")
private String phoneNumber;
```

> `@NotBlank` — rejects `null`, empty strings `""`, and whitespace-only strings like `" "`. It's stricter than `@NotNull`.

---

#### Step 3 — Trigger Validation in the Controller

`@Valid` tells Spring: _"before you pass this object to my method, validate it against its constraints."_

```java
@PostMapping("/contact")
public ResponseEntity<Contact> addContact(@RequestBody @Valid ContactRequest request) {
    // only reaches here if all fields passed validation
}
```

Without `@Valid`, the annotations on the model do nothing at runtime.

---

#### Step 4 — Handle Validation Failures Globally

When `@Valid` finds a violation, Spring throws `MethodArgumentNotValidException`. We catch it in the global handler.

The handler must **extend `ResponseEntityExceptionHandler`** — a Spring base class that already knows about common Spring MVC exceptions. We override one of its methods to customize the response:

```java
@RestControllerAdvice
public class ApplicationExceptionHandler extends ResponseEntityExceptionHandler {

		// vvvvvv the method signature is fixed (from extended class i guess) vvvvvv
    @Override
    protected ResponseEntity<Object> handleMethodArgumentNotValid(
            MethodArgumentNotValidException ex,
            HttpHeaders headers,
            HttpStatusCode status,
            WebRequest request) {

		// vvvv this part is where you handle your error (written by you and your own mind) vvvv
		
        // BindingResult holds the outcome of the validation process
        // getAllErrors() returns a List of every constraint that was violated
        for (ObjectError error : ex.getBindingResult().getAllErrors()) {
            System.out.println(error.getDefaultMessage()); // prints the message= value
        }

        return new ResponseEntity<>(HttpStatus.BAD_REQUEST); // 400
    }
}
```

> **`BindingResult`** — the object Spring uses internally to collect all validation errors after running `@Valid`. Think of it as a report card of what failed.

At this stage: the terminal prints the error messages, and the client gets a `400` with no body.

---

#### Step 5 — Return Structured Error Responses

Instead of printing to terminal and returning an empty body, we want to collect all messages and send them back as JSON.

**Refactor `ErrorResponse.java` — change `message` from `String` to `List<String>`:**

```java
public class ErrorResponse {
    private LocalDateTime timestamp;
    private List<String> message; // was String, now List

    public ErrorResponse(List<String> message) {
        this.message = message;
        this.timestamp = LocalDateTime.now();
    }
}
```

**Fix `handleContactNotFoundException`** — it used to pass a plain string, now wrap it:

```java
ErrorResponse error = new ErrorResponse(Arrays.asList(ex.getLocalizedMessage()));
```

> `Arrays.asList(...)` — wraps a single item into a `List` so the constructor still works.

**Update `handleMethodArgumentNotValid`** — collect all messages into a list, then return:

```java
@Override
protected ResponseEntity<Object> handleMethodArgumentNotValid(...) {

    List<String> errors = new ArrayList<>();

    for (ObjectError error : ex.getBindingResult().getAllErrors()) {
        errors.add(error.getDefaultMessage());
    }

    ErrorResponse errorResponse = new ErrorResponse(errors);
    return new ResponseEntity<>(errorResponse, HttpStatus.BAD_REQUEST);
}
```

---

#### What the Response Looks Like

When both fields are blank, Spring serializes the `List<String>` into a **JSON array**:

```json
{
  "timestamp": "2024-01-01T12:00:00",
  "message": [
    "Name cannot be blank",
    "Number cannot be blank"
  ]
}
```

Spring's Jackson serializer automatically maps `List<String>` → JSON array. No extra config needed.

---

#### The Full Picture

```
Request Body (invalid)
        ↓
@Valid in Controller   ← triggers validation
        ↓
MethodArgumentNotValidException thrown
        ↓
handleMethodArgumentNotValid() in GlobalHandler
        ↓
BindingResult.getAllErrors() → collect messages
        ↓
ErrorResponse(List<String>) → 400 BAD REQUEST
```

# SpringBoot Databases
## Spring Databases — H2 & JPA Introduction

> These notes cover how Spring Boot connects to databases, what H2 and JPA are, and how to wire them up with a REST API — the foundation before touching real databases like PostgreSQL.

**What's covered:**

- Why databases matter in Spring apps
- What H2 is and why we use it
- What JPA is and its role
- Configuring `application.properties`
- Accessing the H2 console via browser

---

#### What Are We Learning Here

When you build a REST API, your data has to live _somewhere_. Spring Boot gives you a clean way to connect to a database — and before dealing with a real database like PostgreSQL, developers use **H2** to move fast and test locally without any setup.

---

#### What is H2

> **H2** — a lightweight, in-memory relational database written in Java. It runs _inside_ your Spring app, no installation needed.

- It lives in RAM — data is gone when the app stops
- It comes bundled with Spring Boot (just add the dependency)
- It has a **browser-based console** so you can inspect tables visually
- Used for: dev, testing, and learning — not production

---

#### What is JPA

> **JPA (Java Persistence API)** — a _specification_ (a set of rules/interfaces) that defines how Java objects map to database tables.

> **Hibernate** — the most common _implementation_ of JPA. Spring Boot uses it under the hood by default.

Think of it this way:

- JPA = the contract ("here's how persistence should work")
- Hibernate = the actual worker that does the job
- Spring Data JPA = Spring's wrapper on top that makes it even easier (gives you `JpaRepository`)

You write Java classes → JPA/Hibernate turns them into SQL tables automatically.

---

#### REST API + H2 — How They Connect

```
HTTP Request
     ↓
Controller  (@RestController)
     ↓
Service     (business logic)
     ↓
Repository  (JpaRepository)
     ↓
JPA / Hibernate
     ↓
H2 In-Memory Database
```

Your `@Entity` classes define the table structure. Spring Boot creates the tables automatically on startup using H2.

---

#### application.properties Configuration

```properties
# Enable the H2 browser console
spring.h2.console.enabled=true

# The URL path to access the console
spring.h2.console.path=/h2

# The in-memory database name (you choose the name after "mem:")
spring.datasource.url=jdbc:h2:mem:grade-submission
```

**Line by line:**

- `console.enabled=true` → turns on the visual web UI for H2
- `console.path=/h2` → the route you hit in the browser: `localhost:8080/h2`
- `datasource.url` → tells Spring _which_ H2 database to connect to. `mem:` means in-memory. `grade-submission` is just the database name you gave it.

---

#### Logging Into H2 Console

1. Run your Spring Boot app
2. Open browser → `http://localhost:8080/h2`
3. You'll see a login form — paste your datasource URL in the **JDBC URL** field:

```
jdbc:h2:mem:grade-submission
```

4. Leave username as `sa`, password empty (defaults)
5. Click **Connect**

You're now inside the database — you can run SQL queries and see your tables.

---

#### Big Picture Flow

```
application.properties
        ↓
Spring Boot reads datasource config
        ↓
Hibernate creates tables from @Entity classes
        ↓
App starts → H2 database is live in memory
        ↓
REST API ←→ JpaRepository ←→ H2
        ↓
Browser → localhost:8080/h2 → console access
```

---

**Official docs:**

- H2 Console: https://www.h2database.com/html/tutorial.html#tutorial_starting_h2_console
- Spring Data JPA: https://docs.spring.io/spring-data/jpa/docs/current/reference/html/
- Spring Boot datasource config: https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.data

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

# Extra 

## Ambiguous Bean Problem
When two or more classes implement the same interface and both are marked as `@Component`, Spring doesn't know which one to inject when you use `@Autowired` — so it crashes on startup.
example:
```java
public interface Animal {
    String speak();
}

@Component
public class Dog implements Animal {
    public String speak() { return "Woof"; }
}

@Component
public class Cat implements Animal {
    public String speak() { return "Meow"; }
}

// Now somewhere else:
@Component
public class Person {
    @Autowired
    Animal animal; // ❌ Spring panics here
}
```

Spring sees **two beans** (`Dog` and `Cat`) that both can fill the `Animal` spot. It doesn't know which one to inject → it **crashes on startup**.

The error looks like:
`NoUniqueBeanDefinitionException: expected single matching bean but found 2: dog, cat`

**Solution**: basically is to go to each class and put a annotation called 
`@ConditionalOnProperty` then set the port , so each interface implementation class has it's own port like here:
```java

@Service
@ConditionalOnProperty(name = 'Server.port' , havingValue = "9090")
public class ContactsServiceImpl implements ContactService{

}

@Service
@ConditionalOnProperty(name = 'Server.port' , havingValue = "9080")
public class ContactsServiceImpl implements ContactService{

}
```


## OpenAPI Documentation — Contact API

These notes cover how to document a Spring Boot REST API using OpenAPI/Swagger annotations. Mastering this matters because undocumented APIs are unusable by other developers — documentation _is_ part of the product.

**What's covered:**

- What OpenAPI is and how Spring Boot auto-generates it
- Configuring the OpenAPI bean (title, version, description)
- Grouping and describing operations with `@Tag` and `@Operation`
- Documenting responses with `@ApiResponse`
- Specifying media types with `produces`

---

### What is OpenAPI?

> **OpenAPI** — A standard, machine-readable format that describes the capabilities of a REST API (paths, parameters, responses, schemas).

Spring Boot can auto-generate an OpenAPI spec for your app. Two auto-generated endpoints become available when the dependency is added:

|URL|What it gives you|
|---|---|
|`/v3/api-docs`|Raw JSON OpenAPI spec|
|`/swagger-ui/index.html`|Human-friendly interactive docs UI|

The spec detects **every operation** (controller method) automatically — no manual work needed just to get the base output.

---

### Task 1 — Dependency & Auto-generation

The OpenAPI dependency must be added to `pom.xml`. Unlike Spring Boot starters, this one **requires an explicit version** — the parent POM does not manage it.

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.x.x</version>
</dependency>
```

> **Parent POM** — The `spring-boot-starter-parent` that manages versions for all official Spring Boot starters. Third-party libs like springdoc are NOT covered by it.

Run the app → visit `/v3/api-docs` → you'll see a JSON spec with all your endpoints already detected.

---

### Task 2 & 3 — OpenAPI Bean Configuration

Create a config class that defines the API's identity.

```
src/main/java/
└── config/
    └── OpenApiConfig.java
```

```java
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class OpenApiConfig {

    @Bean
    public OpenAPI openAPI() {
        return new OpenAPI()
                .info(new Info()
                        .title("Contact API")
                        .version("1.0")
                        .description("API for managing contacts"));
    }
}
```

> **`@Configuration`** — Marks this class as a source of `@Bean` definitions. Spring scans it at startup and registers the beans into the application context.

> **`@Bean`** — Tells Spring: "call this method and register its return value as a managed bean." Here, it registers the `OpenAPI` object so springdoc picks it up to populate the Swagger UI header.

This updates the Swagger UI title, version, and description fields at the top of the page.

---

### Task 4 & 5 — `@Tag`: Grouping Operations

> **`@Tag`** — An annotation that assigns a name (and optionally a description) to group operations together in the Swagger UI.

Apply at the **class level** so every method in the controller inherits it:

```java
import io.swagger.v3.oas.annotations.tags.Tag;

@RestController
@Tag(name = "Contact Controller", description = "Create and retrieve contacts")
public class ContactController {
    // ...
}
```

Without `@Tag`, Spring auto-generates a tag from the class name (`contact-controller`). With it, you control the display name and description shown in the UI.

---

### Task 6 — `@Operation`: Describing Each Endpoint

> **`@Operation`** — Applied at the **method level** to give an endpoint a `summary` (short label) and `description` (longer explanation).

```java
import io.swagger.v3.oas.annotations.Operation;

@GetMapping("/contact/all")
@Operation(summary = "Retrieves contacts", description = "Provides a list of all contacts")
public List<Contact> getContacts() { ... }

@GetMapping("/contact/{id}")
@Operation(summary = "Get contact by Id", description = "Returns a contact based on an ID")
public Contact getContact(@PathVariable String id) { ... }

@PostMapping("/contact")
@Operation(summary = "Create Contact", description = "Creates a contact from the provided payload")
public Contact createContact(@RequestBody Contact contact) { ... }
```

---

### Task 7 — `@ApiResponse`: Documenting Responses

> **`@ApiResponse`** — Documents what the client should expect back for a given HTTP status code, including the response body schema.

#### `getContacts` — returns a JSON array

```java
import io.swagger.v3.oas.annotations.media.*;
import io.swagger.v3.oas.annotations.responses.ApiResponse;

@ApiResponse(
    responseCode = "200",
    description = "Successful retrieval of contacts",
    content = @Content(array = @ArraySchema(schema = @Schema(implementation = Contact.class)))
)
@GetMapping(value = "/contact/all", produces = MediaType.APPLICATION_JSON_VALUE)
public List<Contact> getContacts() { ... }
```

#### `getContact` — can return 200 or 404

```java
import io.swagger.v3.oas.annotations.responses.ApiResponses;

@ApiResponses(value = {
    @ApiResponse(responseCode = "200", description = "Successful retrieval of contact",
        content = @Content(schema = @Schema(implementation = Contact.class))),
    @ApiResponse(responseCode = "404", description = "Contact doesn't exist",
        content = @Content(schema = @Schema(implementation = ErrorResponse.class)))
})
@GetMapping(value = "/contact/{id}", produces = MediaType.APPLICATION_JSON_VALUE)
public Contact getContact(@PathVariable String id) { ... }
```

#### `createContact` — can return 201 or 400

```java
@ApiResponses(value = {
    @ApiResponse(responseCode = "201", description = "Successful creation of contact"),
    @ApiResponse(responseCode = "400", description = "Bad request: unsuccessful submission",
        content = @Content(schema = @Schema(implementation = ErrorResponse.class)))
})
@PostMapping(value = "/contact", produces = MediaType.APPLICATION_JSON_VALUE)
public Contact createContact(@RequestBody Contact contact) { ... }
```

> **`@ArraySchema`** — Used when the response body is a JSON array, not a single object. Wraps `@Schema` to tell Swagger the array element type.

> **`@Content`** — Specifies the media type and schema of the response/request body. Without it, Swagger shows no schema preview for that response.

---

### Task 8 — `produces` on `@GetMapping`

Adding `produces = MediaType.APPLICATION_JSON_VALUE` to the mapping annotation explicitly declares that the endpoint returns `application/json`. This is reflected in the Swagger UI and helps HTTP clients (and the spec) understand the content type.

```java
@GetMapping(value = "/contact/all", produces = MediaType.APPLICATION_JSON_VALUE)
```

> **`MediaType.APPLICATION_JSON_VALUE`** — A Spring constant equal to the string `"application/json"`. Declares the MIME type this endpoint produces.

---

### Big Picture Flow

```
pom.xml (springdoc dependency)
        |
        v
Spring Boot starts
        |
        v
springdoc scans @RestController methods
        |
        v
OpenApiConfig @Bean → sets title / version / description
        |
        v
@Tag on class → groups endpoints in Swagger UI
        |
        v
@Operation on method → adds summary + description per endpoint
        |
        v
@ApiResponse on method → documents each possible HTTP response + schema
        |
        v
produces = APPLICATION_JSON_VALUE → declares content type
        |
        v
/v3/api-docs  ─────────→ Raw JSON spec
/swagger-ui   ─────────→ Human-friendly interactive UI
```
## API Testing in Spring Boot — MockMVC & Integration Tests

> These notes cover how to write integration tests for a REST API in Spring Boot using MockMVC, JSONPath, and ObjectMapper. This matters because shipping untested endpoints is shipping broken trust — tests are your contract proof.

**What we'll cover:**

- Test lifecycle: `@BeforeEach` / `@AfterEach`
- Mocking HTTP requests with MockMVC
- Asserting HTTP status, content type, and JSON body
- JSONPath queries for single objects and arrays
- Sending a request body using ObjectMapper (POJO → JSON)
- Validating error cases (404, 400)

---

#### Test Lifecycle Setup

Before any test runs, you need data in the repository. After each test, you clean it out — so tests don't bleed into each other.

```java
@BeforeEach
void setup() {
    for (int i = 0; i < contacts.length; i++) {
        contactRepository.saveContact(contacts[i]);
    }
}

@AfterEach
void clear() {
    contactRepository.getContacts().clear();
}
```

> **`@BeforeEach`** — runs once before every single test method in the class.  
> **`@AfterEach`** — runs once after every single test method, used here to reset state.

---

#### Test 1 — GET a single contact by ID

Mock a `GET /contact/1` and assert the response.

```java
@Test
void getContactByIdTest() throws Exception {
    mockMvc.perform(get("/contact/1"))
        .andExpect(status().isOk())                          // 200
        .andExpect(content().contentType(MediaType.APPLICATION_JSON))
        .andExpect(jsonPath("$.name").value("Rayan"))
        .andExpect(jsonPath("$.phoneNumber").value("5334221234"));
}
```

> **JSONPath** — a query language for JSON, similar to XPath for XML.  
> **`$`** — refers to the root element of the JSON document.  
> **`$.name`** — grabs the `name` field at the root level.

So if the response body is:

```json
{
  "id": "1",
  "name": "Rayan",
  "phoneNumber": "5334221234"
}
```

Then `jsonPath("$.name").value("Rayan")` passes. ✅

---

#### Test 2 — GET all contacts

Mock a `GET /contact/all` and assert the array response.

```java
@Test
void getAllContactsTest() throws Exception {
    mockMvc.perform(get("/contact/all"))
        .andExpect(status().isOk())
        .andExpect(content().contentType(MediaType.APPLICATION_JSON))
        .andExpect(jsonPath("$.size()").value(3))
        .andExpect(jsonPath("$.[?(@.id == \"2\" && @.name == \"Tyrion Lannister\" && @.phoneNumber == \"4145433332\")]").exists());
}
```

> **`$.size()`** — returns the number of elements in a root-level JSON array.  
> **`$.[?(@.field == "value")]`** — filters array elements by condition. `@` refers to the current item being evaluated.  
> **`.exists()`** — asserts that at least one matching element was found.

The full expected JSON array:

```json
[
  { "id": "1", "name": "Jon Snow",          "phoneNumber": "6135342524" },
  { "id": "2", "name": "Tyrion Lannister",  "phoneNumber": "4145433332" },
  { "id": "3", "name": "The Hound",         "phoneNumber": "3452125631" }
]
```

---

#### Test 3 — GET a contact that doesn't exist (404)

Mock a `GET /contact/4` — ID 4 doesn't exist, so we expect a 404.

```java
@Test
void contactNotFoundTest() throws Exception {
    mockMvc.perform(get("/contact/4"))
        .andExpect(status().isNotFound()); // 404
}
```

> **404 Not Found** — the HTTP status code meaning the requested resource doesn't exist on the server.

This test verifies that your global exception handler is working. If you throw `PlaceNotFoundException` (or similar) and your `@ControllerAdvice` maps it to 404, this test proves it.

---

#### Test 4 — POST a valid contact (201 Created)

To send a request body, you need to serialize a Java object into JSON string. That's what `ObjectMapper` does.

```java
@Autowired
ObjectMapper objectMapper;

@Test
void validContactCreation() throws Exception {
    Contact newContact = new Contact(null, "Rayan", "5334221234");

    mockMvc.perform(post("/contact")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(newContact)))
        .andExpect(status().isCreated()); // 201
}
```

> **`ObjectMapper`** — a Spring/Jackson class that converts Java objects (POJOs) to JSON strings and vice versa.  
> **`writeValueAsString(obj)`** — serializes the Java object into a JSON string to be sent as the request body.  
> **`contentType(MediaType.APPLICATION_JSON)`** — tells the server "I'm sending JSON".  
> **201 Created** — HTTP status code meaning the resource was successfully created.

The JSON being sent:

```json
{
  "id": null,
  "name": "Rayan",
  "phoneNumber": "5334221234"
}
```

---

#### Test 5 — POST an invalid contact (400 Bad Request)

Send a contact with blank/whitespace-only fields. The server should reject it.

```java
@Test
void invalidContactCreation() throws Exception {
    Contact invalidContact = new Contact(null, "   ", "     ");

    mockMvc.perform(post("/contact")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(invalidContact)))
        .andExpect(status().isBadRequest()); // 400
}
```

> **400 Bad Request** — HTTP status code meaning the client sent something the server can't process because the data is malformed or invalid.

This test verifies that your `@Valid` / `@NotBlank` Bean Validation is wired and active on the controller. If validation fails → Spring returns 400 automatically.

---

#### HTTP Status Codes Quick Reference

|Code|Name|Meaning|
|---|---|---|
|200|OK|Request succeeded|
|201|Created|Resource was created successfully|
|400|Bad Request|Invalid input from client|
|404|Not Found|Resource doesn't exist|

---

#### Big Picture Flow

```
Test class
    │
    ├── @BeforeEach ──────────► populate repository with test data
    │
    ├── Test 1: GET /contact/1 ──► 200 + assert name & phone via JSONPath
    ├── Test 2: GET /contact/all ► 200 + assert size + assert specific doc exists
    ├── Test 3: GET /contact/4 ──► 404 (not found path)
    ├── Test 4: POST /contact ───► 201 (valid body via ObjectMapper)
    └── Test 5: POST /contact ───► 400 (blank fields, validation rejected)
    │
    └── @AfterEach ───────────► clear repository, reset state
```

---

**Official References:**

- MockMVC docs: https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/MockMvc.html
- JSONPath syntax: https://github.com/json-path/JsonPath
- Jackson ObjectMapper: https://fasterxml.github.io/jackson-databind/javadoc/2.7/com/fasterxml/jackson/databind/ObjectMapper.html
- Spring `@Valid` + Bean Validation: https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/requestbody.html
## break point Sessions

