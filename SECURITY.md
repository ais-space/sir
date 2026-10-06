# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in the SIR specification, reference grammar, or related code in this repository, please report it privately.

**Contact:** support@ais-platform.dev

**Response time:** We aim to acknowledge reports within 48 hours and provide an initial assessment within 5 business days.

## Scope

This security policy covers:

- The SIR specification text (ambiguities that could lead to insecure implementations)
- The formal grammar (`grammar/stf.ebnf`, shared with STF core)
- Reference implementations and tooling in this repository

## Out of Scope

- Vulnerabilities in third-party SIR implementations (report to those projects directly)
- Semantic prompt injection through untrusted code content (documented limitation; see the STF core Trust Model, §4)
- Denial-of-service through resource exhaustion beyond documented parser limits (STF core §2.0)

## Supported Versions

| Version     | Supported          |
| ----------- | ------------------ |
| 0.11.1-draft | :white_check_mark: |

## Disclosure Policy

We follow responsible disclosure:

1. Reporter submits vulnerability privately
2. We acknowledge within 48 hours
3. We assess and develop a fix
4. We notify the reporter when the fix is ready
5. We publish the fix and credit the reporter (unless anonymity is requested)

## Security Model

SIR's security model is inherited from the STF core Trust Model (§4) and defined in the specification (§4 Security & Trust Boundaries). Key properties:

- **Trust-aware execution**: code execution authority is bounded by STF Origin
- **Untrusted-code sandboxing**: modules with `Origin: Agent` or `Origin: Human` MUST NOT declare `Capabilities`; attempting privileged Ops is a structural `TrustBoundaryViolation`
- **Trusted orchestration**: only `Origin: Application` or `Origin: Backend` may declare `Capabilities` and perform stateful side-effects
- **Fail-closed Origin**: default Origin is `Unknown` (untrusted)

For a full understanding, see the STF core Trust Model (§4) and the SIR specification (§4).
