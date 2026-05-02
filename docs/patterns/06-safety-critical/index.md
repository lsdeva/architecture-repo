# Safety-Critical Systems

Software where failure can cause loss of life, serious injury, environmental damage, or significant financial loss. Subject to regulatory certification.

!!! warning "Specialist domain"
    This catalog provides only a high-level overview. Do not begin a safety-critical project without a domain specialist. Standards (ISO 26262, DO-178C, IEC 62304, IEC 61508) impose specific process and documentation requirements far beyond what this catalog covers.

## When to look here

- Human safety is at stake (medical, automotive, avionics, industrial control)
- Regulatory certification is required before deployment
- Failure modes must be analysed and mitigated formally (FMEA, FTA)

## Patterns in this category

| Pattern | Status | Best for |
|---|---|---|
| Medical Device Software (TODO) | Adopt | IEC 62304 Class A/B/C software |
| Automotive ECU Software (TODO) | Adopt | ISO 26262 ASIL-rated components |
| Industrial Control System (TODO) | Adopt | IEC 61508 SIL-rated controllers |

## Default tech stack

- **Languages:** C (with MISRA), Ada/SPARK, Rust (increasingly accepted)
- **RTOS:** FreeRTOS, Zephyr, VxWorks, QNX, INTEGRITY
- **Verification:** Formal methods, MC/DC test coverage, static analysis (Polyspace, Coverity, Astrée)

## LLM-development fit

The worst fit of any category for autonomous LLM development — not because the code is harder, but because verification, traceability, and accountability requirements do not match LLMs' probabilistic nature. LLMs may assist with boilerplate, but a human owns and certifies every line.
