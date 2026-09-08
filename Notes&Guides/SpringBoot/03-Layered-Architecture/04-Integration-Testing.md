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
