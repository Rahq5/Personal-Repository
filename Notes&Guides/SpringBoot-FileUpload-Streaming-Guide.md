### Summary

This guide covers building a file upload service in Spring Boot using InputStream streaming instead of getBytes() to handle large files without RAM suffocation. The implementation follows the Controller → Service layer architecture with proper security sanitization and configuration for scalable uploads limited only by disk space.

### Steps Overview

- Configure Spring Boot multipart settings in application.properties
- Create Service package and FileService class
- Implement upload method using InputStream streaming
- Add filename sanitization for security
- Update Controller to use the service
- Test with small and large files to verify streaming works
- Verify files appear in files_Database folder
- (optional) find a way to monitor data being transmitted to verify:
	- memory usage (what is the applicable amount of data bandwidth for your 4GB RAM)
	- other reasons i didnt wrote cuz am running of time 

---

### Configuration Setup (application.properties)

Add these settings to control multipart behavior and upload limits:

**Required settings:**
- Remove file size limits (set to -1)
- Remove request size limits (set to -1)
- Set file-size-threshold to 2KB (forces disk streaming for files > 2KB)
- Define upload directory path (e.g., files_Database)

> **Threshold**: The point where Spring's behavior changes — files smaller than this stay in RAM, larger ones go directly to disk temp storage

**Injection pattern:** Use @Value annotation to read the upload directory path from properties into your service class

---

### Package Structure

Create new package under `com.solidsudogear.ccsp`:
- Service (new package at same level as Controller)

Place your FileService class inside this package.

**Standard Spring Boot layers:**
```
Controller (HTTP layer) → Service (business logic) → Repository (data access, future)
```

---

### Service Layer Implementation

#### Class Setup

**Annotations needed:**
- @Service (marks this as a Spring-managed service bean)

**Fields:**
- Upload directory path (injected from configuration using @Value)

> **Inject**: Spring automatically provides configuration values to class fields at runtime

#### Methods to Implement

**Main method: uploadFile(MultipartFile file, String name)**

Task: 
1. sanitize file name
2. starts uploading 

Responsibilities:
- Sanitize the filename (extract safe name, no path traversal)
- Construct full path (uploadDir + sanitized filename)
- Create directory if not exists
- Open InputStream from MultipartFile
- Stream to disk using Files.copy()
- Return the saved filename or success message

**Helper method: sanitizeFileName(String name)**

Responsibilities:
- Use Paths.get(name).getFileName().toString()
- Strips all path components (removes ../, ../../, etc.)
- Returns only the safe filename

**Directory creation:**
- Use Files.createDirectories() not mkdir()
- This creates parent directories if needed
- Does not throw exception if directory already exists

**Streaming operation:**
- Use try-with-resources for InputStream
- Use Files.copy(InputStream, Path, StandardCopyOption.REPLACE_EXISTING)
- This streams data in 8KB chunks (RAM stays constant)

> **Try-with-resources**: Java syntax that automatically closes resources (like InputStream) when done, even if exceptions occur

---

### Controller Updates

#### Responsibilities

Controller should be **thin** — no business logic here:
- Receive MultipartFile from request
- Receive filename from request (or extract from file.getOriginalFilename())
- Call service.uploadFile()
- Return appropriate HTTP response

**Annotations:**
- @PostMapping for upload endpoint
- @RequestParam("file") for receiving file
- @RequestParam("name") for receiving filename

**Dependency injection:**
- Use constructor injection (preferred) or @Autowired
- Inject FileService into controller

**Response handling:**
- Return 200 OK on success
- Return 400 Bad Request for invalid input
- Return 500 Internal Server Error for IOExceptions

---

### Testing Checklist

#### Step-by-step verification:

1. Start the Spring Boot application
2. Use Postman or curl to send POST request with multipart file
3. Check project root for files_Database folder (should be auto-created)
4. Verify file exists inside files_Database/
5. Compare file size with original (ensure no corruption)
6. Test with small file first (1MB)
7. Test with large file (100MB or 1GB MP4) — RAM should stay low

**Example curl command:**
```bash
curl -X POST http://localhost:8080/upload \
  -F "file=@/path/to/video.mp4" \
  -F "name=test-video.mp4"
```

**Monitor RAM:** Use `htop` or `top` while uploading large file — RAM usage should not spike

---

### Common Mistakes to Avoid

❌ **Using file.getBytes()** — This loads entire file into RAM, defeats streaming purpose

❌ **Hardcoding paths** — Use configuration injection, not "files_Database" literals everywhere

❌ **Not checking directory existence** — Always use Files.createDirectories() before writing

❌ **Not sanitizing filename** — Security vulnerability, allows path traversal attacks

❌ **Forgetting try-with-resources** — InputStream must be closed, use try-with-resources syntax

❌ **Using java.io.File API** — Old API, use java.nio.file.* instead

❌ **Testing only with small files** — Large files reveal streaming issues, test with 1GB+

---

### Key Java Classes Reference

**Package: java.nio.file**

**Paths.get(String... parts)**
- Constructs file paths from string segments
- Example: Paths.get("/home", "user", "file.txt")

**Files.copy(InputStream, Path, CopyOption...)**
- Streams data from InputStream to file on disk
- Use StandardCopyOption.REPLACE_EXISTING to overwrite

**Files.createDirectories(Path)**
- Creates directory and all parent directories
- Safe to call even if directory exists

**Path interface**
- Represents file system path
- Methods: getFileName(), normalize(), resolve()

**StandardCopyOption enum**
- REPLACE_EXISTING — overwrites file if exists
- COPY_ATTRIBUTES — preserves file metadata

> **Streaming**: Reading/writing data in small chunks (8KB buffers) instead of loading entire file into memory

---

### Security: Path Traversal Prevention

**Attack scenario:**
User sends: `name=../../../../etc/passwd`

**Without sanitization:**
Path resolves to: `/etc/passwd` (system file overwrite!)

**With sanitization:**
getFileName() extracts only: `passwd`
Final path: `files_Database/passwd` (safe, contained)

**Additional security layers:**
- Extension whitelist (only allow .jpg, .png, .mp4, .pdf)
- UUID filename generation (removes user control completely)
- Path verification (ensure final path is inside upload directory)

> **Path Traversal**: Security exploit using `../` to navigate to parent directories and write files outside intended location

---

### Flow Diagram

```
HTTP POST Request (multipart/form-data)
         ↓
    Controller (@PostMapping)
         ↓
    Receive MultipartFile + name
         ↓
    Inject FileService
         ↓
    Call service.uploadFile(file, name)
         ↓
    Service Layer
         ↓
    Sanitize filename (remove ../)
         ↓
    Construct Path (uploadDir + safeName)
         ↓
    Create directory if not exists
         ↓
    Open InputStream from MultipartFile
         ↓
    Files.copy() — Stream in 8KB chunks
         ↓
    Data flows: Network → RAM buffer (8KB) → Disk
         ↓
    Close InputStream (auto via try-with-resources)
         ↓
    Return success message
         ↓
    Controller returns HTTP 200 OK
         ↓
    File saved in files_Database/
```

---

### Key Takeaways

**Why Streaming?**
- RAM usage constant at buffer size (8KB) regardless of file size
- Can upload 100GB files without RAM suffocation
- Only disk space is the limit

**Why Service Layer?**
- Separation of concerns (HTTP vs business logic)
- Reusable code (can call from multiple controllers)
- Easier to test (mock service in tests)

**Why Sanitization?**
- Prevents path traversal attacks
- User cannot overwrite system files
- Ensures files stay in designated directory

**Configuration over Hardcoding:**
- Upload path in properties file
- Easy to change for different environments
- No code changes needed for deployment

---

### When You Encounter Issues

**NullPointerException**
- Check @Autowired or constructor injection
- Verify configuration key matches properties file

**FileAlreadyExistsException**
- Add StandardCopyOption.REPLACE_EXISTING

**AccessDeniedException**
- Check Linux permissions on files_Database directory
- Ensure user running Spring Boot has write permission

**OutOfMemoryError**
- You're using getBytes() somewhere — switch to InputStream

**File not appearing**
- Check if directory path is correct
- Verify Files.createDirectories() was called
- Check application logs for IOExceptions

---

🎉 You've covered it all, wanna go deeper? 🎉
