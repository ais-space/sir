# Contributing to SIR

Thank you for your interest in contributing to SIR — the STF application vocabulary for executable agent logic (Semantic Intermediate Representation).

## Ways to Contribute

### 1. Specification Feedback

- Open an issue for: bugs, ambiguities, missing edge cases, unclear wording
- Proposals for new features or changes: open an issue first to discuss before submitting a PR
- See `CHANGELOG.md` for the history of changes

### 2. Implementations & Experiments

- Build a tree-walk interpreter or AOT lowering path for SIR
- Report interoperability issues you encounter
- Share your implementation in the implementations list

### 3. Tooling

- Submit a tree-sitter or TextMate grammar for `.stf` syntax highlighting
- Help build a conformance test suite for SIR nodes, Ops, and rules
- Improve the formal `grammar/stf.ebnf` (shared STF core grammar)

### 4. Documentation

- Improve the README, examples, or guides
- Create tutorials or blog posts

## Pull Request Process

1. Fork the repository
2. Create a branch for your change
3. Ensure your change follows the repository structure
4. For specification changes: edit `SIR_Specification.stf` and keep it self-validating under its grammar
5. For grammar changes: edit `grammar/stf.ebnf` and keep `GrammarVersion` in sync with the specification meta-block
6. Submit a PR with a clear description of the change and its motivation

## Specification Development

The canonical specification is `SIR_Specification.stf` — a self-hosting STF document, itself a valid STF document under its own grammar. SIR is an application vocabulary of the STF core; the underlying syntax and semantic core are defined by STF core and referenced normatively, without version pinning.

## Governance

This project is maintained by AIS Platform. Specification changes are proposed through GitHub issues and pull requests, reviewed by maintainers, and merged when consensus is reached.

## License

- Specification text: [CC-BY-4.0](LICENSE)
- Reference code and tests: [Apache-2.0](LICENSE-CODE)
- Contributions are accepted under the same terms
