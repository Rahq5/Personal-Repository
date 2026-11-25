

## 1.1 MakingDirectoriesHelperMethod

**Describe:** in the mainController.java there's a part of logic that initiates the directory before starting the uploading logic. 

see code snippet:
```java
// this holds the file path in application.propierties
@Value("${upload.dir}")
private String uploadDir;


@PostMapping(value="/upload")
public ResponseEntity<?> uploadFile(@RequestParam("file")MultipartFile file){
	// this uploadDir hold the global path for uploads to be saved 
	// -----------------------vvvvvvvv------- 
	File directory = new File(uploadDir);

	// check if file exist
	if(!directory.exists()){
		//if not exist it makes it
		directory.mkdir();
	}

```

> Intel:
- 1. it's exactly in `src/main/java/com/solidsudogear/ccsp/Controllers/mainController.java`
  
- 2. if not find you can take the code right from the code snippet above


> Problem:

- 1.1. this should be a helper method
- 2.1. needs to be refactored


> Fix:

- 1.1. make a directory for it, and make other depending codes import it 


> Done:



----
# Help 
in case you forgot how to orgnize your notes just follow these 


> Problem:

- 1.1. [what went wrong or what needed doing]
- 2.1. [another problem]

> Fix:

- 1.1. [main fix — same number as the problem it solves]
- 1.1.A [alternate fix, if you tried more than one approach]
    - 1.1.A.1 [first step of that fix, if it's sequential]
    - 1.1.A.2 [second step]
- 1.1.B [another alternate fix, if needed]
  
- 2.1.[fixes to the second problem]

> message for claude: each fix should have it's corresponding problem number
