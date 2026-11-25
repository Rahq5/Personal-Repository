# Spring AI

> LearningFlow note — topic: **Spring AI**. Sources are listed at the end.
> Version context: Spring AI **2.1.0** is the stable line in the reference docs; the docs also state that Spring AI 2.0.x supports Spring Boot 4.0.x / 4.1.x.

---

## What is `Spring AI`

**Spring AI** is an official Spring application framework for AI engineering in Java. Its stated goal is to apply Spring design principles — **portability** (the ability to move between AI providers without rewriting your code) and **modular design** (using only the parts you need) — to AI work, using POJOs as the building blocks [1][2].

- **What it is:** a set of portable interfaces, Spring Boot starters, auto-configuration, and ready-made patterns (memory, RAG, tool calling) for talking to AI models from a Spring application.
- **What it helps in:** connecting enterprise **data** and **APIs** to AI models without writing vendor-specific HTTP, JSON parsing, retry, and configuration code by hand.
- **What it does:** builds a prompt from your messages and options, sends it to a chosen model provider (cloud or local), and gives you back text, a stream, or a mapped Java object.
- **What it offers:** `ChatClient` (fluent API), `ChatModel`/`EmbeddingModel` (portable model APIs), advisors, structured output, multimodal messages, tool calling, MCP, vector-store APIs, session memory, evaluation, and Micrometer observability [2].

One-line mental model: **Spring Data abstracts databases; Spring AI abstracts AI models** [10].

---

## `Spring AI` Properties

These are the **design properties** of the framework, not a list of every feature:

1. **Provider portability** — one `ChatModel` / `EmbeddingModel` interface with implementations for OpenAI, Anthropic, Bedrock, Google, Ollama, Mistral, DeepSeek and others. Provider choice is a configuration decision, not a code change [2].
2. **Escaped abstraction** — portable calls for sync **and** streaming, plus access to provider-specific options when you need them (`OpenAiChatOptions`, etc.) [2]. The abstraction is a base, not a cage.
3. **Convention over configuration** — Spring Boot starters and auto-configuration create the model bean and a prototype `ChatClient.Builder`; `start.spring.io` lets you pick the model and vector store [1][3].
4. **Fluent client API** — `ChatClient.prompt().user(...).call().content()`, deliberately shaped like `WebClient` / `RestClient` [2][3].
5. **Advisor chain** — cross-cutting concerns (memory, RAG, tools, logging, guardrails) are chained components that run before and after the model call, much like servlet filters [5][9]. Even the model call itself is the last advisor in the chain [9].
6. **Composability / modularity** — domain modules (`spring-ai-commons`, `spring-ai-model`, provider modules) keep the dependency footprint small; capabilities are added one at a time [8][9].
7. **Structured output** — `.entity(Class)` maps model text into Java records/POJOs, with optional schema validation, self-correction, and provider-native JSON schemas [2][6].
8. **State handling as a separate concern** — models are stateless, so conversation history lives in a session/memory store and is sent with each request; the Session API in 2.1 replaces the older Chat Memory [2][5].
9. **Data awareness** — vector-store abstraction with SQL-like metadata filters, ETL document ingestion, and modular RAG advisors [2][5].
10. **Tool and protocol support** — `@Tool` methods let the model ask your application to run code; MCP lets tools be written once and consumed by any MCP-speaking application [5][7].
11. **Production-minded** — Micrometer metrics/traces (`gen_ai.client.token.usage`), evaluators, guardrails, virtual-thread friendliness, Testcontainers and Dev Services support [1][2][7].

---

## `Spring AI` Assumptions & Expectations

### Assumptions (what must already be true)

- You have a **Spring Boot application**. Spring AI is not a standalone runtime; its first-class experience is built on Boot auto-configuration [2][10].
- Your Boot, Spring AI, and JDK versions are **aligned**. Spring AI 2.0.x targets Spring Boot 4.0.x / 4.1.x [3]; the Boot 4 line uses Java 21 as its baseline [10]. Pin versions with `spring-ai-bom` [3].
- You have either a **provider account + API key** (OpenAI, Anthropic, etc.) or a **local model** such as Ollama [2][7].
- You accept that the model is an **external network dependency**: calls are relatively slow, cost tokens, and can be wrong (hallucinations) [7].
- Your data is **not in the model**: private documents need to be supplied through the prompt, RAG, or tools [5].

```properties
# application.properties — provider credentials come from the environment,
# never from a hard-coded string in source control.
spring.ai.openai.api-key=${OPENAI_API_KEY}
# Choosing a model is a config change: application code stays untouched.
spring.ai.openai.chat.options.model=gpt-4o
```

### Expectations (what you should get out of it)

- A first endpoint that takes a user prompt and returns the model's answer with very little code [1].
- The ability to **swap providers** by changing configuration rather than controllers and services [2][10].
- **Incremental growth**: start with `ChatClient`, then add memory, RAG, tools, and observability only when a real need appears [10].
- Testable code: model calls sit behind interfaces you can mock or replace, and token usage is visible through Actuator metrics [1][7].
- Honest limits: the API surface still moves between minor versions, so track releases when upgrading [10].

---

## How `Spring AI` Works / Operate

### 1. Project setup (build time)

Add a model starter through Spring Initializr, or import the BOM and add the module dependency. Maven Central already contains the artifacts, so no extra repository is needed for releases [3].

```xml
<!-- Maven: the BOM pins compatible Spring AI module versions. -->
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.ai</groupId>
      <artifactId>spring-ai-bom</artifactId>
      <version>2.1.0</version> <!-- use the version matching your Boot line -->
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>

<!-- The starter brings in the provider module and its auto-configuration. -->
<dependencies>
  <dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-openai</artifactId>
  </dependency>
</dependencies>
```

### 2. Auto-configuration (startup)

Boot reads the properties, creates a `ChatModel` bean for the selected provider, and exposes a prototype-scoped `ChatClient.Builder`. Prototype scope means each injection point gets its own builder, so you can configure different defaults per client [4].

### 3. The request path (runtime)

The shortest useful app is a normal Spring controller:

```java
@RestController
class PromptController {
    private final ChatClient chatClient;

    // Spring injects the auto-configured builder; build() creates the client.
    PromptController(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }

    // The HTTP query parameter is the user's prompt.
    @GetMapping("/ai")
    String generation(String userInput) {
        return this.chatClient
            .prompt()          // start a request specification
            .user(userInput)   // put the text in a user message
            .call()            // choose synchronous execution
            .content();        // trigger the call and return the answer text
    }
}
```

Under the hood that one chain does all of the following:

1. **Prompt assembly** — a prompt is a list of *messages* plus *options*. Roles matter: `system` sets behavior/rules, `user` carries the question, `assistant` carries earlier answers, `tool` carries tool results [5]. Fixed wording with placeholders is handled by `PromptTemplate` + a `TemplateRenderer` (StringTemplate by default) [4][5].
2. **Tokenization** — models read *tokens*, not whole words (roughly ¾ of an English word). Input and output tokens drive cost; the context window limits how much can be sent at once [5].
3. **Advisor chain** — `ChatClient` builds an ordered chain for each call. Advisors can change the request/response, add history, add retrieved documents, log, check guardrails, and even re-run the rest of the chain (recursive advisors such as tool calling and schema-validation retries) [5][9]. The chain ends with `ChatModelCallAdvisor` / `ChatModelStreamAdvisor`, which invoke the model [9].
4. **Provider adapter** — the `ChatModel` implementation translates the portable prompt into the vendor's HTTP request (and parses the response), which is where provider-specific options live [4][6].
5. **Response handling** — `.call()` only selects the mode; the model is actually invoked when you ask for a result: `.content()` (String), `.chatResponse()` (metadata + generations), `.entity(...)` (Java type), or `.stream()` (a `Flux` of pieces) [4][6]. Token usage appears in response metadata and Micrometer metrics [1][5].

`ChatClient` vs `ChatModel`: use **`ChatClient`** for normal apps — it owns the advisor chain, tool loop, memory, and observability. Use **`ChatModel`** directly only for low-level control; it returns raw responses and does not execute tools itself [6].

### 4. Adding knowledge (RAG) — two phases

```java
// Advisor: before each call it retrieves similar documents and adds them to the prompt.
// The VectorStore abstraction hides which database actually stores the vectors.
var chatClient = builder
    .defaultAdvisors(new QuestionAnswerAdvisor(vectorStore))
    .build();
```

- **Ingestion (ahead of time):** read documents → split into chunks → embed chunks → store in a vector store.
- **Retrieval (per question):** embed the question → find nearest chunks → add them to the prompt → let the model answer from that context [5][7].

### 5. Giving the model actions (tool calling)

```java
@Component
class SchedulerTools {
    // @Tool exposes this method to the model with a human-readable description.
    @Tool(description = "Book a pickup date for an adopted dog")
    String schedule(int dogId) {
        return "2026-11-28"; // the application, not the model, runs this code
    }
}
```

The `ToolCallingAdvisor` sends tool definitions with the prompt; if the model asks for a tool, the advisor runs it, appends the result to the conversation, and calls the model again until a final answer is produced. The model never runs code itself [5][9].

### 6. Observing it

Set `management.endpoints.web.exposure.include=*` (or a narrow safe list in production) and inspect `gen_ai.client.token.usage`. Traces follow the request through model, advisors, tools, and vector store [1][5].

---

## `Spring AI` Use Cases

These are the **"when"** situations the framework is designed for [2][7]:

| When you need... | Spring AI piece |
|---|---|
| An HTTP endpoint that turns a user prompt into an answer | `ChatClient` + a model starter (your target app) |
| Q&A over private docs / "chat with your documentation" | Vector store + RAG advisors (`QuestionAnswerAdvisor`) |
| Conversation that remembers earlier turns | Session / chat-memory advisor with a conversation id |
| A Java object instead of free text (sentiment, extraction, form filling) | `.entity(...)` structured output, with validation |
| The model to read live data or perform an action | `@Tool` methods + `ToolCallingAdvisor`, or MCP tools |
| To reuse tools across languages/applications | MCP client / server starters |
| To classify or filter content cheaply | Moderation / decision model APIs |
| To develop without cloud keys or with data staying local | Ollama provider |
| To know what a call cost and whether it was correct | Micrometer metrics, evaluators, guardrails |

---

## Pros & Cons

### Pros

- **Portability** — provider swaps are mostly configuration, and the same interfaces cover major models and vector stores [2][10].
- **Familiar Spring model** — DI, starters, `@RestController`, Actuator, Spring Security, DevTools, Testcontainers — little new machinery to learn [1][7].
- **Batteries included** — RAG, memory, tools, MCP, structured output, evaluation and observability are first-class [2].
- **Low overhead in practice** — one community analysis notes LLM network latency is orders of magnitude larger than framework overhead [10].
- **Active ecosystem** — rapid releases and community integrations [10].

### Cons

- **Spring-only advantage** — if you use Quarkus, Micronaut or bare Vert.x, the auto-configuration story mostly disappears; LangChain4j is the alternative there [10].
- **Abstraction leaks at the edges** — provider-specific features still require provider-specific options; portability is not magic [2].
- **Docs can be thin in places** — one senior developer reported reading integration tests to understand advisor composition; budget extra time for advanced RAG/memory setups [10].
- **Moving API surface** — 1.x adapters changed between minor versions; the 1.x → 2.x move also shifts to Boot 4 / Jakarta EE 11, so track versions [10].
- **Model problems remain yours** — hallucinations, token cost, context limits, and prompt injection are not solved by a framework; you still need evaluators, guardrails and good retrieval [7].

**Judgment:** for a Spring Boot backend, Spring AI is the lowest-friction way to add an LLM-backed endpoint. For a non-Spring JVM stack, or if you need the widest possible provider list, compare LangChain4j first [10].

---

## Why We Needed `Spring AI`

**The problem:** most AI engineering is integration — call an HTTP API with human-language strings, parse the reply, manage keys and history. Java teams already had the business logic and data, but in 2023–2024 the AI tooling ecosystem was Python-first, which suggested a separate Python sidecar service for every LLM feature [7][10].

**The decision:** bring the integration into the Spring container using the same patterns that made Spring Data work — abstractions behind interfaces, inversion of control, and configuration as a deployment concern [1][10].

**History (official releases):**

- **20 May 2025** — Spring AI **1.0 GA**, with portable abstractions, starters, auto-configuration, and observability [7].
- **12 Nov 2025** — **1.1 GA**, adding MCP, modularized RAG and many refinements [10].
- **20 Nov 2026** — **2.1.0 GA** announced, continuing the Boot 4-era line (2.0.x targets Boot 4.0/4.1) [11][3].

**What it changed:** you no longer choose between "keep the Spring backend" and "add AI". You keep the backend, add a starter, and treat the model as one more external dependency your Spring application can call, observe, and test [1][7].

---

## References

1. Spring AI — Getting Started: https://docs.spring.io/spring-ai/reference/getting-started.html
2. Spring AI — Project overview and features: https://spring.io/projects/spring-ai
3. Spring AI — Getting Started (Boot compatibility, BOM, Maven Central): https://docs.spring.io/spring-ai/reference/getting-started.html
4. Spring AI — Chat Client API: https://docs.spring.io/spring-ai/reference/api/chatclient.html
5. Spring AI — AI Concepts (models, messages, tokens, RAG, tools, advisors, observability): https://docs.spring.io/spring-ai/reference/2.1-SNAPSHOT/concepts.html
6. Spring AI — Chat Model API: https://docs.spring.io/spring-ai/reference/api/chatmodel.html
7. Spring Blog — *Your First Spring AI 1.0 Application* (20 May 2025): https://spring.io/blog/2025/05/20/your-first-spring-ai-1
8. Spring Blog — *Spring AI 1.0.0 M7 Released* (module structure): https://spring.io/blog/2025/04/10/spring-ai-1-0-0-m7-released
9. Spring AI — Advisors: https://docs.spring.io/spring-ai/reference/api/advisors.html and Tool Calling: https://docs.spring.io/spring-ai/reference/api/tools.html
10. DEV Community — *Spring AI: The Senior Dev's Honest Take on Java's AI Moment* (pros/cons, LangChain4j comparison): https://dev.to/sayed_ali_alkamel/spring-ai-the-senior-devs-honest-take-on-javas-ai-moment-2g9c
11. Spring Blog — *Spring AI 2.1.0 GA and 2.0.3 GA Available Now* (20 Nov 2026): https://spring.io/blog/2026/11/20/spring-ai-2-1-0-GA-available-now

> Accessed: 25 Nov 2026. Official Spring sources were used for definitions, internals and release dates; the community article was used only for the balanced pros/cons judgement.
