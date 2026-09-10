## Conformance

This section is normative.

This chapter separates two questions that are routinely conflated: whether an
implementation satisfies this specification, and whether a deployment holds its
properties in composition. The first is **conformance**, and it is decided by
tests. The second is **assurance**, and it is decided by evidence. This
specification defines the first and defines an interface for the second.

### Profiles

A **profile** is a named set of chapters. Requirements bind to targets; profiles
group them so that a target can state what it implements without enumerating
requirement identifiers.

| Profile | Chapters |
|---|---|
| `Core` | Trust contexts and the authority model; Identity, DIDs and key management; Sessions and authentication; Client onboarding and lifecycle; Operation surface; Credentials and presentations |
| `Delivery` | Transports, messaging and delivery |
| `Community` | The Verifiable Trust Community; Membership lifecycle and community credentials; Trust registries and cross-community recognition |
| `Composition` | Composition requirements |
| `Operations` | Operational and assurance requirements |

### Targets

| Target | `Core` | `Delivery` | `Community` | `Composition` | `Operations` |
|---|---|---|---|---|---|
| VTA | MUST | MUST | MAY | MUST | MUST |
| VTC | MUST | MUST | MUST | MUST | MUST |
| Client | MUST | MUST | MAY | MUST | MAY |
| Mediator | MAY | MUST | MAY | MAY | MUST |
| Host service | MAY | MUST | MAY | MAY | MUST |
| Trust registry | MAY | MAY | MUST | MUST | MUST |

**VTI-CNF-001** — A target MUST satisfy every requirement of every profile
marked MUST for it that binds that target. A requirement that does not bind a
target imposes nothing on it.

**VTI-CNF-002** — An implementation claiming conformance MUST state: the
version of this specification claimed against; the target or targets claimed;
the profiles implemented; and any OPTIONAL behaviour it implements that a peer
may rely on.

**VTI-CNF-003** — An implementation MUST NOT claim conformance to a profile
while implementing a subset of it.

### Requirement identifier areas

Each normative statement carries an identifier of the form `VTI-<AREA>-<nnn>`.
Areas allocated:

| Area | Chapter |
|---|---|
| `CTX` | Trust contexts — identifiers, ancestry, lifecycle |
| `ACL` | Access control entries, roles, capabilities, scopes, listing, delegation, revocation |
| `APV` | Approvals, consent and step-up |
| `AUD` | Audit |
| `KEY` | Identity, DIDs and key management |
| `TRN` | Transports, messaging and delivery |
| `SES` | Sessions and authentication |
| `CLT` | Client onboarding and lifecycle |
| `OPS` | Operation surface — precedence, documents, versioning, retry |
| `CRD` | Credentials and presentations |
| `VTA` | The Verifiable Trust Agent |
| `VTC` | The Verifiable Trust Community |
| `MEM` | Membership lifecycle and community credentials |
| `REG` | Trust registries and recognition |
| `CMP` | Composition requirements |
| `OPR` | Operational and assurance requirements |
| `CNF` | Conformance |

**VTI-CNF-004** — An identifier MUST NOT be reused. A withdrawn requirement MUST
be marked withdrawn and MUST retain its identifier.

*Note.* Identifier stability binds from the first Working Group Approved
Deliverable. While this specification is a Working Draft, a requirement may
still be revised in place as the working group settles its substance; each such
revision is recorded in the change log. Implementers tracking a Working Draft
should pin the version they built against.

**VTI-CNF-005** — An area MUST NOT be renamed.

### Coverage

**VTI-CNF-006** — Every requirement in this specification MUST map to at least
one conformance test. A requirement with no test MUST be recorded as a defect in
this specification.

*Rationale.* A requirement nobody can test is a requirement nobody can be held
to, and the gap is invisible from inside the document. Recording it as a defect
in the specification — rather than as a shortcoming of an implementation — puts
it where it can be fixed.

### The test suite

**VTI-CNF-007** — A conformance test MUST validate responses as well as
requests, as required by VTI-OPS-031.

**VTI-CNF-008** — Conformance MUST be claimed against a named version of this
specification and a named profile set, and results MUST be published in a form
that identifies which requirements were exercised.

The location of the suite, and who may change it, are not yet settled. Until
they are, a conformance claim identifies the suite it was run against by
revision, so that a reader can tell which tests a claim rests on.

### The assessment interface

Assurance — whether a deployment's properties hold in composition — is answered
by evidence rather than by a test result, and more than one assessment
programme is expected to exist. This specification therefore defines the
interface and does not name an assessor.

**VTI-CNF-010** — An assessment submission MUST cite the requirement identifier
it concerns.

**VTI-CNF-011** — A submission MUST state a disposition of `supported`,
`refuted` or `indeterminate`.

**VTI-CNF-012** — A submission MUST identify the method used, the artefacts the
disposition rests on, the assessor, and the date.

**VTI-CNF-013** — A submission concerning a composition property MUST identify
the components composed and the versions assessed.

Submissions are recorded in the catalogue in Appendix E.

### Divergence from implementations

**VTI-CNF-014** — A divergence between this specification and an implementation
MUST NOT be read as qualifying the requirement. An implementation that diverges
does not conform to the requirement it diverges from, whatever the reason.

**VTI-CNF-015** — An implementer MAY record a known divergence in the register
in Appendix F, stating the requirement, the observed behaviour and the intended
resolution. Recording a divergence MUST NOT be treated as an exemption from it.

*Rationale.* A register of known divergences is useful in two directions: it
tells a reader evaluating an implementation which requirements to ask about,
and it tells the working group where a requirement is being resisted — which is
sometimes evidence that the requirement is wrong, and is always evidence worth
having. It is stated as non-exempting because a register that grants exemptions
becomes the specification.

### What an assessment does not do

**VTI-CNF-020** — No assessment programme holds normative authority under this
specification.

**VTI-CNF-021** — An assessment MUST NOT alter a conformance status. Conformance
is decided by the tests in this chapter.

**VTI-CNF-022** — Only the working group converts an open proposition into a
requirement.

**VTI-CNF-023** — Where two assessments reach different dispositions on the same
requirement, both MUST be recorded. A disagreement MUST NOT be resolved by
selecting one of them.

*Rationale for VTI-CNF-023.* Two competent assessments disagreeing about
whether a property holds is information about the property, and usually about
the conditions each assessment assumed. Recording only the preferred
disposition destroys the more useful of the two facts.
