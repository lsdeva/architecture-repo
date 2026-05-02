# ADR-0005: LLM orchestration approach — hand-rolled over frameworks for most patterns

- **Status:** Accepted
- **Date:** 2026-05-02
- **Deciders:** Architecture Team
- **Tags:** ai, llm, orchestration, langchain, langgraph

## Context

Every LLM-integrated system needs code that sequences prompts, calls tools, manages context, and handles errors. The question is whether to write this code directly (hand-rolled) or to adopt a framework like LangChain, LangGraph, LlamaIndex, or the Vercel AI SDK.

This decision is non-obvious because the frameworks appear to reduce boilerplate but introduce their own abstraction costs. Armin Ronacher (Flask author, Sentry co-founder) wrote in 2023 that he regretted adopting LangChain early: *"LangChain is confusing and adds abstractions where there are none needed."* Simon Willison (Datasette, Django co-creator) consistently advocates writing LLM application code directly against provider APIs. At the same time, LangGraph addresses a genuinely hard problem — stateful multi-agent graphs with checkpointing — that hand-rolled code handles poorly.

The right answer depends on the pattern. This ADR captures the policy across patterns.

## Decision

We will use **hand-rolled Python** as the default for all LLM patterns except Agentic Workflows.

Specifically:
- **RAG, Conversational Assistant, Document Processing, Copilot:** Hand-rolled Python calling provider SDKs (`anthropic`, `openai`) directly. LlamaIndex is acceptable for RAG pipelines where its index abstraction provides genuine value, but use only the retrieval components — not the full stack.
- **Agentic Workflows:** LangGraph is the default for multi-step, multi-tool agents with persistent state. The graph abstraction and built-in checkpointing are genuinely hard to replicate correctly by hand.
- **Fine-tuned models:** No orchestration framework needed — inference calls go directly to the vLLM OpenAI-compatible API.

## Alternatives considered

### Option A: Hand-rolled Python calling provider SDKs (chosen for most patterns)
- **Pros:** Direct, transparent, debuggable. No hidden magic — every prompt, every API call, every retry is visible. No breaking changes from framework upgrades. Teams own the abstractions they need and nothing more. Provider SDK ergonomics (streaming, tool use, structured output) have matured to the point where frameworks add little convenience.
- **Cons:** More boilerplate for common patterns (retry logic, context window management, streaming response handling). Teams must implement their own token counting, history truncation, and caching. Each developer re-solves the same problems without shared library code.

### Option B: LangChain
- **Pros:** Large community, many integrations, extensive documentation.
- **Cons:** Abstractions do not match the underlying model concepts well — `Chain`, `Agent`, `Memory`, and `Retriever` are LangChain's models of these concepts, not the LLM providers'. Debugging a LangChain pipeline requires understanding multiple abstraction layers. Breaking changes in minor versions have historically been frequent. For simple patterns, the framework adds more cognitive load than it saves.

### Option C: LangGraph (chosen for Agentic Workflows)
- **Pros:** Directly addresses the hard problems of agentic systems: state persistence across steps, branching execution graphs, human-in-the-loop interrupts, and parallel node execution. The graph mental model maps well to multi-step agentic tasks. Built-in checkpointing via configurable state stores. Actively maintained by the LangChain team with a cleaner API than LangChain itself.
- **Cons:** Adds a framework dependency. Learning curve for the graph/node/state model. Overkill for linear workflows (A → B → C with no branching).

### Option D: LlamaIndex
- **Pros:** Purpose-built for RAG; excellent abstractions for document loading, chunking, indexing, and retrieval. Handles many vector store backends with a consistent API. Strong community and documentation.
- **Cons:** The full LlamaIndex stack (LLM abstraction + index + query engine) is heavier than needed for teams that only want the retrieval layer. Acceptable to use selectively for the index/retrieval components.

### Option E: Vercel AI SDK
- **Pros:** Best-in-class frontend streaming integration (React hooks, `useChat`, `useCompletion`). TypeScript-first with good type safety. Works well for Next.js-based copilot frontends.
- **Cons:** TypeScript only — not applicable to Python backend patterns. Primarily a frontend/API-route tool, not a full orchestration framework.

## Consequences

**Positive**

- LLM application code is readable and debuggable by any Python engineer, without framework knowledge
- Provider SDK upgrades are self-contained; no framework upgrade chain
- Prompts and context management are explicit — easier to reason about token budgets and cost
- LangGraph used only where its graph abstraction genuinely earns its weight (agentic workflows)

**Negative / trade-offs**

- Teams must implement common utility code (retry with exponential backoff, context window truncation, streaming error handling) — create an internal shared library for this rather than re-implementing per project
- No built-in multi-vector-store portability — switching from pgvector to Qdrant requires rewriting retrieval queries (this is acceptable; the portability abstraction is rarely needed)
- LangGraph adds a framework dependency to all agentic workflows; its API has changed across versions — pin the version

**Follow-up actions**

- [ ] Create an internal `llm_utils` Python package with: retry logic, streaming helper, token counter, context window truncation
- [ ] Publish standard prompt templates for RAG, chatbot system prompts, and extraction schemas as a shared internal resource
- [ ] Pin LangGraph version in the agentic workflow project template; schedule quarterly review of the pin
