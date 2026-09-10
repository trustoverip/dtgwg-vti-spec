## Conformance

{{This section is normative.}}

This chapter separates two questions that are commonly conflated: whether an
implementation satisfies this specification, and whether a deployment holds its
properties in composition. The first is conformance and is decided by tests.
The second is assurance and is decided by evidence.

### Conformance targets and profiles

{{Targets are those named in the Architecture chapter. Profiles group the
normative chapters:

| Profile | Chapters |
|---|---|
| `Core` | Trust contexts and authority, identity and keys, sessions, clients, operation surface, credentials |
| `Delivery` | Transports, messaging and delivery |
| `Community` | Verifiable Trust Community, membership lifecycle, registries |
| `Composition` | Composition requirements |

This section states which profiles each target MUST implement, and which are
OPTIONAL.}}

### Requirement identifier areas

{{Each normative statement carries an identifier of the form
`VTI-<AREA>-<nnn>`. Areas allocated so far:

| Area | Chapter |
|---|---|
| `CTX` | Trust contexts — identifiers, ancestry, lifecycle |
| `ACL` | Access control entries, roles, capabilities, scopes, listing, delegation, revocation |
| `APV` | Approvals, consent and step-up |
| `AUD` | Audit |
| `CLT` | Client onboarding and lifecycle |
| `OPS` | Operation surface — precedence, documents, versioning, retry |
| `CMP` | Composition requirements |
| `KEY` | Identity, DIDs and key management |
| `TRN` | Transports, messaging and delivery |
| `SES` | Sessions and authentication |
| `VTA` | The Verifiable Trust Agent |

Further areas are allocated as the remaining chapters are drafted. An area is
never renamed and an identifier is never reused.}}

### Requirement identifiers and coverage

{{Every normative statement carries a stable identifier, and every identifier
maps to at least one conformance test. A requirement with no test is tracked as
a defect in this specification rather than left silently unverifiable.}}

### The test suite

{{Executable, per-target, and covering requests **and responses** — a suite
that validates only what an implementation sends measures its own fixtures
rather than the service under test. Conformance is claimed against a named
version of this specification and a named profile set, and results are
published.}}

### Assessment interface

{{Assurance — whether a deployment's properties hold in composition — is
answered by evidence rather than by a test result, and more than one assessment
programme is expected to exist.

This specification therefore defines the **interface** rather than naming an
assessor. An assessment submission cites a requirement identifier, states a
disposition of `supported`, `refuted` or `indeterminate`, names the method and
the artefacts it rests on, and carries the assessor's identity and the date.
Submissions accumulate in the catalogue in Appendix E.}}

### What an assessment does not do

{{No assessment programme holds normative authority under this specification.
Evidence does not alter a conformance status, and only the working group
converts an open proposition into a requirement. Two assessments may reach
different dispositions on the same proposition; a disagreement is recorded as a
disagreement, which is itself useful signal, and does not resolve itself into a
verdict.}}
