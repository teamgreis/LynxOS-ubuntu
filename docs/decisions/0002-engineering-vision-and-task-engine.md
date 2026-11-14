# ADR 0002: Engineering Vision and Task Engine Boundaries

- **Status:** Accepted as project requirements; implementation details remain subject to prototypes.
- **Date:** 2026-11-14

## Context

LynxOS has explicit product ambitions: exceptional security, very low resource consumption, high performance, compactness, modern capabilities and an outstanding desktop experience. The minimum target remains 4 GB RAM and 64 GB storage, with a complete LynxOS desktop on an Ubuntu LTS foundation.

A system-level Task Engine is desired to coordinate workloads intelligently. A second userspace scheduler that fights the Linux scheduler would add complexity, overhead and potential instability.

## Decisions

1. Treat the Engineering Vision 001 as a binding product requirement and quality direction.
2. Security and correctness are hard constraints; performance optimizations may not weaken them.
3. Require measurable, reproducible evidence for claims about speed, memory, storage, energy and security.
4. Develop the Task Engine as a bounded userspace workload coordinator for Generation 001.
5. Keep the Linux kernel scheduler authoritative for actual CPU scheduling.
6. Use supported Linux interfaces such as cgroup v2, systemd scopes/slices and PSI for initial observation and controlled resource policy.
7. Start the Task Engine in observe-only mode. Resource control is enabled only after deterministic policy, safety and performance testing.
8. Make the engine explainable, privacy-preserving, fail-safe and optional until validated.
9. Build a cohesive first-party desktop design system with accessibility and UI performance as release requirements.
10. Do not require always-on AI inference for the Task Engine. Any predictive intelligence must demonstrate net benefit within strict resource budgets.

## Consequences

Positive:
- Establishes a clear standard for product quality.
- Avoids competing with the Linux scheduler.
- Makes Task Engine behavior testable and reversible.
- Protects low-resource hardware from an optimization service that itself consumes excessive resources.
- Connects visual design, accessibility and performance from the beginning.

Trade-offs:
- The Task Engine cannot guarantee absolute CPU scheduling behavior outside Linux's supported interfaces.
- The desktop design system and shell are substantial engineering work.
- Targets may need adjustment after real hardware measurements.

## Revisit when

Revisit Task Engine policy or its control interfaces if prototypes show overhead, starvation, security risk, unacceptable latency or no measurable net benefit. Revisit product budgets only through documented measurements and an explicit decision.
