# AI / LLM-Integrated Systems

Systems whose primary value comes from a Large Language Model or other foundation model in the loop. The model is treated as a software component with its own engineering discipline.

## When to look here

- The product feature would be impossible (or absurdly expensive) without an LLM
- Natural language is the primary input, output, or both
- The system needs to reason over unstructured documents

## Patterns in this category

| Pattern | Status | Best for |
|---|---|---|
| [RAG (Retrieval-Augmented Generation)](rag.md) | Adopt | Q&A over a private corpus, knowledge assistants |
| [Conversational Assistant / Chatbot](conversational-assistant.md) | Adopt | Customer support, internal help desk |
| [Agentic Workflow](agentic-workflow.md) | Trial | Multi-step tasks where the model uses tools |
| [Document Processing / Extraction](document-processing.md) | Adopt | Invoices, contracts, forms; replaces traditional OCR + rules |
| [Copilot / In-app Assistant](copilot-in-app.md) | Trial | Embedded assistance within an existing product |
| [Fine-tuned Domain Model](fine-tuned-model.md) | Assess | High-volume narrow tasks where prompt engineering plateaus |

## Default tech stack

- **Model providers:** Anthropic (Claude), OpenAI, Google (Gemini), AWS Bedrock (multi-vendor)
- **Orchestration:** LangGraph, Vercel AI SDK, or hand-rolled (often the right answer per Armin Ronacher)
- **Vector store:** Postgres + pgvector (default), Pinecone, Weaviate, Qdrant
- **Evaluation:** Braintrust, LangSmith, custom eval harness with `pytest`
- **Observability:** Langfuse, Helicone, Datadog LLM Observability
- **Guardrails:** Provider-side moderation + custom output validation; assume hallucination will occur

## LLM-development fit

A strange case: LLMs are good at writing code that calls other LLMs, but the resulting system inherits all the unpredictability problems. Build evaluation harnesses before scaling. Design every workflow assuming the model will return wrong output some percentage of the time.
