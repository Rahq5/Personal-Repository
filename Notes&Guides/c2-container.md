# C2 — Container Diagram: Prompt Enhancer

**Repository:** https://github.com/netr/promptenhancer  
**Diagram:** `c2-container.svg`

## Purpose
This diagram zooms into the Prompt Enhancer system to show its main containers (applications/modules/configuration) and how they collaborate. It corresponds to C4 Level 2.

## Elements

| Element | Type | Technology / Location | Description |
|---|---|---|---|
| Prompt Author | Person | — | Runs the tool and supplies an original prompt. |
| CLI Application | Container | Python (`main.py`) | Entry point that drives execution: takes input, invokes enhancement logic, and outputs the result. |
| Prompt & Enhancement Logic | Container | Python (`prompt.py`) | Holds prompt-engineering instructions/templates and structures the request/expected response format (score, reason, enhanced prompt, enhancements). |
| Configuration | Container | `.env` / environment variables (e.g. `.env.example`) | Provides runtime configuration such as the OpenAI API key. |
| OpenAI API | External System | Chat Completions API (GPT-4 noted as reliable) | External LLM service that performs prompt analysis and rewriting. |

## Relationships

| From | To | Description |
|---|---|---|
| Prompt Author | CLI Application | Runs the program and provides the original prompt. |
| CLI Application | Prompt & Enhancement Logic | Calls logic to build the enhancement request and handle the formatted result. |
| CLI Application | Configuration | Loads API key and runtime settings. |
| Prompt & Enhancement Logic | Configuration | Uses configured parameters as needed for requests. |
| Prompt & Enhancement Logic / CLI | OpenAI API | Sends system/user messages via HTTPS/JSON and receives a structured response. |
| Prompt & Enhancement Logic | CLI Application | Returns the parsed/structured enhancement output for display. |

## Notes
- The repo is small (few files). "Containers" here represent logical runtime modules within a single Python CLI rather than separately deployed services.
- Dependencies are listed in `requirements.txt`.
