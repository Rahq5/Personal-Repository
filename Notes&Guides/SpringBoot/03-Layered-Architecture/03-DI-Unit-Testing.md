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


