# Systems & Infrastructure

Low-level software where performance, memory, and hardware proximity matter. Includes operating system components, drivers, databases, compilers, network stacks, and embedded firmware.

## When to look here

- The constraint is hardware (memory, latency, power, real-time deadlines)
- You are building infrastructure that other software runs on
- Garbage collection or interpreter overhead is unacceptable

## Patterns in this category

| Pattern | Status | Best for |
|---|---|---|
| [High-performance Network Service](high-performance-network-service.md) | Trial | Proxies, load balancers, custom protocols |
| [Embedded Firmware](embedded-firmware.md) | Adopt | IoT devices, hardware controllers |
| [Database Engine / Storage Layer](database-engine.md) | Assess | Specialised data stores; rare to build from scratch |
| [Custom OS Component / Kernel Module](custom-os-component.md) | Hold | Almost never the right answer; consider eBPF first |

## Default tech stack

- **Languages:** Rust (modern default), C, C++, Zig (assess), Go (for network services that don't need C-level perf)
- **Build:** CMake, Cargo, Bazel
- **Testing:** Property-based testing (proptest, QuickCheck), fuzzing (AFL, libFuzzer), formal methods for critical sections

## LLM-development fit

Lower than other categories. Less training data, mistakes are expensive, concurrency bugs are hard to catch by inspection. Useful for boilerplate (parsers, serialisation), but architectural decisions still need an experienced human.
