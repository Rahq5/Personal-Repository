# C1 — System Context: Prompt Enhancer

**Repository:** https://github.com/netr/promptenhancer  
**Diagram:** `c1-system-context.svg`

## Purpose
This diagram shows the Prompt Enhancer in its environment: who uses it and which external system it depends on. It provides the highest-level (C4 Level 1) view.

## Elements

| Element | Type | Description |
|---|---|---|
| Prompt Author | Person | A user who wants to improve a prompt for an LLM. They provide an original prompt and receive feedback. |
| Prompt Enhancer | Software System (Python CLI) | A small tool/experiment that evaluates a prompt (score and reason), generates an enhanced prompt, and lists enhancements. Intended to return structured JSON output. |
| OpenAI Chat Completions API | External Software System | The LLM service (e.g. GPT-4, as noted in the repo) used to analyze and rewrite the prompt. |

## Relationships

| From | To | Description |
|---|---|---|
| Prompt Author | Prompt Enhancer | Submits an original prompt and receives the enhanced prompt, score, reason, and list of enhancements. |
| Prompt Enhancer | OpenAI Chat Completions API | Sends chat messages (system instructions + user prompt) over HTTPS/JSON using an API key and receives the model’s structured response. |

## Notes
- The repository describes this as an experiment; reliable structured output was noted as working best with GPT-4.
- Configuration such as the OpenAI API key is expected via environment variables (e.g. `.env`), but secrets are not shown as separate systems at this level.
