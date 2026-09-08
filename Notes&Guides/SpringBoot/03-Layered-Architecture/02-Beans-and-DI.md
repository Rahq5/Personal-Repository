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
