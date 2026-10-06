# SIR — Semantic Intermediate Representation

**A structural intermediate representation for executable agent logic, built on STF.**

[![Specification](https://img.shields.io/badge/Spec-v0.11.1--draft-blue)](SIR_Specification.stf)
[![License](https://img.shields.io/badge/License-CC--BY--4.0-green)](LICENSE)
[![Code License](https://img.shields.io/badge/Code-Apache--2.0-green)](LICENSE-CODE)

## What is SIR?

SIR (Semantic Intermediate Representation) is an application vocabulary built on top of the **Semantic Text Format (STF)**. It uses STF as a direct Abstract Syntax Tree (AST) representation of executable logic, designed for intelligent agents and compilers rather than for human-readable source text.

The language problem — traditional programming languages were designed for humans: syntactic sugar, context-dependent grammars, formatting conventions. When LLMs generate code, they spend tokens predicting syntax and are prone to syntax hallucinations. Combining modules across languages is hard because their intermediate representations do not match.

SIR answers by representing code as a structural semantic tree. Syntax errors become structurally impossible; agents write structural nodes directly. Modules can exist at different levels of abstraction within the same semantic graph, bridging high-level logic and low-level execution.

## Why SIR?

- **Code is Data** — a SIR module is a valid STF document; agents read, write, and reason about it as a semantic tree, not raw text.
- **Explicit Abstraction** — modules declare their level (High, Low; Mid reserved) explicitly, so the compiler understands context without guessing.
- **Unified Memory Space** — executable code (SIR) and persistent state (STF-Memory) share one structural graph and addressing scheme.
- **Trust-Aware Execution** — SIR reuses the STF Trust Model; code execution authority is bounded by Origin. Untrusted code (Agent/Human) is structurally forbidden from side-effects.
- **Structural Determinism** — control flow and concurrency are explicit structural branches; no hidden state mutation or ambiguous jumps.

## SIR and STF

SIR is an **application vocabulary** of STF core. It reuses STF's structural grammar, identity, and trust model, and adds an executable vocabulary on top.

```text
STF core (syntax, object model, trust model)
 └── SIR (application vocabulary)
      ├── executable types (Module, Function, Instruction, Branch, Loop, AsyncGroup, Result)
      ├── high-level types (Class, Type)
       ├── memory & ABI types (TransientVar, ABIMap)
       └── Ops (Core.Add, Mem.Read, Ctrl.Call, Sys.Trace) + Capabilities
```

SIR is referenced as an extension of STF core — normative by reference, without version pinning. A conforming SIR document is, by construction, a valid STF document. STF core: [ais-space/stf](https://github.com/ais-space/stf), grammar `GrammarVersion 1.0.1`.

File conventions: SIR programs use the `.sir` extension, specification and concept documents use `.stf`. Every compilable SIR program carries a `Context` header declaring its SIR `DocumentVersion` (§2.5 of the specification).

## How it works (conceptually)

A SIR program is a semantic tree whose nodes represent functions, instructions, variables, and control-flow branches. Execution is dual-phase:

- **Phase 1 — Tree-Walk Interpretation:** the runtime interprets the STF AST directly for rapid agent prototyping.
- **Phase 2 — AOT Compilation:** the structural tree is lowered to a binary format for production performance.

```text
Module (AbstractionLevel: High)
  Function Add
    Instruction (Op: Core.Add, Params: a, b) → Returns: r
    Result (State: Ok)
```

Cross-level calls (High → Low) require an explicit **ABI Map** so lowering to targets like WebAssembly is structurally resolvable. Untrusted modules (Origin: Agent/Human) MUST NOT declare `Capabilities`; attempting privileged Ops is a structural `TrustBoundaryViolation`.

## Key properties

### Code as structural data

SIR modules are valid STF documents; syntax errors are structurally impossible.

### Explicit abstraction levels

High and Low IR coexist in one representation; the level is declared, not inferred (Mid is reserved for future use and must not be declared).

### Trust-aware execution

Execution authority is bounded by STF Origin. Untrusted code cannot perform stateful side-effects.

### Structural control flow

Control flow is hierarchical via Branch/Loop; arbitrary jumps are forbidden. Errors propagate through monadic `Result` nodes — no hidden exceptions.

## SIR compared with other approaches

| Approach        | Primary purpose                | Relationship to SIR                                                          |
| --------------- | ------------------------------ | ---------------------------------------------------------------------------- |
| **Python / JS** | Human-written source code      | SIR trades token efficiency for structural determinism, no syntax hallucinations |
| **LLVM IR**     | Compiler intermediate representation | SIR is a structural, STF-based IR aimed at agents, not a textual IR      |
| **JSON AST**    | Machine AST exchange           | SIR adds a normative vocabulary, types, trust boundaries, and execution rules  |

SIR does not define a production compiler, ISA, runtime, or type system in this repository — it is a research starting point. The reference implementation lives separately: [ais-space/sir-compiler](https://github.com/ais-space/sir-compiler) — a self-hosting SIR→WASM compiler written in SIR itself (106 functions, compiles its own source into a byte-identical binary), paired with this specification (see Status below).

## Specification

The canonical documents (both written in STF, self-hosting):

**SIR v0.11.1-draft (concept v0.2.0-draft)**

- [SIR_Specification.stf](SIR_Specification.stf) — normative vocabulary, types, execution & memory rules, security boundaries
- [SIR_Concept.stf](SIR_Concept.stf) — research concept and motivation
- [grammar/stf.ebnf](grammar/stf.ebnf) — the STF core grammar (normative by reference)
- [examples/](examples/) — small runnable SIR programs (`.sir`): `validate_config` (branching validation), `wordcount` (loops and accumulation), `retry_budget` (bounded retry logic), `ledger_sum` (memory reads over a ledger), plus `sir_tool.sir` (canonical formatter and structural editor: `fmt`, `expand`, `insert`, `replace`, `delete`, `move`, `wrap`, `diff` modes over a framed stdin protocol)

Beyond these toys, the largest SIR program in existence is the reference compiler itself ([ais-space/sir-compiler](https://github.com/ais-space/sir-compiler), 106 functions across 97 modules plus runtime helpers) — a real-world proof that SIR scales past examples.

## SirTool — formatter and structural editor

`examples/sir_tool.sir` (29 functions, 7600 lines) is a real SIR utility, not a demo: one program, eight modes
selected by the first stdin line. Build it with the reference compiler, then:

| Mode | Input after the mode line | Output |
|------|---------------------------|--------|
| `fmt` | a document | canonical text (normalized indents, idempotent) |
| `expand` | frame + entry text | single flat document (includes glued, deduped) |
| `insert` / `replace` / `delete` | frame + path + target + fragment | edited canonical document |
| `move` | frame + `from>to` paths + target | node relocated |
| `wrap` | frame + `Func/i..j` range + target | range wrapped in a `Branch` |
| `diff` | frame + old text + new text | unified ` /`-`- `/`+` preview |

The frame for complex modes is three header lines (`BASE` directory for file
reads, path, target line count) followed by the target text and the payload
fragment. Example — canonical formatting:

```bash
{ printf 'fmt\n'; cat program.sir; } | wasmtime run sir_tool.wasm | sed -e '${/^[0-9][0-9]*$/d;}'
```

(All tool text outputs end with a newline, so the wasmtime result tail is
always a separate trailing digit line.) The full multi-file chain
(expand → compile → validate) is documented in the
[compiler README](https://github.com/ais-space/sir-compiler/blob/main/README.md).

## Repository structure

```text
sir/
├── SIR_Specification.stf
├── SIR_Concept.stf
├── README.md
├── CHANGELOG.md
├── ROADMAP.md
├── LICENSE
├── LICENSE-CODE
├── CONTRIBUTING.md
├── SECURITY.md
├── CODE_OF_CONDUCT.md
├── grammar/
│   └── stf.ebnf
└── examples/
    ├── validate_config.sir
    ├── wordcount.sir
    ├── retry_budget.sir
    ├── ledger_sum.sir
    └── sir_tool.sir
```

## Status

SIR is an open research draft (concept v0.2.0-draft, specification v0.11.1-draft). It invites independent implementations and alternative models.

Paired releases (SIR spec ↔ compiler, backward compatible downward —
a newer compiler accepts programs written against older SIR versions,
but refuses programs declaring a newer SIR than it supports):

| SIR spec      | Compiler |
|---------------|----------|
| v0.11.1-draft | 0.2.2    |

## License

- **Specification:** [Creative Commons Attribution 4.0 International](LICENSE) (CC-BY-4.0)
- **Reference code, tests, and tooling:** [Apache License 2.0](LICENSE-CODE)

Implementations do not inherit these licenses and may be released under other licenses.

## Contact

- **Issues:** [GitHub Issues](https://github.com/ais-space/sir/issues)
- **Security:** [support@ais-platform.dev](mailto:support@ais-platform.dev)
- **Website:** [ais-platform.dev](https://ais-platform.dev)
