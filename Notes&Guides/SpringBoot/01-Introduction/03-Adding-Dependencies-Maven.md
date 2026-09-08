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

