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
