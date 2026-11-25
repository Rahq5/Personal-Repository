# Making a Simple Front End in Layered Architecture for a CCSP Project Using React

> **Topic:** Making a simple front end in layered architecture for a CCSP project using React, with focus on: (1) how the frontend talks to a Spring app using APIs, (2) where/how to place the React frontend in the project (tree structure), and (3) how to run everything together during testing/development.

---

## 1. What is `React Frontend Layering`

### What is it
A **simple front end in layered architecture for a CCSP project using React** is a browser-based user interface (UI) built with React that is organized into logical layers (for example: presentation/UI, application/state/use-case logic, and data-access/API communication) and connected to a Spring (Spring Boot) backend through HTTP APIs (typically REST over JSON).

In practice, this means:
- **React** renders views and handles user interaction in the browser.
- **Layering in the frontend** separates concerns so UI components do not directly mix rendering, business rules, and low-level HTTP details.
- **Spring Boot backend** exposes endpoints (e.g., `/api/...`) that the React app calls to read/write data or trigger server-side operations.

### What it helps in
- **Separation of concerns:** UI, client-side logic, and API communication are easier to reason about and change independently.
- **Maintainability:** A small but structured frontend is easier to extend as the CCSP project grows.
- **Team collaboration:** Clear boundaries make it easier to split work (UI, integration, backend).
- **Testability:** Layers (especially API/data-access and domain/use-case logic) can be tested with less coupling to UI components.
- **Integration clarity:** Makes the contract between frontend and Spring backend explicit (endpoints, request/response shapes, error handling).

### What it does
- Presents screens/forms/tables for users to interact with CCSP functionality.
- Sends HTTP requests (e.g., `GET`, `POST`, `PUT`, `DELETE`) to Spring Boot APIs.
- Handles responses (success data, loading states, validation/server errors).
- Applies client-side rules/workflows where appropriate (validation hints, state transitions, orchestration of API calls).
- Runs in development with a React dev server and in production as built static assets (often served by a web server or packaged/served alongside the backend, depending on deployment choice).

### What it offers
- A **structured frontend codebase** (not just components scattered with inline fetch calls).
- A **clear integration path** with Spring Boot:
  - Dev-time: proxy or CORS configuration so browser requests reach the backend.
  - Runtime: consistent API base paths (e.g., `/api`) and JSON contracts.
- A **project organization model** (separate `frontend/` and `backend/` folders, or frontend embedded/build-copied into Spring resources).
- A **repeatable run workflow** for local testing (start backend + frontend together, with proxying to avoid cross-origin issues).

---

## 2. `React Frontend Layering` Properties

These are design properties/features commonly associated with a simple layered React frontend integrated with a Spring backend:

1. **Layered separation of concerns (frontend-side)**
   - **Presentation/UI layer:** React components/pages, routing, rendering, user events.
   - **Application/use-case or state layer (optional but useful):** orchestrates user actions, manages UI/server state, calls domain/data services (often via hooks, state libraries, or query libraries).
   - **Domain/business rules layer (lightweight in simple apps):** entities/types, validation rules, transformations, use-case functions independent of frameworks where possible.
   - **Data access/infrastructure layer:** HTTP client wrappers (Fetch/Axios), API modules, DTO mapping, auth header handling, error normalization.

2. **API-driven integration boundary**
   - Frontend and Spring backend communicate through explicit HTTP endpoints and payload contracts (usually JSON).
   - The frontend treats the backend as an external service boundary even if both live in the same repository.

3. **Explicit transport and configuration concerns**
   - Base URL / proxy configuration (dev) to route `/api` calls to Spring Boot.
   - CORS configuration (backend) when frontend and backend run on different origins and no proxy is used.
   - Environment-specific configuration (dev/test/prod API URLs).

4. **Modular project placement**
   - Commonly organized as a monorepo-style layout with separate `frontend/` and `backend/` directories.
   - Can also embed frontend build output into Spring Boot static resources for unified serving in production.

5. **Independent dev servers with coordinated runtime**
   - React dev server (e.g., Vite/CRA dev server) provides fast UI feedback (HMR/dev reload).
   - Spring Boot runs the API/server logic on its own port.
   - A proxy or orchestration script/tool runs them together for local testing.

6. **Incremental complexity**
   - Starts simple (components + API module/service + basic layering) and can evolve toward stricter Clean Architecture/feature-based structures as the project grows.

---

## 3. `React Frontend Layering` Assumptions & Expectations

### Assumptions
- **Spring Boot backend exposes HTTP APIs** (REST endpoints) that the React app can consume.
- **Frontend and backend may run on different ports in development** (e.g., React on `5173`/`3000`, Spring Boot on `8080`), creating cross-origin requests unless proxied or CORS-enabled.
- **A clear API contract exists or will be defined** (paths, methods, request/response JSON, status/error behavior).
- **The CCSP project benefits from a simple but structured UI** rather than ad-hoc scripts or tightly coupled UI/network code.
- **Node.js/npm (or pnpm/yarn) and Java/Maven/Gradle** are available in the development environment.

### Expectations (outcomes/benefits)
- You can **build a React UI that calls Spring Boot APIs** reliably in local development.
- You can **organize frontend code in layers** so API calls and UI concerns are not tangled.
- You can **place the React app in a sensible project structure** (commonly alongside `backend/` in the same repo).
- You can **run backend and frontend together** during testing/development (via proxy + concurrent commands, separate terminals, or IDE run configs).
- You gain a foundation to later add authentication, better state/data fetching, validation, and production build/deployment integration.

---

## 4. How `React Frontend Layering` Works / Operate

This section focuses on the practical workflow: **API communication**, **project tree/placement**, and **running everything together**.

### 4.1 How the frontend talks to the Spring app using APIs

#### Core flow
1. **User interacts with React UI** (clicks button, submits form, loads page).
2. **UI/presentation layer triggers an action** (event handler or hook).
3. **Application/data-access layer calls an API function** (e.g., `userApi.getUsers()`), not raw HTTP logic scattered in many components.
4. **HTTP client sends request** to Spring Boot endpoint (e.g., `GET /api/users`) using `fetch` or `axios`.
5. **Spring controller/service processes request** and returns JSON + HTTP status.
6. **Frontend receives response**, updates state, and renders loading/success/error UI.

#### Typical layered split for API communication (simple form)
- **Presentation (components/pages):** calls hooks/functions like `loadUsers()`, displays data/errors.
- **Application/state (hooks/services):** coordinates calls, manages loading/error/data state.
- **Data access/infrastructure (API client):** contains actual `fetch`/`axios` calls, base path, headers, response parsing.

#### Example request patterns (conceptual)
- **Using Fetch API (browser built-in):**
  - `fetch('/api/users')` for same-origin/proxied dev setup
  - parse JSON with `response.json()`
  - handle non-2xx responses explicitly
- **Using Axios (library):**
  - `axios.get('/api/users')` / `axios.post('/api/users', payload)`
  - configure base URL/interceptors (optional) in one place

#### CORS and dev proxy (important in development)
When React dev server and Spring Boot run on different origins (different ports), browsers enforce CORS:
- **Option A: Dev proxy (common in local dev)**
  - Configure React dev server to forward `/api` requests to `http://localhost:8080`.
  - Browser talks to frontend origin; dev server proxies to backend, reducing CORS friction during development.
  - Examples:
    - CRA: `"proxy": "http://localhost:8080"` in frontend `package.json` (simple case)
    - Vite/custom setups: proxy config (e.g., `server.proxy` in `vite.config.*`) or `http-proxy-middleware` setup in some environments
- **Option B: Enable CORS in Spring Boot**
  - Add CORS mappings (globally or per controller), e.g. allow frontend origin like `http://localhost:5173` or `http://localhost:3000`, allowed methods/headers.
  - Useful when not proxying or when testing direct cross-origin calls.

#### Practical checklist for frontend ↔ Spring API integration
- Agree on **API base path** (e.g., `/api`).
- Use consistent **JSON request/response formats**.
- Handle **loading, success, empty, and error states** in UI.
- In dev, choose **proxy** or **CORS** (or both as needed) and verify in browser network/console.
- Keep API calls centralized (API module/client) to support layering and future changes.

### 4.2 Tree structure: where to put the React front end in the project

There is more than one valid structure. For a simple CCSP project, a clear and common choice is a **separate frontend folder** next to the Spring Boot backend (monorepo-style repository layout).

#### Recommended simple structure (separate frontend and backend)
```text
ccsp-project/
├── backend/                         # Spring Boot application
│   ├── pom.xml                      # or build.gradle
│   └── src/
│       ├── main/
│       │   ├── java/...             # controllers, services, repositories, etc.
│       │   └── resources/
│       │       ├── application.yml  # or application.properties
│       │       └── static/          # (optional) built frontend assets for prod
│       └── test/
│           └── java/...
├── frontend/                        # React application
│   ├── package.json
│   ├── vite.config.* / package proxy config
│   ├── public/                      # (if used by chosen tooling)
│   └── src/
│       ├── main.* / index.*         # app entry
│       ├── App.*                    # root component/routes
│       ├── pages/                   # presentation/screens
│       ├── components/              # reusable UI components
│       ├── features/ or modules/    # optional feature grouping
│       ├── services/ or api/        # data access layer (HTTP calls to Spring)
│       ├── hooks/                   # app/state orchestration hooks (optional)
│       ├── domain/ or models/       # types/entities/client-side rules (optional)
│       └── styles/                  # CSS/styling
└── README.md
```

#### How this maps to "layered architecture" on the frontend (simple interpretation)
- **Presentation layer:** `pages/`, `components/`
- **Application/state layer:** `hooks/`, feature orchestration logic (or state/query hooks)
- **Domain layer (lightweight):** `domain/` or `models/` (types, validation, mapping rules)
- **Data access/infrastructure layer:** `api/` or `services/` (Fetch/Axios wrappers, endpoint functions)

> Note: For a *simple* frontend, you do not need a heavy enterprise folder structure. The key is to avoid putting fetch logic, UI rendering, and business rules all in the same component.

#### Alternative structure (frontend embedded for unified packaging)
Some projects keep frontend source under the backend module and copy/build frontend assets into Spring Boot static resources (e.g., `src/main/resources/static`) for production packaging. This can simplify deployment as one artifact, but development tooling/proxy setup still needs to be handled carefully.

Example idea (conceptual):
```text
backend/
├── pom.xml
├── frontend-src/                    # React source (optional placement)
└── src/main/resources/static/       # built React files copied here for serving
```

For learning and local development clarity, the **separate `backend/` + `frontend/`** layout is usually easier.

### 4.3 How to run everything together on testing/development (firing servers up)

#### Goal
Run:
- **Spring Boot backend API server** (e.g., `http://localhost:8080`)
- **React dev server** (e.g., `http://localhost:5173` or `http://localhost:3000`)
- **A connection mechanism** between them (proxy preferred in dev, or CORS)

#### Method 1: Two terminals (simplest and explicit)
1. **Terminal A (backend):**
   - `cd backend`
   - Run Spring Boot (`mvn spring-boot:run` or `./mvnw spring-boot:run`, or Gradle equivalent)
2. **Terminal B (frontend):**
   - `cd frontend`
   - Install deps if needed (`npm install`)
   - Start dev server (`npm run dev` for Vite, or `npm start` for CRA)
3. **Ensure integration config:**
   - Frontend proxy points `/api` to backend (recommended), or backend allows frontend origin via CORS.
4. **Test flow:**
   - Open frontend URL in browser.
   - Trigger UI actions that call `/api/...`.
   - Verify requests/responses in browser DevTools Network tab and backend logs.

#### Method 2: One command via `concurrently` (frontend scripts orchestrate both)
In the frontend `package.json`, add a script that starts backend and frontend together (paths/commands adjusted to your project):
```json
{
  "scripts": {
    "start": "concurrently \"cd ../backend && mvn spring-boot:run\" \"npm run dev\""
  }
}
```
Then run:
- `cd frontend`
- `npm install` (and install `concurrently` if not present)
- `npm run start` (or your chosen script name)

This "fires up" both processes from one command, but you still need correct proxy/CORS configuration.

#### Method 3: IDE run configurations / tasks
- Create one run config for Spring Boot and one for the React dev server.
- Start both from the IDE (separately or as a compound/multi-run setup, depending on IDE support).
- Useful when you want integrated logs, debugging, and restart controls.

#### Development integration recommendation (for learning)
- Use **separate servers** in dev: React dev server + Spring Boot server.
- Use a **proxy** for `/api` calls during development to simplify cross-origin behavior.
- Keep backend base path consistent (e.g., all APIs under `/api`).
- Add **CORS config** in Spring Boot as a fallback/explicit policy when needed.

#### Production/testing note (high level)
- In production, you often build the React app (`npm run build`) and serve static files via a web server or copy them into Spring Boot's static resources, while Spring Boot serves APIs.
- The exact deployment packaging depends on your CCSP project requirements; the key learning point is that dev-time "run both servers" differs from production serving.

---

## 5. `React Frontend Layering` Use Cases

### General use cases
- **CRUD dashboard/UI:** React pages/forms/tables call Spring Boot REST endpoints to create, read, update, and delete records.
- **Authenticated app shell:** React login/UI flows call Spring authentication endpoints and send tokens/cookies on subsequent API requests.
- **Data visualization/admin panel:** React renders charts/tables from API responses provided by Spring services.
- **Workflow/task interface:** Multi-step UI actions orchestrated in React, persisted/executed through backend APIs.

### Use cases in systems/projects (relevant patterns)
- **Monorepo web app:** `backend/` (Spring Boot) + `frontend/` (React) in one repository; frontend consumes backend APIs during development via proxy and in production via deployed API routes.
- **Spring Boot + SPA integration:** React SPA developed independently, then built and served as static assets (or deployed separately) while Spring Boot remains the API/service layer.
- **Micro-frontend or modular frontend evolution (later stage):** starting from a simple layered React app and evolving into feature-based/domain-oriented modules as complexity grows.

### When to use this approach (the "When")
- When your CCSP project needs a **browser UI** and a **Spring backend** with clear API boundaries.
- When you want a **simple but maintainable** frontend structure (not all logic inside components).
- When you need to **develop and test quickly** with hot-reloading frontend dev server while backend runs separately.
- When future growth is expected and you want a foundation that can evolve into more formal layered/clean/feature-based architecture.

---

## 6. Pros & Cons

### Pros
- **Clear separation of concerns:** Easier to understand and modify UI, state/use-case logic, and API calls independently.
- **Better maintainability than ad-hoc structure:** Centralized API layer and organized folders reduce duplication and tangled code.
- **Smooth developer experience:** React dev server + Spring Boot dev run enable fast iteration and focused debugging.
- **Flexible project organization:** Works well with `frontend/` + `backend/` monorepo layout and can later support production bundling strategies.
- **Scalable learning path:** Simple layering can evolve toward feature-based or clean architecture patterns as the project grows.

### Cons
- **More initial setup than a single-file/simple script UI:** Proxy/CORS, two runtimes, and folder conventions add setup overhead.
- **Cross-origin/dev integration pitfalls:** Misconfigured proxy or CORS can cause confusing request failures (e.g., blocked requests, wrong ports/paths).
- **Risk of "fake layering":** Creating folders/layers without real boundaries (e.g., components still calling fetch directly everywhere) gives little benefit.
- **Operational complexity in dev:** Two servers/processes must be started, monitored, and kept in sync (ports, env vars, API paths).
- **Potential duplication/mapping work:** DTOs/types and transformations may be needed between backend contracts and frontend models.

---

## 7. Why We Needed `React Frontend Layering`

### The main problem
As web applications grew beyond static pages, teams needed a way to build **interactive UIs** that could communicate with **server-side systems** (like Spring applications) without tightly coupling presentation code to backend internals or scattering network/business logic throughout the UI. Without structure, frontend code becomes hard to change, test, and integrate reliably—especially when APIs, UI states, and team size grow.

### Historical/contextual drivers (related to frontend layering and React+backend integration)
- **Separation of concerns in software design:** Layered architectural thinking (long used in backend and enterprise systems) influenced frontend architecture to reduce coupling and clarify responsibilities.
- **Rise of SPAs and API-driven backends:** Modern frontends (React and similar libraries/frameworks) increasingly consume backend APIs (e.g., Spring Boot REST APIs), making client-server boundaries and integration workflows central concerns.
- **Need for maintainable frontend codebases:** As React apps grew, teams moved from component-only organization toward layered, feature-based, or clean architecture approaches to manage complexity, testing, and change.
- **Developer workflow demands:** Running separate frontend and backend dev servers with fast feedback (HMR/dev reload) required practical integration patterns such as proxies, CORS configuration, and coordinated run scripts/tasks.

### Why this matters for a CCSP project (practical motivation)
- A CCSP project typically needs a usable interface over backend capabilities; a simple layered React frontend provides a **clear path** to connect UI actions to Spring APIs.
- Defining structure early (layers + project placement + run workflow) reduces rework when features, endpoints, and team collaboration expand.
- Understanding the integration mechanics (API calls, proxy/CORS, dual-server dev run) is foundational for debugging and for building a reliable end-to-end system.

---

## References

### Section 1 & 2 (What it is / Properties: layered frontend, React, separation of concerns)
- [Frontend Architecture — Burhanuddin Hrynkevych (Medium)](https://medium.com/@hrynkevych/frontend-architecture-ff011afff992)
- [How I Apply Clean Architecture in Frontend React Projects — Lakshay Kapoor (Medium)](https://medium.com/@lakshaykapoor08/how-i-apply-clean-architecture-in-frontend-react-projects-0982b60ddb2c)
- [Layered Architecture: Still Relevant for Frontend? — Feature-Sliced Design](https://feature-sliced.design/blog/frontend-layered-architecture)
- [Exploring 4-Layer Frontend Architecture: A Practical Guide — DEV Community](https://dev.to/ahr_dev/exploring-4-layer-frontend-architecture-a-practical-guide-362b)
- [React Clean Architecture example (GitHub, daslaf/react-clean-architecture)](https://github.com/daslaf/react-clean-architecture)
- [Layered frontend architecture example (GitHub, zavvdev/layered-frontend-arch)](https://github.com/zavvdev/layered-frontend-arch)

### Section 3 & 4.1 (API communication, proxy, CORS, React ↔ Spring Boot)
- [React + Spring Boot: How to Connect Frontend and Backend (Complete Guide) — CodeBegun](https://codebegun.com/blog/react-spring-boot-connect-frontend-backend)
- [Integrate React Frontend with Spring Boot Backend — CodingStreams](https://codingstreams.in/posts/spring-boot/integrate-react-frontend-with-spring-boot-backend)
- [Frontend Proxy Paths in Spring Boot with React Dev Server — Alexander Obregon (Medium)](https://medium.com/@AlexanderObregon/frontend-proxy-paths-in-spring-boot-with-react-dev-server-2b18d136fc64)
- [Using http-proxy-middleware with React and Spring Boot (Stack Overflow)](https://stackoverflow.com/questions/65555658/http-proxy-middleware-proxy-is-not-working-in-react-js-and-spring-boot-project)
- [Create React App proxying API requests in development (Create React App docs)](https://create-react-app.dev/docs/proxying-api-requests-in-development/)
- [Vite server proxy options (Vite docs)](https://vite.dev/config/server-options#server-proxy)
- [Axios documentation](https://axios-http.com/docs/intro)
- [Fetch API (MDN Web Docs)](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [Cross-Origin Resource Sharing (CORS) (MDN Web Docs)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS)

### Section 4.2 & 4.3 (Project tree/placement and running together)
- [Run and Package React App With Spring Boot via only React Commands — Muco (Medium)](https://medium.com/@mucoboy/run-and-package-react-app-with-spring-boot-via-only-react-commands-15762a822734)
- [How to combine a Java Spring Boot back end with a ReactJS front end app — Bhargav Kanjarla (Medium)](https://medium.com/@bhargavkanjarla01/how-to-combine-a-java-spring-boot-back-end-with-a-reactjs-front-end-app-ed8d8ca65285)
- [How can I use create-react-app with Spring Boot? (Stack Overflow)](https://stackoverflow.com/questions/44892384/how-can-i-use-create-react-app-with-spring-boot)
- [concurrently (npm package)](https://www.npmjs.com/package/concurrently)
- [Spring Boot static content serving (Spring Boot Reference)](https://docs.spring.io/spring-boot/reference/web/servlet.html#web.servlet.spring-mvc.static-content)
- [Spring Boot + React monorepo example (GitHub, YogeshDharya/Localyze)](https://github.com/YogeshDharya/Localyze)
- [Spring Boot + React project layout example (GitHub, ShreyanDev5/SpringMart)](https://github.com/ShreyanDev5/SpringMart)

### Section 5, 6, 7 (Use cases, trade-offs, motivation/history)
- [Feature-Based Architecture for Next.js: Scaling React Apps with Layered Design — Medium](https://medium.com/@srachel27/feature-based-architecture-for-next-js-scaling-react-apps-with-layered-design-216b2f9fa9e2)
- [Building Robust React Apps with 3-Tier Architecture and Custom Hooks — Kavindu Madushanka (Medium)](https://medium.com/@kavindumadushanka972/building-robust-react-apps-with-3-tier-architecture-and-custom-hooks-799e24a8d740)
- [React documentation (react.dev)](https://react.dev/)
- [Spring Guides](https://spring.io/guides)
- [Multitier architecture (Wikipedia, overview of layered separation concept)](https://en.wikipedia.org/wiki/Multitier_architecture)
