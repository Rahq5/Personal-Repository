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
