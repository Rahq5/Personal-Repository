# AI Engineering in Java instead of Python

Scope of this note: using Java (JVM) to build the AI parts of an application **instead of adding a Python service**, with the concrete target being my **prompt enhancer** that talks to an LLM from inside the app.

## 1. What is AI engineering in Java instead of Python

**AI engineering** = the process of designing, developing, and deploying AI systems that solve a real problem. It mixes software development, data work, and model usage, and it brings models out of research into production where reliability and performance matter [19].

**AI engineering in Java instead of Python** = doing that work inside a Java/JVM application, using Java libraries to talk to an LLM, instead of running a separate Python program and calling it from Java.

- **What it is here:** a normal Java backend feature that happens to call a model. Spring AI calls itself "an application framework for AI engineering" [1].
- **What it helps in:** adding AI features to an app that already runs on Java/Spring without a second runtime, second deployment pipeline, or extra network hop [14][15].
- **What it does:** builds the prompt, sends it to a model, receives the answer, converts that answer into a Java object, and keeps the whole thing testable and observable.
- **What it offers:** portable model APIs, prompt templates, structured output (response forced into a defined shape), chat memory, tool calling, RAG, evaluation, and metrics [1].
- **For my prompt enhancer:** the app takes a raw user prompt, renders a better one from a template, calls the LLM, and gets back a typed result such as the improved prompt plus the list of changes made.

## 2. AI engineering in Java — Properties (design properties)

These are the design traits of the Java/JVM way of doing this:

- **Portability (same code, different provider):** `ChatClient` / `ChatModel` in Spring AI, or unified `ChatModel` in LangChain4j, let the same application code run against OpenAI, Anthropic, Ollama, Bedrock, etc.; switching is mostly configuration [1][11].
- **Type-safe building blocks (POJOs = plain old Java objects):** the model output is mapped into Java `record`s / classes with `.entity(EnhancedPrompt.class)` or by declaring an interface return type in LangChain4j AI Services [5][12][13].
- **Prompt templating:** variables such as `{rawPrompt}` in Spring AI's `PromptTemplate`, or `{{it}}` in LangChain4j, are filled at runtime; templates can live in resource files [3][4][11].
- **Composition instead of one big call:** Spring AI **Advisors** (an interceptor = code that runs before/after the main call) inject memory or retrieved data into the prompt; LangChain4j gives both low-level components and high-level AI Services [6][11].
- **Framework-native:** Spring Boot starters, auto-configuration, properties/env-based config, dependency injection; LangChain4j also integrates with Quarkus, Micronaut, and Helidon [1][2][11].
- **Sync + streaming:** both normal `.call()` and token-by-token `.stream()` are supported [4].
- **Structured output in two ways:** format instructions added to the prompt and parsed back (converter), or provider-native JSON-schema enforcement when the provider supports it [5].
- **Observability (seeing what the running system does):** Micrometer metrics for latency, token usage, tool calls, plus tracing [1][10].
- **Modular dependencies:** add only the model starter needed; no need to pull the whole AI stack [2].
- **Local/offline option:** Ollama or Jlama can run a model locally for privacy and low latency [14].

## 3. AI engineering in Java — Assumptions & Expectations

### Assumptions

- The application is already a Java backend (Spring Boot in my case), so keeping AI in the same process is cheaper than a Python sidecar (a separate helper service running next to the app).
- The LLM is reachable through an API key / local endpoint; we are **using** a model, not training one.
- Prompt quality is application logic: it belongs in version-controlled templates and Java code, not in a notebook.
- Answers that feed other code must have a fixed shape, so structured output + validation is required, not optional [17].
- Secrets stay in environment variables / config, never in source code [14].

### Expected outcomes and benefits

- One deployable service, one set of logs, one security model, one CI pipeline.
- A prompt enhancer that returns something like:

  ```java
  // A record is an immutable data carrier; its fields define the JSON shape the model must return.
  record EnhancedPrompt(
      String improvedPrompt,    // the rewritten prompt sent back to the user
      List<String> changesMade, // what changed, so the user can review it
      String rationale          // why those changes make the prompt clearer
  ) {}
  ```

  instead of an unstructured string that has to be parsed by hand.

- Ability to change model provider by changing configuration, not rewriting the feature [1][9].
- Repeatable prompt tests: define success criteria first, then improve the prompt; not every failure is a prompt problem (sometimes a different model fixes latency or cost) [18].
- Visibility into tokens and latency so the feature can be judged on cost, not just "it works" [10].

### Ruthless check of the premise: "Java instead of Python"

- **Correctness for your exact case: 80%.**
- **What is correct:** for an *application-layer* feature like a prompt enhancer living inside a backend, Java + Spring AI/LangChain4j fully replaces Python; you do not need a Python service for prompt templates, model calls, structured output, memory, or evaluation [1][11][15].
- **What was missed:** (1) "instead of Python" is too absolute — model training, fine-tuning (extra training on your own data), and most new research libraries are still Python-first; (2) the newest model features and example code often appear in Python SDKs before Java ones; (3) Python's data/preprocessing libraries are richer if the enhancer later needs heavy text/document processing; (4) quick prompt experimentation in notebooks is faster in Python. If the scope ever moves from *using* a model to *building/training* one, Python becomes the stronger choice.

## 4. How AI engineering in Java works / operates

### Workflow to implement the prompt enhancer

1. **Create the app.** Use Spring Initializr with Web + the model starter, e.g. `spring-ai-starter-model-anthropic` (or OpenAI/Ollama). Spring AI 2.0.x targets Spring Boot 4.x, and the BOM manages versions [2][9].
2. **Configure the model.** Put the API key in config from an environment variable, choose the model and low temperature (e.g. `0.2`) because prompt rewriting should be consistent, not creative [2][9].
3. **Write the prompt as a template.** Put it in `src/main/resources/prompts/enhance.st` with a system message (the role/rules) and a user message with `{rawPrompt}` and `{goal}` placeholders [3][4].
4. **Define the output record** (the one from section 3).
5. **Call the model with `ChatClient`** and map the answer directly to the record:

   ```java
   // chatClient is built once from the configured ChatModel; one prompt() = one model request.
   EnhancedPrompt result = chatClient.prompt()
       .system("""
               You are a prompt enhancer. Improve the user's prompt for clarity,
               constraints, and expected output format.
               Never invent facts. Return only the requested structure.
               """) // system message = role + rules that apply to every call
       .user(u -> u.text("""
                   Raw prompt: {rawPrompt}
                   Goal: {goal}
                   """)
                   .param("rawPrompt", rawPrompt) // replaces {rawPrompt} in the template
                   .param("goal", goal))          // replaces {goal} in the template
       .options(ChatOptions.builder().temperature(0.2).build()) // low randomness = consistent rewriting
       .call()      // synchronous call; use .stream() for token-by-token output
       .entity(EnhancedPrompt.class); // parse the model text into our Java record
   ```

   Behind the scenes `ChatClient` uses `PromptTemplate` and a renderer to replace the variables, then the output converter turns the text into the Java type [4][5].

6. **Validate after parsing.** Structured Outputs guarantee valid JSON and schema shape, but your application still must handle refusals, incomplete responses, API errors, and business rules the schema cannot express [17].
7. **Add only what is needed.** For multi-turn "make it better again" flows, add `ChatMemory` via an advisor; RAG is not needed for a basic enhancer [6][10]. If you want the model to call app functions (e.g. look up project conventions), use `@Tool` [10].
8. **Test the prompt.** Use JUnit with Spring AI `Evaluator`s (`RelevancyEvaluator`, `FactCheckingEvaluator`) or LLM-as-a-Judge; keep the judge model separate from the generating model to reduce self-bias [7][10]. Compare prompt variants, not just one happy path [18].
9. **Observe it.** Track model latency, input/output tokens, and errors with Micrometer [10].
10. **Handle failure paths.** Timeouts, rate limits, malformed output, retries with backoff, and safe fallback to the original prompt.

### The alternative path (if not using Spring)

LangChain4j declares an interface:

```java
// We only declare the interface; LangChain4j creates the implementation at runtime.
interface PromptEnhancer {
    @SystemMessage("You are a prompt enhancer. Never invent facts.") // role + rule for every call
    @UserMessage("Improve this prompt: {{it}}") // {{it}} is replaced by the argument below
    EnhancedPrompt enhance(String rawPrompt); // return type triggers structured-output parsing
}
```

then builds it with `AiServices.builder(PromptEnhancer.class).chatModel(model).build()`; the return type triggers structured output parsing automatically [12][13].

## 5. AI engineering in Java — Use cases

**When to use Java for AI (the "when"):**

- The app already runs on the JVM and the AI feature must ship with it: **my prompt enhancer**, in-app assistants, "summarize this" / "rewrite this" buttons [15].
- Enterprise systems where the model must reach existing Java data/APIs and comply with existing security and audit rules [14][15].
- Customer-support chatbots, educational assistants, document Q&A over your own data (RAG), extracting structured info from unstructured files, generation (emails/content), and transformation (summarize, proofread, translate) [1][11].
- Classification and routing, tool/function calling, and agent workflows when the feature grows beyond one call [10][11].

**When Python is still the better tool:**

- Training or fine-tuning models, research, and experiments.
- Heavy data-science preprocessing pipelines.
- fast notebook-based prompt prototyping.

## 6. Pros & Cons

### Pros

- **No Python sidecar:** one process, one deployment, one observability stack; the two-process architecture becomes a maintenance liability in production [15].
- **Type safety:** LLM output becomes a real Java object with compile-time checks; useful when generated data feeds other code [1][5].
- **Provider portability:** swap model vendors via configuration; no rewrite of the enhancer [1][9].
- **Reuses what already exists:** Spring Boot config, dependency injection, security, testing, and Micrometer [1][10].
- **Enterprise readiness:** governance, audit, and the same deployment process as the rest of the backend [14][15].
- **Mature runtime:** JVM concurrency (including virtual threads) handles many slow model calls well; framework overhead is tiny compared with LLM network latency.
- **Standards-based growth:** MCP (Model Context Protocol, a standard way for models to use tools) is supported, and the MCP Java SDK was donated to Anthropic [10].

### Cons

- **Python-first ecosystem:** more tutorials, libraries, and earliest provider SDK support are in Python.
- **Fast-moving APIs:** Spring AI went from 2023 launch → 1.0 GA (May 2025) → 2.x now; LangChain4j also went through rapid 0.x → 1.x changes, so upgrade notes matter [10][11].
- **Java verbosity:** a quick prompt experiment is shorter in Python; Java needs records, builders, and dependencies.
- **Java-only features may lag** for brand-new model capabilities.
- **Evaluation is not solved:** LLM-as-a-Judge has verbosity bias (longer answers scored better), position bias, self-preference, and weak score reproducibility [10].
- **Extra setup if the app is not already Spring:** LangChain4j is lighter outside Spring, but still adds concepts [11].
- **Still need prompt craft:** using Java does not fix a bad prompt; system/context/few-shot design and success criteria are still required [9][18].

## 7. Why We Needed AI engineering in Java

- **The original problem:** Python was the first language for AI/ML applications, but businesses run their systems on Java and Spring. That created a gap: how to add GenAI without rewriting the stack or learning a new language [15].
- **2022–2023 trigger:** the ChatGPT boom produced many Python and JavaScript LLM libraries, with no Java counterparts; LangChain4j started in early 2023 specifically to fix that [11][14].
- **Spring's answer:** Spring AI was launched at SpringOne in Las Vegas in 2023, reached 1.0 GA on 20 May 2025, and is now at 2.x [9][10].
- **Architecture decision:** the alternative was a Python sidecar service for every LLM call — fine for a prototype, a liability in production because of two processes, two pipelines, and harder governance [15].
- **Standardization:** MCP appeared in November 2024 and gave all languages a common way to expose tools and prompts; Java/Spring support arrived quickly and was donated upstream [10].
- **For my case:** a prompt enhancer is small, synchronous, and lives next to the code that needs the prompt. Keeping it in Java gives typed output, tests, config, and deployment in the same system — which is exactly why this approach exists.

## Phrase meanings (B1+)

- **Application framework** — a ready-made structure you build your app inside, instead of writing all plumbing yourself.
- **Portability** — ability to move the same code to a different provider/model with little or no change.
- **Abstraction** — hiding provider-specific details behind one simple interface.
- **Structured output** — model answer forced into a defined format (e.g. JSON matching a schema).
- **Schema** — the written description of the allowed fields and types of a response.
- **Deterministic** — same input gives nearly the same output every time.
- **Observability** — being able to see latency, tokens, errors, and traces while the app runs.
- **Telemetry** — the measured data (metrics, logs, traces) sent from the running app.
- **Hallucination** — a confident answer that is not supported by the facts given.
- **Sidecar** — a separate helper service running beside the main app.
- **Vendor lock-in** — being stuck with one provider because the code depends on its private API.
- **Interceptor** — code that runs before/after the main call to modify the request or response.
- **Dependency injection** — framework supplies an object to a class instead of the class creating it.
- **Orchestration** — coordinating several steps/calls into one workflow.
- **Inference** — running a trained model to produce an answer (not training it).
- **Fine-tuning** — extra training of a model on your own data.
- **Governance** — rules and controls for what data/APIs the AI layer may use.
- **RAG (Retrieval-Augmented Generation)** — fetching your own documents first and giving them to the model as context before it answers.
- **Interpolate / render a template** — replacing placeholders like `{goal}` with real values.

## References

1. Spring AI project page — https://spring.io/projects/spring-ai
2. Spring AI Reference — Getting Started — https://docs.spring.io/spring-ai/reference/getting-started.html
3. Spring AI Reference — Prompts — https://docs.spring.io/spring-ai/reference/api/prompt.html
4. Spring AI Reference — ChatClient API — https://docs.spring.io/spring-ai/reference/api/chatclient.html
5. Spring AI Reference — Output Converters / Structured Output — https://docs.spring.io/spring-ai/reference/api/structured-output/converters.html
6. Spring AI Reference — Advisors API — https://docs.spring.io/spring-ai/reference/api/advisors.html
7. Spring AI Reference — Model Evaluation / Testing — https://docs.spring.io/spring-ai/reference/api/testing.html
8. Spring AI Reference — LLM-as-a-Judge — https://docs.spring.io/spring-ai/reference/guides/llm-as-judge.html
9. Spring Blog — Prompt Engineering Techniques with Spring AI (14 Apr 2025) — https://spring.io/blog/2025/04/14/spring-ai-prompt-engineering-patterns
10. Spring Blog — Spring AI 1.0 GA Released (20 May 2025) — https://spring.io/blog/2025/05/20/spring-ai-1-0-GA-released
11. LangChain4j — Introduction — https://docs.langchain4j.dev/intro
12. LangChain4j — AI Services — https://docs.langchain4j.dev/tutorials/ai-services
13. LangChain4j — Structured Outputs — https://docs.langchain4j.dev/tutorials/structured-outputs
14. Inside Java (Oracle) — Evolution of Java Ecosystem for Integrating AI (29 Jan 2025) — https://inside.java/2025/01/29/evolution-of-java-ecosystem-for-integrating-ai
15. VMware Tanzu Blog — Spring AI Enables Quick Delivery of Intelligent Apps in Java — https://blogs.vmware.com/tanzu/spring-ai-enables-quick-delivery-of-intelligent-apps-in-java
16. OpenAI — Prompt engineering guide — https://platform.openai.com/docs/guides/prompt-engineering
17. OpenAI — Structured model outputs — https://platform.openai.com/docs/guides/structured-outputs
18. Anthropic — Prompt engineering overview — https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview
19. Splunk — What is AI Engineering? — https://www.splunk.com/en_us/blog/learn/ai-engineering.html
20. Model Context Protocol — Introduction — https://modelcontextprotocol.io/introduction
