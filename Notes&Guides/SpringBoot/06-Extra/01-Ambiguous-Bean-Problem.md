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


