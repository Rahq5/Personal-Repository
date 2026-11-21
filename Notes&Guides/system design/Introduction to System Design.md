# Introduction to System Design

> A **LearningFlow** note built from the seven mandatory questions in `templates/ai_prompts/LearningFlow.md`. Web research was performed for every section; sources are listed at the end of the file.

---

## 1. What is System Design

**Definition.** System design is the process of defining the architecture, components, modules, interfaces, and data flow of a software system so that it satisfies specified functional and quality requirements. It is the bridge between *requirements analysis* (what the system must do) and *implementation* (writing the code). [1][3][4]

- **As a verb:** following a process to plan how a product will work before it is implemented.
- **As a noun:** the artifact produced — design documents, sketches, diagrams (e.g. UML), specifications — that serves as a plan for implementation. [5]

**What it helps in**
- Aligning an information system with an organization's strategic objectives; deciding whether to buy, outsource, or build in-house. [2]
- Translating user requirements into a "blueprint" — the link between the problem space and the solution space. [7]
- Thinking through answers before coding → fewer errors and problems (efficiency); fewer bugs caused by incorrect assumptions → reliability. [3]

**What it does**
- Decomposes the system into subsystems, defines their data, interfaces, and communication, and addresses the major design goals (Bruegge's eight issues: identify goals, subsystem decomposition, layers vs. partitions, coherence & coupling, etc.). [6]
- Answers high-level questions: *Where does data live? How do components communicate? What happens when one piece fails? How does the system behave at 100x traffic?* [4]
- Generates, communicates, and evaluates candidate designs against quality attributes — the **Generate–Communicate–Evaluate (GCE)** paradigm. [5]

**What it offers**
- Scalability, reliability, performance, maintainability, and a sensible cost balance. [4]
- A shared vocabulary and diagrams that let stakeholders reason about the system before it exists. [3]

> **Note:** design happens *before* the system is built; **system analysis** happens *after* — analysis breaks a system down to understand it, design plans it. [3]

---

## 2. System Design Properties

These are the design properties a system is judged on — also called **quality attributes** (characteristics of a system that determine its fitness for use): [5][13]

- **Scalability** — handle growth in users, data, traffic. It is not a property of a single component; the system is only as scalable as its *least* scalable part. Vertical (scale up, add power to one machine) vs. horizontal (scale out, add machines). [10][11]
- **Reliability** — continue working correctly when things go wrong (hardware faults, software bugs, human error). Related ideas: *fault* (a component deviating from spec) vs. *failure*; *idempotence* (the same request can run twice without corrupting state); consistency models. [12][11]
- **Availability** — uptime target (e.g. 99.9% or 99.99%); *graceful degradation* (the system keeps serving a reduced but useful function instead of failing completely) when parts fail. [16][10]
- **Performance** — **latency** (time to respond to one request) and **throughput** (requests handled per unit of time). Architecture decisions set these early; code optimization cannot rescue a bad architecture later. [4]
- **Maintainability / Modularity** — modularity, abstraction, separation of concerns, low coupling; safe evolution as requirements change. [11][1]
- **Security** — protect data and users from unauthorized access. [9]
- **Extensibility, reusability, interoperability, testability, robustness, portability, usability** — the broader quality-attribute set. [5][13]
- **Cost-effectiveness** — balance over-engineering (wasted money) against under-engineering (outages). [4]

Properties **conflict**, so system design is trade-off management: consistency vs. availability (CAP theorem), latency vs. throughput, simplicity vs. flexibility, cost vs. performance, performance vs. fault tolerance, security vs. usability, time-to-market vs. technical debt (the future cost of taking shortcuts now). [4][27][32][14]

---

## 3. System Design Assumptions & Expectations

**Assumptions** — design begins in ambiguity, so implicit assumptions must be surfaced early; unexamined assumptions silently drive architectural decisions that later fail under real conditions. [15][16]
- **Scale assumptions:** users, QPS (**queries per second**), storage size, bandwidth — *back-of-the-envelope estimation* (rough mental math used to size a system before choosing components) prevents over- or under-designing. [16][4]
- Environment, supplier, and process constraints are treated as first-class inputs. [15]
- Stakeholders have different goals: developers care about modifiability, readability, and well-defined interfaces; clients about reliability, low cost, rapid development, and flexibility; end users about functionality, usability, fault tolerance, and robustness. [6]

**Expectations / intended outcomes and benefits**
- The product behaves as a coherent whole: interfaces align predictably, assumptions are explicit, and risks are mitigated by design rather than discovered after the build. [15]
- Non-functional requirements such as interoperability and security are architected for early in the lifecycle. [8]
- The architecture is a usable asset for decision makers to evaluate investments and for communication among stakeholders via multiple views. [8]
- **Risk-driven:** the amount of design should be proportional to risk, where `Risk = cost of failure × likelihood of failure`; the goal is to eliminate designs likely to cause high-risk failures, not to achieve perfection. [5]

---

## 4. How System Design Works / Operate

**Workflow / steps** (synthesized from the structured approaches in [4][18][16][17][20]):
1. **Clarify requirements** — functional (what it does) and non-functional (performance, scale, availability, consistency), plus scope boundaries (what is in and out of scope). [4][18][16]
2. **Estimate scale** — QPS, data volume, bandwidth; prevents over-/under-designing. [4][16]
3. **Define the high-level design (HLD)** — draw the major components: clients, servers, databases, caches, load balancers, message queues; show data flow. [4][18]
4. **Deep-dive into components (LLD)** — design the most critical component in detail: database schema, API contracts, caching strategy, failure handling. [4][18]
5. **Identify bottlenecks & trade-offs** — where the system breaks under extreme load, what happens during a network partition, mitigations, and alternatives evaluated via trade studies. [4][17]
6. **Review & iterate** — evaluate designs against quality attributes; design is never "finished" but continuous and iterative (Generate → Communicate → Evaluate). [5][17]
7. **Implementation planning** — produce technical specifications, then build, test, and validate. [30][31]

**How to do it rightly**
- Start simple; decompose later when the need is clear. [4]
- Make assumptions explicit and check them with stakeholders. [16]
- Use diagrams at multiple levels — Akamai lists five diagramming levels, beginning with architecture (block diagram) and components. [3]
- Follow the architecting process: identify components, establish structural/behavioral relationships, select/compose/create the architecture, describe it from multiple perspectives, and evaluate it. [8]
- NASA defines four interdependent, iterative system design processes: develop stakeholder expectations → technical requirements → logical decompositions → design solutions. [17]

---

## 5. System Design Use Cases

**When it is used**
- Before coding any non-trivial system; it is a phase of the SDLC (software development life cycle) bridging requirements and implementation. [18]
- In architecture reviews and in system design interviews (tested at top tech companies). [4][9]
- When a system must scale, survive failures, or align IT with business strategy. [2]

**Example systems**
- **Netflix** — global CDN, microservices, streaming events via Apache Kafka (trillions of events/day), and safe failover: when the personalization service fails, traffic is rerouted to a service returning a generic "top 10" list. [19][20]
- **Twitter/X** — timeline generation with a push model for celebrities, pull model for regular users, and a hybrid optimization; peaks of 6,000+ tweets/sec. [21]
- **Amazon** — service-oriented architecture, DynamoDB for catalog data, recommendation engine; on Black Friday it intentionally sheds non-essential features (reviews, recommendations) to protect checkout. [21]
- **Instagram** — ~2B users, ~95M photos/day, Django/PostgreSQL backend with Redis caching. [21]
- **Uber** — geospatial, high-throughput ride matching. [28]
- **Discord** — stores trillions of messages. [19]
- **Airbnb** — search and pricing engine across 7M+ listings. [28]
- **General patterns:** event-driven order processing, user-registration flows, real-time dashboards, chat applications, content publishing. [29]

---

## 6. Pros & Cons

**Pros (upside)** [22][7]
- **Scalability** — handle growing workloads without a rewrite; horizontal/vertical scaling, caching, sharding.
- **Modularity & reusability** — cleaner code organization and teams that work independently on different components.
- **Performance optimization** — spot bottlenecks early and address them with caching and efficient data structures.
- **Reliability & fault tolerance** — redundancy, failover, and disaster recovery reduce single points of failure.
- **Maintainability & extensibility** — separation of concerns and loose coupling make future changes easier.
- **Quality & cost** — well-designed software meets functional and nonfunctional requirements, and defects caught in design are far cheaper to fix than defects found in coding or testing. [7]

**Cons (downside)** [22][14][23][25]
- **Increased complexity** — large-scale design needs deep knowledge of patterns, algorithms, and trade-offs; a poor design hurts performance and maintenance.
- **Time and resource intensive** — analysis, collaboration, and iterations across performance, scalability, and security constraints.
- **Overengineering** — unnecessary complexity wastes effort, raises maintenance cost, and makes debugging harder.
- **Trade-off penalties** — reliability usually costs more; lower latency may reduce consistency; simplifying may limit extensibility. [14]
- **Microservices-specific costs** — inter-service network latency, deployment orchestration, service discovery, data consistency issues, and operational overhead. [23][24][25]
- **Never truly finished** — continuous iteration consumes ongoing effort. [5]

---

## 7. Why We Needed System Design

**The main problem.** As systems grow, ad-hoc coding hits walls: systems become hard to understand, change, scale, and keep reliable, and defects introduced early become exponentially more expensive to fix later. Poor design risks lost customers and revenue (availability/usability), delays (poor extensibility), theft of sensitive information (poor security), and in safety-critical domains even injury or loss of life. [7][5]

**History and the decisions that led here**
- The discipline draws from computer science and systems engineering, with decades of practice organized into **design patterns**, **architectural styles**, and modeling notations such as **UML**. Dijkstra's *separation of concerns* (dividing a system into parts, each addressing a coherent subset of concerns) is foundational; modularity, abstraction, encapsulation, and low coupling are its direct expressions. [1]
- The classic starting point was the **monolith** — one codebase and one deployable unit, simple to develop, test, and deploy early — but tight coupling made individual components hard to scale and any change risky. [23][24]
- Growth in traffic and users, plus the need for independent scaling, resilience, and autonomous teams, drove the industry to **distributed systems / microservices**: small, independently deployable services with their own data, communicating through APIs and messaging. [26][23]
- Key realization: microservices do not *remove* complexity — they reveal it, making large systems easier to manage; monoliths remain the right choice for small teams and early-stage products. Wrong service boundaries give "the worst of both worlds": the operational complexity of distribution plus the tight coupling of a monolith. [24][31][26]
- Modern practice: start with a monolith, extract services gradually at natural boundaries, and size the design effort by risk (`cost of failure × likelihood of failure`). [26][5]

---

## References

1. IEEE Technology Navigator — *Software design*. https://technav.ieee.org/topic/software-design/
2. OpenStax — *4.1 Systems Analysis and Design for Application Development*. https://openstax.org/books/foundations-information-systems/pages/4-1-systems-analysis-and-design-for-application-development
3. Akamai — *System Design 101: How to Design a System*. https://www.akamai.com/cloud/guides/system-design/
4. SystemForces — *Introduction to System Design*. https://www.systemforces.com/learn/courses/01-introduction
5. CMU — *Introduction to Software System Design* (lecture slides). https://cmu-swdesign.github.io/slides/Lecture-1-Introduction.pdf
6. TUM (Bruegge) — *System Design and Software Architecture* (lecture slides). https://ase.in.tum.de/lehrstuhl_1/files/teaching/ss07/SE/SE2007_Lecture11.pdf
7. IEEE Computer Society — *Why Software Design Is Important*. https://www.computer.org/resources/importance-of-software-design-is-important
8. IJSSE — *The Process of Architecting for Software / System Engineering*. https://manta.cs.vt.edu/sinergy/Docs/IJSSE.pdf
9. InfraSketch — *The Complete Guide to System Design in 2026*. https://infrasketch.net/blog/complete-guide-system-design
10. DesignGurus — *50 System Design Concepts Explained Simply*. https://designgurus.substack.com/p/50-system-design-concepts-explained
11. LevelUpCoding — *System Design Quality Attributes Clearly Explained*. https://blog.levelupcoding.com/p/system-design-quality-attributes
12. Magda Miu — *Reliability, Scalability & Maintainability*. https://magdamiu.com/2022/09/10/reliability-scalability-maintainability
13. Wikipedia — *List of system quality attributes*. https://en.wikipedia.org/wiki/List_of_system_quality_attributes
14. System Design Handbook — *System Design Tradeoffs*. https://www.systemdesignhandbook.com/blog/system-design-tradeoffs
15. Inertia — *Systems Design for Product Development*. https://www.inertiapd.com/development/systems-design
16. Duke University Career Hub — *How to Answer System Design Questions*. https://careerhub.students.duke.edu/blog/2023/03/24/how-to-answer-system-design-questions
17. NASA — *SEH 4.0 System Design Processes*. https://www.nasa.gov/reference/4-0-system-design-processes
18. GeeksforGeeks — *System Design Introduction — LLD & HLD*. https://www.geeksforgeeks.org/system-design/getting-started-with-system-design
19. ByteByteGo — *Real World Case Studies*. https://bytebytego.com/guides/real-world-case-studies
20. DEV Community — *Netflix System Design — Backend Architecture*. https://dev.to/gbengelebs/netflix-system-design-backend-architecture-10i3
21. *System Design Case Studies: Netflix, WhatsApp, Uber & More* (video). https://www.youtube.com/watch?v=irSmmAfYtO8
22. DEV Community — *Exploring System Design: Pros, Cons, and Effective Implementation*. https://dev.to/pyies/exploring-system-design-pros-cons-and-effective-implementation-1glp
23. ScholarHat — *Types of System Design: A Comprehensive Guide to Architectures*. https://www.scholarhat.com/tutorial/systemdesign/types-of-system-design
24. Martin Fowler — *Microservices*. https://martinfowler.com/articles/microservices.html
25. Atlassian — *Microservices vs. monolithic architecture*. https://www.atlassian.com/microservices/microservices-architecture/microservices-vs-monolith
26. DEV Community — *Monolithic vs Distributed Systems: Trade-offs, Evolution, and Real-World Design Decisions*. https://dev.to/imsushant12/monolithic-vs-distributed-systems-trade-offs-evolution-and-real-world-design-decisions-1npg
27. Educative — *5 hidden trade-offs of System Design*. https://www.educative.io/newsletter/system-design/sytem-design-trade-offs
28. SystemDesigner — *Case Studies*. https://www.systemdesigner.net/case-studies
29. Medium (AWS) — *Event-Driven Architecture Explained with Real-World Examples*. https://aws.plainenglish.io/event-driven-architecture-explained-with-real-world-examples-4f9cc5c67661
30. New York ITS — *System Design*. https://its.ny.gov/system-design
31. Study.com — *Systems Design | Requirements, Process & Examples*. https://study.com/academy/lesson/systems-design-requirements-process-examples.html
32. DesignGurus — *System Design Trade-Offs*. https://www.designgurus.io/blog/complex-system-design-tradeoffs
