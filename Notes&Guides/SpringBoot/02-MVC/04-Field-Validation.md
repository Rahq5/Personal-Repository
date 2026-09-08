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


