# Changelog

All notable changes to SIR (Semantic Intermediate Representation — STF application vocabulary for executable agent logic).

---

## Unreleased — SirTool showcase program

- New `examples/sir_tool.sir`: canonical formatter (`fmt`) and structural editor (`expand`, `insert`, `replace`, `delete`, `move`, `wrap`, `diff`) in one tool with stdin-prefix modes; framed protocol (`base`, `path`, `N`, target lines, payload); reuses the compiler Parse stack via Include; host/WASM parity proven per mode

## v0.11.1-draft (2026-09-22) — Raw float ops and Float tag pin

- New §6.13.6: raw float ops over i64-held binary64 bits (IR.F64Add/Sub/Mul/Div/Eq/Lt/Gt/FromI64/ToI64/Floor) under the unified i64 ABI; division by zero yields infinity; out-of-range/NaN/inf truncation traps loudly
- §7.6(f): portable Float carries type tag 6 (was declared without a number)

## v0.11.0-draft (2026-09-17) — Bulk/in-place collection semantics and float payload pin

- New §6.7.1: functional observable semantics of §6.7 unchanged; engines MAY share structure underneath but MUST document complexity of ListPush/ListConcat/MapSet; Core.StrJoinParts(parts) single-pass bulk join as the sanctioned anti-accum-loop op; Mut family (Core.ListPushMut/ListSetMut/MapSetMut) with a linear-use contract (aliased use is a structural error); implementation lands with the TD-021 batch, use before that is a load-time error
- §6.7: Core.StrParse float pin — decimal float forms MUST yield Float, never silently truncated Int
- §7.6(f): Float payload is IEEE-754 binary64 little-endian, preserved exactly; backends MUST NOT truncate Float to Int — refuse loudly until float lowering exists (matches the TD-014 silent-plus-loud closure)

## v0.10.3-draft (2026-09-06) — Polymorphic ListGet on strings

- §6.7: Core.ListGet(s, i) applied to a String returns the byte value (Int) at index i — binary parsers (ParseWasm over file bytes read as text) work without conversion; engines implement it in the interpreter and in sir_list_get
- §6.13.3: boxed twins IR.BitAnd/IR.BitOr/IR.ShiftL/IR.ShiftR for portable Core bit/shift Ops (unbox–op–rebox); IR.W* twins stay raw-only and MUST NOT receive boxes

## v0.10.2-draft (2026-09-04) — Canonical trace point

- New §7.2.2 Sys.Trace(Path): single canonical instrumentation Op for observability tooling; pure (no Capability, no bound value, no error); unquoted dotted path [a-z0-9_.]+, no brackets/quotes/whitespace
- Backends MUST strip Sys.Trace to zero instructions/bytes (instrumented and clean sources compile byte-identically); Sys.Trace is never error-aware and never participates in Result propagation
- §2.1 Op examples, §7.5 registry and §8 conformance (Trace Stripping) updated accordingly

## v0.10.1-draft (2026-08-26) — Semantic clarifications

- §6.3: division closure pinned — Int/Int yields Int truncated toward zero (i64.div_s-compatible); Float participates as Float
- §6.3: canonical rendering of Bool through Sys.Output fixed as 1 / 0 (engines MUST match)

## v0.10.0-draft (2026-08-25) — Completeness and honesty release (draft)

- Reserved types made explicit: AsyncGroup, Class, Type and the ABIMap contract node are marked RESERVED with the normative rule that engines treat them as inert structure until their semantics are specified — no declared type is left without a defined status
- Cross-level rule (§3) rewritten: level crossing happens only through Function boundaries and an explicit ABI contract; implicit reliance on another level's value representation or memory layout is forbidden; ABIMap structure itself stays unstandardized
- Origin property defined in §2.1 (values from the STF core trust model: Human, Agent, External, Application, Backend, System); Capabilities examples replaced with the real standard names (Sys.Input / Sys.Output / Sys.Storage / Mem.Read / MemPersistentWrite)
- Literal grammar normative (§6.2): quoted tokens are verbatim strings, case-insensitive true/false/null, decimal Int and Float forms, otherwise String; no other implicit coercions
- Result node shape formalized (§6.5): direct child of Function, Returns names the returned binding, Ok unwraps, Error propagates as the call's value
- New §8 Conformance Requirements: structural parsing, capability enforcement, bounded execution, monadic fidelity, deterministic composition, level discipline

## v0.9.0-draft (2026-08-25) — Full-surface specification release (draft)

- Two-Domain Value Model (§6.12): portable boxed domain vs raw linear-memory domain; explicit crossing operations (boxing constructors / unboxing accessors); no implicit mixing within a function body
- Low-Level / Linear-Memory Ops (§6.13): normative semantics of the IR.* namespace — raw arithmetic (two's-complement wrap), signed/unsigned division/remainder and comparisons, bitwise/logic ops, raw literals, bump allocation, width-parameterized little-endian load/store, module globals, host I/O boundary, raw identity binding
- AbstractionLevel made normative (§2.1): High = portable vocabulary only, Low = additionally IR.*, Mid reserved; one module — one level; level crossing via calls only
- Sys Op Registry (§7.5): channel ops (Sys.Input / Sys.Output / Sys.Storage.Read) with capabilities, sink bindings registry, no-silent-drop rule when no binding can be established
- Runtime ABI skeleton (§7.6): box layout, Bool realization on linear-memory targets, helper index contract, debox/rebox boundaries, data region — anchors fixed now, concrete layouts in Phase 8
- Op vocabulary alignment: Core.Sort added to List Ops; §6.4 reworded to "any pure Op binds via Returns" (Core.Bind removed as unimplemented); ABI.Unpack dropped from Op examples; IR namespace added to §2.1

## v0.8.0-draft (2026-08-24) — Memory regions and heap buffers

- §6.6 Memory Regions: graph region ("graph", persistent STF-Memory by NodeRef) vs buffer regions (linear byte-addressable cells); region "heap" = target linear memory; Width ∈ {1,2,4,8} selects byte/i32/i64 cells, little-endian, no alignment enforcement; Mem.Alloc("heap", Size) bump allocator without free; heap cursor starts at 1024 reserving the data region for string literals; bounds/capacity violations are structural RuntimeErrors surfaced via Result

## v0.7.0-draft (2026-08-23) — M2 self-hosting primitives

- §6.10.1–6.10.3: Core.Equals (structural equality), Core.TypeOf (type tag), Core.StrFind/Core.StrCount (lexing helpers)
- §6.10.4: low-level numeric abstraction — Core.BitAnd/BitOr/BitXor/ShiftL/ShiftR, Core.Id identity binding
- §2.1 Op examples extended with the new namespaces

## v0.6.0-draft (2026-08-22) — M2 language minimum

- Language extension for the self-hosting compiler phase: structural equality, type tags, lexing helpers (sections shipped as v0.7.0 above; introduced together with interpreter storage read and error context)

## v0.5.0-draft (2026-08-22) — Standard library

- §6.9 Standard Library (prelude): prelude mechanism, Ctrl.Call resolves Function names only, extended Core Op set (numeric/string/char/type predicates/booleans), library inventory (collections/strings/math/functional/result/dictutil/core_fn/io)

## v0.4.0-draft (2026-08-22) — Expressiveness for self-hosting

- List/Map as first-class composite values (§6.1); string and composite Ops (§6.7)
- §6.8 Functions & Calls: multiple named Functions, Ctrl.Call with positional argument binding, fresh Frame per call (TransientVar isolation, shared STF-Memory), recursion with bounded depth
- §7.2.1 Input/Output semantics: Sys.Input(Channel) / Sys.Output(Value)

## v0.3.0-draft (2026-08-21) — Broader execution core

- Monadic error propagation rules (§3, §5): fallible Ops yield Result, Error propagates without exceptions, hidden stack unwinding forbidden
- Structural control flow detail (§5): Branch condition/arms, Loop condition/body with bounded iteration
- Memory isolation rule sharpened (§3): TransientVar vs STF-Memory targets, MemPersistentWrite capability
- New arithmetic: Core.Neg, Core.Mod (§6.3)

---

## v0.1.2-draft (2026-08-21) — First vocabulary release (draft)

- Normative SIR vocabulary on top of STF core: SIR-specific properties (`AbstractionLevel`, `Op`, `Params`, `Returns`, `Capabilities`)
- Base executable types (`Module`, `Function`, `Instruction`, `Branch`, `Loop`, `AsyncGroup`, `Result`), high-level types (`Class`, `Type`), memory & ABI types (`TransientVar`, `ABIMap`)
- Execution & memory rules: dual-phase execution, memory isolation, cross-level ABI compliance
- Security & trust boundaries: untrusted-code sandboxing, trusted orchestration
- Control flow & error handling: structural control flow, monadic error propagation
- Referenced as an application vocabulary of STF core (normative by reference, no version pin); GrammarVersion 1.0.1

---

## Concept track

### v0.2.0-draft (2026-08-25) — Synced with the normative specification

- `SIR_Concept.stf`: ecosystem framing corrected — SIR is an application vocabulary of the STF core among others (SIL, STF-Memory, and future vocabularies such as configuration or frontend-generation languages), not a "final layer"; new Status Relative to the Specification section separates what became normative (structural control flow, Result monad, dual-phase execution, abstraction-level ladder, two-domain value model, reserved ABI contract node, native includes) from remaining research territory (AsyncGroup concurrency, dynamic ABI contracts); self-hosting noted as moved from research to engineering (compiler on SIR driving to bootstrap fixpoint)

### v0.1.1-draft (2026-08-21) — Research concept

- `SIR_Concept.stf`: STF as a structural AST for executable agent logic — design thesis, principles, conceptual model, operations, trade-offs, ecosystem closure, open questions
