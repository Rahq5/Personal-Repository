# Three-Layered Architecture in Spring Boot

## What is Layered Architecture?

Layered architecture is a design pattern that organizes code into separate horizontal layers, where each layer has a specific responsibility and communicates only with adjacent layers.

>Note: all prevoius skills learnt in MVC part are also can be applied here but the reason we studied MVC is for fully educating reason , while layerd arch is way better in real working fields

## The Three Layers

 1. Presentation Layer
**Components:**
- Controllers
- Model and View
**Behavior:**
- Handles HTTP requests and responses
- Manages user interaction
- Validates input data
- Returns formatted responses

2. Business Layer
**Components:**
- Service part only
**Behavior:**
- Executes business logic
- Applies processing rules
- Transforms data between layers
- Manages transactions

3. Data Access Layer
**Components:**
- All CRUD operations (Create, Read, Update, Delete)
**Behavior:**
- Communicates with database
- Executes queries
- Maps data to objects
- Handles persistence

Flow Pattern

```
Controller (Presentation) → Service (Business) → Repository (Data Access) → Database
```

