# Roadmap

## v0.11.1-draft (2026-09-22) — Current draft

- [x] Normative vocabulary through v0.11.1: executable/high-level/memory types, two-domain value model, IR namespace, Sys registry, trace op, version gate
- [x] Concept document (`SIR_Concept.stf` v0.2.0-draft)
- [x] Examples (`examples/`, `.sir` with `Context DocumentVersion` headers)
- [x] Paired reference compiler 0.2.1 (developed separately; fixpoint proven)

## v0.1.2-draft (2026-08-21) — First vocabulary release (draft)

- [x] Normative vocabulary: SIR properties, executable/high-level/memory types
- [x] Execution & memory rules: dual-phase, isolation, ABI compliance
- [x] Security & trust boundaries: untrusted-code sandboxing
- [x] Control flow & error handling: structural flow, monadic errors
- [x] Concept document (`SIR_Concept.stf`)
- [x] Minimal example (`examples/`)

## v0.2 (planned)

- [ ] Reference tree-walk interpreter prototype (in this repository)
- [ ] Minimal viable Op vocabulary updates as needed
- [ ] Structural concurrency (AsyncGroup) semantics (currently reserved)
- [ ] Conformance test suite and vectors

## v1.0 (planned)

- [ ] Stabilize the normative vocabulary
- [ ] Self-hosted compiler experiment (SIR written in SIR)
- [ ] Formal trust-boundary verifier

## Continuous

- [ ] Community feedback via GitHub Issues
- [ ] Independent implementations and lowering experiments
