# C3 — Component Diagram: Prompt Enhancer

**Repository:** https://github.com/netr/promptenhancer  
**Diagram:** `c3-component.svg`

## Purpose
This diagram shows the key components inside the CLI/enhancement container and how they work together to produce an enhanced prompt. It corresponds to C4 Level 3.

## Scope
The scope is the internal logic of the Python CLI (primarily `main.py` and `prompt.py`) as inferred from the repository structure and README.

## Elements

| Element               | Type            | Source (inferred)           | Description                                                                                                                                                            |
| --------------------- | --------------- | --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Input / CLI Handler   | Component       | `main.py`                   | Accepts the original prompt from the user/run context and initiates the enhancement flow.                                                                              |
| System Prompt Builder | Component       | `prompt.py`                 | Contains the system instructions that guide the model to score the prompt, explain the score, enhance it, and list enhancements in a consistent (JSON-like) structure. |
| OpenAI Client         | Component       | `main.py` / API integration | Reads configuration (API key), assembles chat messages, and calls the OpenAI Chat Completions API (repo notes GPT-4 for reliable formatting).                          |
| Response Parser       | Component       | output handling logic       | Interprets the model response and extracts structured fields: original score, reason, enhanced prompt, and list of enhancements.                                       |
| Output Presenter      | Component       | `main.py`                   | Outputs the final structured result to the user (e.g., printed to console).                                                                                            |
| OpenAI API            | External System | External                    | Performs LLM inference and returns the generated enhancement content.                                                                                                  |

## Relationships

| From | To | Description |
|---|---|---|
| Input / CLI Handler | System Prompt Builder | Obtains system instructions/templates for the enhancement task. |
| Input / CLI Handler | OpenAI Client | Passes the original prompt and prepared messages for processing. |
| OpenAI Client | OpenAI API | Sends chat completion request over HTTPS with API key. |
| OpenAI Client | Response Parser | Provides the raw model response. |
| Response Parser | Output Presenter | Supplies parsed structured fields for display. |
| OpenAI Client | Output Presenter | May pass errors/status when requests fail (inferred). |

## Notes
- This is a conceptual component view based on a small codebase; exact function/class names may differ from these labels.
- The README highlights a large system prompt and reliance on GPT-4 for consistent structured JSON output.
