# MVC Part
## What is MVC Architecture

**MVC** stands for Model-View-Controller. It's a **design pattern** (not monolithic - that's a deployment style) that separates your application into three parts:

- **Model**: Represents data and business logic (your entities, database operations)
- **View**: What the user sees (HTML pages, JSON responses)
- **Controller**: Handles requests and decides what to do (receives request, calls model, returns view)

**Monolithic vs MVC:** Monolithic describes how you deploy (one big application vs microservices). MVC describes how you organize code inside that application. A monolithic app can use MVC pattern.

## Request Journey in MVC

- Browser sends request to `localhost:8080/users`
- Request hits **DispatcherServlet** (Spring's front controller)
- DispatcherServlet looks at URL and finds matching **Controller** (the `@Controller` or `@RestController` class with `@GetMapping("/users")`)
- Controller method executes, calls **Model** layer (Service classes, Repository classes) to fetch data from database
- Model returns data to Controller
- Controller processes data and decides which **View** to return - either returns view name (like "users.html" for Thymeleaf template) or returns data directly (for `@RestController`, Spring converts object to JSON)
- DispatcherServlet sends the View back as HTTP response to browser

Quick flow: **Request → DispatcherServlet → Controller → Model (Service/Repository) → Controller → View → Response**

## How controller, model, view works together

as we now previously that view act as visuals elements of web page (e.g showing metadata), model is the actual data (real data), controller glues view and model.

so in action :
- **Controller** handles every web request
- when a user makes a request, the controller maps the request to a handler method (e.g GetGrades , GetEmployees ) the fetches data
-  fetched data stored in **model**
-  the **View** without data is useless , so the **controller** sends the **model** to the **view**
- now View has data and became useful
- then handler method returns the **view**

