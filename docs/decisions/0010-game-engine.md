# ADR-0010: Game engine selection policy

- **Status:** Accepted
- **Date:** 2026-05-03
- **Deciders:** Architecture Team
- **Tags:** games, engine, unity, unreal, godot

## Context

Game engine selection is the most consequential architectural decision for any game project. The engine determines the scripting language, the asset pipeline, the deployment targets, the hiring pool, the licensing cost, the physics model, the render quality ceiling, and the size of the available community and asset store. Changing engines after significant development has begun is effectively a full rewrite.

The three viable cross-platform game engines in 2026 are Unity, Unreal Engine, and Godot. Each represents a fundamentally different trade-off. This ADR establishes a default and the criteria for choosing an alternative so that teams make this decision deliberately rather than by default or habit.

## Decision

The default game engine is **Godot** for new projects where:
- The project is 2D-primary or stylised 3D (not photorealistic)
- The team is small (< 8 people) or includes engineers who value open-source and code-centric workflows
- Licensing risk is a concern

**Unity** is the recommended engine when:
- Cross-platform reach (mobile iOS/Android + PC + console) is essential and the team has Unity expertise
- The Unity asset store provides significant value for the project's content pipeline
- The team has confirmed the current Unity licensing terms are acceptable for the project's commercial model

**Unreal Engine** is the recommended engine when:
- Photorealistic rendering quality (Lumen, Nanite) is a primary requirement
- The project is AAA-quality PC or console targeted
- The team has experienced Unreal / C++ engineers

## Alternatives considered

### Option A: Godot (chosen as default)
- **Pros:** MIT licensed — zero runtime fees and no licensing risk. Fully open-source with community governance (no single vendor can change terms). Lightweight and fast iteration cycle. Excellent 2D engine. C# and GDScript support. Growing community and improving 3D capabilities. Free to use for any project at any revenue level.
- **Cons:** 3D rendering quality ceiling is lower than Unreal (no Lumen/Nanite equivalent). Asset store is smaller than Unity's. Console exporting is less mature. Fewer enterprise integrations (analytics, ads, LiveOps) than Unity Gaming Services.

### Option B: Unity
- **Pros:** Largest game engine community and asset store. Excellent cross-platform support (25+ platforms including all major consoles). Extensive tooling for mobile (Unity Ads, IAP, Analytics). Large hiring pool. Strong AR/VR support.
- **Cons:** Controversial runtime fee policy change in 2023 (subsequently partially reversed); licensing terms require monitoring. Proprietary — Epics or Unity can change terms unilaterally. Higher cost for teams that need Unity Pro or Enterprise. Editor performance can be slow on large projects.

### Option C: Unreal Engine
- **Pros:** Industry-leading rendering (Lumen global illumination, Nanite virtualised geometry). Free until $1M gross revenue (5% royalty above). Widely used in AAA games, film VFX (Virtual Production), and architectural visualisation. Blueprint visual scripting lowers the barrier for designers.
- **Cons:** C++ primary — higher engineering complexity and compile times. Steeper learning curve. Resource-intensive editor (requires high-spec hardware). Overkill for 2D, casual, or mobile-primary projects. 5% royalty above revenue threshold can be significant.

## Consequences

**Positive**

- Teams selecting Godot avoid licensing risk entirely — MIT licence means no runtime fees and no future term changes
- Clear decision criteria prevent engine selection from being a "biggest name wins" decision
- Teams that need Unreal or Unity have explicit criteria to justify the selection

**Negative / trade-offs**

- Godot's 3D capabilities are improving rapidly but trail Unreal; teams choosing it for complex 3D must evaluate the current state, not the state at the time of this ADR
- Unity's large community and asset store represent genuine value that this ADR may underweight for some projects

**Follow-up actions**

- [ ] Review Godot's 3D readiness annually — this ADR may need revision as Godot 4.x matures
- [ ] Confirm current Unity licensing terms before every new Unity project start — terms have changed historically
- [ ] Document engine choice rationale for each project in the project's architecture notes
