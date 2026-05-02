# ADR-0006: Anthropic Claude as the default LLM provider

- **Status:** Accepted
- **Date:** 2026-05-02
- **Deciders:** Architecture Team
- **Tags:** ai, llm, provider, anthropic, openai

## Context

Every LLM-integrated pattern in this catalog requires choosing a model provider. The choice affects: output quality on different task types, cost per token, latency, data retention and compliance posture, API ergonomics, and model availability under load. Changing providers after a product ships requires rewriting prompts (prompt sensitivity differs across models), re-evaluating outputs, and potentially re-embedding corpora.

The realistic candidates for a production system in 2026 are Anthropic (Claude family), OpenAI (GPT-4o family), Google (Gemini family), and AWS Bedrock (multi-vendor managed). Self-hosted open weights (Llama, Mistral) are covered by the Fine-tuned Domain Model pattern.

## Decision

We will use **Anthropic Claude** as the default provider, with the following model tier policy:

| Use case | Default model | Notes |
|---|---|---|
| Complex reasoning, agentic tasks | Claude Sonnet 4.6 | Best tool-use accuracy and extended thinking |
| Interactive generation (chatbot, copilot) | Claude Haiku 4.5 | Fastest and cheapest; sufficient for most chat |
| Document extraction (vision) | Claude Sonnet 4.6 | Multimodal accuracy justifies the cost |
| Batch / offline processing | Claude Haiku 4.5 via Batch API | 50% cost reduction vs synchronous API |

Teams may use OpenAI or Google models for specific features where evaluation demonstrates a material quality advantage on that task. AWS Bedrock is recommended as the access layer for multi-provider strategies or when cloud-consolidated billing is required.

## Alternatives considered

### Option A: Anthropic Claude (chosen)
- **Pros:** Strongest instruction-following on complex, multi-constraint prompts. Industry-leading tool use accuracy (critical for agentic workflows). Extended thinking (Claude 3.5+ Sonnet) for tasks requiring step-by-step reasoning. Prompt caching with `cache_control` blocks reduces cost by up to 90% and latency by ~30% on repeat-context patterns (chatbots, copilots). Business Associate Agreements (BAA) available for HIPAA. EU data residency via Bedrock. Context window up to 200k tokens. Native `instructor`-compatible structured output via tool use.
- **Cons:** No image generation capability (no equivalent to DALL-E). OpenAI's embedding models (`text-embedding-3-small`) are better-established for RAG pipelines — we use them for embeddings regardless of this decision. Slightly higher cost than GPT-4o-mini for equivalent quality tasks.

### Option B: OpenAI (GPT-4o family)
- **Pros:** Largest installed base — most third-party tooling targets OpenAI first. Best-in-class embedding models (`text-embedding-3-small`, `text-embedding-3-large`). Strong multimodal (image + text) performance. JSON mode and structured output via `response_format`. DALL-E for image generation. The OpenAI API is the de-facto standard interface that vLLM, LiteLLM, and local inference servers emulate.
- **Cons:** Data retention policy has historically been less transparent than Anthropic's; enterprise tier required for zero-retention guarantees. No equivalent to Anthropic's prompt caching for reducing repeat-context costs. GPT-4o instruction-following on complex, multi-constraint prompts is measurably weaker than Claude Sonnet on internal evaluations. Rate limits on shared tier are less predictable.

### Option C: Google Gemini (1.5 / 2.0 family)
- **Pros:** Largest context window (1M tokens for Gemini 1.5 Pro) is useful for whole-codebase or whole-document tasks. Native Google Workspace integration. Competitive pricing. Gemini 2.0 Flash is the fastest model in its class.
- **Cons:** Gemini API stability has been less consistent than Anthropic or OpenAI in our experience. Tool use accuracy lags Claude Sonnet on complex agentic benchmarks. Weaker European data residency story compared to Anthropic/Bedrock. Less mature third-party ecosystem.

### Option D: AWS Bedrock (multi-vendor)
- **Pros:** Single AWS billing and IAM integration across Claude, Titan, Mistral, and Llama models. No direct internet call to provider APIs — traffic stays within AWS VPC. Useful for organisations with AWS-consolidated compliance requirements. Guardrails feature (content filtering) at the infrastructure layer.
- **Cons:** Adds latency vs direct API calls (~20–50 ms). Model availability on Bedrock lags direct provider availability (new Claude models appear on Bedrock weeks after the direct API). Harder to test locally — requires AWS credentials even for development.

## Consequences

**Positive**

- Single provider for most patterns reduces the cognitive overhead of maintaining multiple API keys, rate limit quotas, and billing relationships
- Prompt caching materially reduces cost and latency for chatbot and copilot patterns — not available from all providers
- BAA availability makes Anthropic viable for HIPAA use cases without switching providers per use case
- Extended thinking enables complex reasoning in agentic workflows without prompt-engineering workarounds

**Negative / trade-offs**

- OpenAI embedding models (`text-embedding-3-small`) remain the default for RAG pipelines — this means two provider relationships when RAG is involved
- Prompt sensitivity differs between Claude and GPT — prompts written for one model require testing when migrated to the other; never assume portability
- Vendor dependency: if Anthropic degrades service or changes pricing significantly, migrating all prompts is a multi-week engineering effort

**Follow-up actions**

- [ ] Abstract the LLM client behind a thin interface (`LLMClient` with `complete()` and `stream()`) so provider swaps require a one-file change
- [ ] Establish a Bedrock account as a fallback access path for Claude models (maintains provider choice while gaining AWS billing integration)
- [ ] Re-evaluate this decision annually: the LLM provider landscape changes faster than any other technology in this catalog
- [ ] Document the BAA process with Anthropic for any new project handling HIPAA-regulated data
