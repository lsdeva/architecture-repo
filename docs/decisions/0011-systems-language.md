# ADR-0011: Rust as the default language for new systems and infrastructure code

- **Status:** Accepted
- **Date:** 2026-05-03
- **Deciders:** Architecture Team
- **Tags:** systems, infrastructure, rust, c, cpp, language

## Context

The Systems & Infrastructure category covers network services, embedded firmware, storage engines, and OS components. These systems share constraints that application code does not: no garbage collector, manual memory management, strict latency requirements, and high blast radius for bugs (system crashes, data corruption, security vulnerabilities). The language choice for this category is non-obvious and has long-term implications for security, maintainability, and hiring.

Historically, C and C++ were the only choices. Rust has reached production maturity since 2015, is now accepted in the Linux kernel (6.1+), and is explicitly backed by the NSA, CISA, and the US White House ONCD as the preferred memory-safe alternative to C/C++. The question is no longer whether Rust is viable, but when to prefer it over C/C++ for new projects.

## Decision

We will default to **Rust** for all new systems and infrastructure code where the team has or can develop Rust expertise.

**C remains acceptable** for:
- Embedded firmware targeting MCUs with mature vendor HAL/RTOS support in C and limited Rust embedded ecosystem support for that specific target
- Extensions to existing C codebases where rewriting is not feasible
- Safety-critical systems (automotive, medical) where the industry toolchain and certification infrastructure is still C-centric

**C++ remains acceptable** for:
- Extensions to existing C++ game engine or system codebases
- Projects where the team has deep C++ expertise and the codebase predates this ADR

Go remains the default for application-layer backend services (see ADR-0002). This ADR applies only to the Systems & Infrastructure category.

## Alternatives considered

### Option A: Rust (chosen)
- **Pros:** Memory safety guaranteed by the borrow checker at compile time — eliminates entire classes of vulnerability (use-after-free, buffer overflow, data races) that account for ~70% of CVEs in systems code (Microsoft, Google, Android security teams all report similar figures). Performance equivalent to C/C++ — no garbage collector, zero-cost abstractions. Growing ecosystem: `tokio` (async networking), `embassy` (embedded async), `prost` (Protobuf), `serde` (serialisation). Accepted in Linux kernel (6.1+). Cargo package manager and build system is significantly better than CMake/Autotools. Active and friendly community.
- **Cons:** Steep learning curve — the borrow checker rejects code patterns that C/C++ engineers consider normal; expect 2-3 months before productivity matches C proficiency. Embedded ecosystem is maturing but less complete than C for some MCU families. Compile times are slower than C. Not yet accepted in safety-critical certification standards (ISO 26262, IEC 62304) at the same level as C — though this is changing.

### Option B: C
- **Pros:** Universal — every MCU has a C compiler; every kernel accepts C; every safety standard explicitly supports C (with MISRA). Vast existing codebases and tooling. Maximum hiring pool for embedded.
- **Cons:** No memory safety — use-after-free, buffer overflows, and data races require external tools (Valgrind, ASAN, Coverity) to detect; many are never found in testing and reach production. MISRA C eliminates some dangerous patterns but is a process control, not a language guarantee. Increasingly difficult to justify for new code given Rust's demonstrated safety properties.

### Option C: C++
- **Pros:** More expressive than C; RAII helps with resource management; standard library is richer. Widely used in game engines, HPC, and financial systems.
- **Cons:** Subset of C's memory safety problems persist even with modern C++ (C++17/20); undefined behaviour is pervasive and subtle. "Modern C++" best practices require significant discipline to apply consistently across a team. Extremely complex language specification. No borrow checker equivalent — shared ownership still requires careful discipline.

### Option D: Go
- **Pros:** Easy to learn, excellent standard library, strong concurrency model, fast compilation.
- **Cons:** Garbage collector introduces latency spikes unsuitable for real-time systems. Runtime overhead rules out most embedded targets. Already the default for application-layer services (ADR-0002) — this ADR explicitly addresses the systems tier.

## Consequences

**Positive**

- New systems components have a dramatically lower CVE rate due to memory safety guarantees
- The borrow checker enforces correct concurrency patterns at compile time — data races are impossible in safe Rust
- The ecosystem for async networking (`tokio`), embedded (`embassy`), and systems programming has matured to production quality
- CISA/NSA guidance aligns with this decision — reduces compliance/audit risk for government-adjacent projects

**Negative / trade-offs**

- Teams must invest in Rust learning; expect slower initial velocity on the first project (typically 30–50% slower until the borrow checker becomes intuitive)
- Unsafe Rust (`unsafe {}` blocks) can violate the safety guarantees; code review must be especially rigorous for any `unsafe` block
- For embedded targets where the Rust HAL is immature, C with MISRA compliance remains more practical
- Safety-critical certification (automotive, medical) is not yet straightforward in Rust; teams targeting those domains should consult the current state of the Ferrocene/Rust safety toolchain

**Follow-up actions**

- [ ] Identify a "first Rust project" in the systems team to build expertise; choose a non-critical component
- [ ] Document approved crates for production use (tokio, serde, anyhow, thiserror) — not all crates have the same quality bar
- [ ] Review `unsafe` code in all Rust PRs as a mandatory policy; never approve `unsafe` without explicit justification
- [ ] Re-evaluate for embedded firmware annually as the embedded Rust ecosystem (Embassy, probe-rs) matures
