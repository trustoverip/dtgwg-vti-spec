# VTI specification — structure and decision record

This file records *why* the specification is broken up the way it is, and the
decisions that fixed the shape. It is working-group material, not part of the
rendered specification.

## Scope

VTI is the **system specification**: it defines the infrastructure that DTG
components are composed into, and it specifies the nodes of that
infrastructure — including the Verifiable Trust Community in full, and the
Verifiable Trust Network ahead of any implementation of it. It does not
restate the component specifications it binds (identifier methods, credential
data models, Trust Task definitions, transports); it references them and
states how they compose.

Three bodies of normative material:

1. **The common model** — trust contexts and the authority model built over
   them, identity and keys, transports and delivery, sessions, the client
   lifecycle, the operation surface, credential handling. Binds to every node.
2. **The node specifications** — VTA, VTC, VTN, supporting hosts — and, where two
   nodes could each plausibly own an authority decision, which one does.
3. **The composition requirements** — properties that must hold across a
   complete interaction and that no single component can establish.

## Chapter map

| Part | Chapter | File |
|---|---|---|
| A — Foundations | Introduction and scope | `spec/intro.md` |
| | Terminology | `spec/terms-and-definitions-intro.md` |
| | Architecture and conformance targets | `spec/02-architecture.md` |
| B — Common model | Trust contexts | `spec/03-trust-contexts.md` |
| | Access control and authority | `spec/03a-access-control.md` |
| | Approvals, consent and step-up | `spec/03b-approvals.md` |
| | Audit | `spec/03c-audit.md` |
| | Identity, DIDs and key management | `spec/04-identity-keys.md` |
| | Transports, messaging and delivery | `spec/05-transports.md` |
| | Sessions and authentication | `spec/06-sessions-auth.md` |
| | Client onboarding and lifecycle | `spec/07-clients.md` |
| | Operation surface | `spec/08-operations.md` |
| | Credentials and presentations | `spec/09-credentials.md` |
| C — Nodes | The Verifiable Trust Agent | `spec/10-vta.md` |
| | The Verifiable Trust Community | `spec/11-vtc.md` |
| | The Verifiable Trust Network | `spec/11a-vtn.md` |
| | Membership lifecycle and community credentials | `spec/12-vtc-lifecycle.md` |
| | Trust registries and cross-community recognition | `spec/13-registries.md` |
| D — System properties | Composition requirements | `spec/14-composition.md` |
| | Operational and assurance requirements | `spec/15-operational.md` |
| | Considerations (security, privacy, governance, i18n, accessibility) | `spec/90-considerations.md` |
| | Conformance | `spec/95-conformance.md` |
| | References | `spec/96-references.md` |
| | Appendices A–F | `spec/appendix.md` |

One chapter per file, because `markdown_paths` order is the rendered order and
the file split becomes the working group's unit of assignment and review. This
is a deliberate departure from the sibling DTGWG specifications
(`dtgwg-cred-spec`, `dtgwg-trust-tasks-spec`), which each keep their body in a
single `body.md`: VTI is several times their length, and a single file would
make every review touch every chapter. The cost is that the chapter list lives
in three places — `markdown_paths` in `specs.json`, the "Structure of this
document" table in `spec/intro.md`, and the chapter map above — and a change
that adds, removes or moves a chapter updates all three in the same pull
request. The profile table in `spec/95-conformance.md` names chapters too, and
changes with them when a chapter in a profile is split or renamed.

One requirement area per chapter. Every area in the Conformance chapter's area
table has a chapter of its own, so that a reader looking for an area's
requirements finds them under its name. The Trust Contexts chapter originally
carried four areas (`CTX`, `ACL`, `APV`, `AUD`) and was split along them
([#32](https://github.com/trustoverip/dtgwg-vti-spec/issues/32)); no requirement
identifier changed.

The rendered order is the order in `markdown_paths`, not the filenames, so a
chapter inserted between two existing ones takes a suffixed name —
`11a-vtn.md`, `03a-access-control.md` — rather than renumbering every file
after it. Renumbering costs the history of every file it touches and buys
nothing the ordering in `specs.json` does not already give. Filenames are not
identifiers: nothing in the rendered document or in a requirement identifier
refers to one.

## Requirement identifiers

Every normative statement gets a stable identifier — `VTI-<AREA>-<nnn>`, for
example `VTI-CTX-014`. Identifiers are permanent: a withdrawn requirement is
marked withdrawn, never renumbered or reused.

This is load-bearing for conformance. It is what lets a test suite, an
implementation's conformance claim, and any number of independent assessment
programmes all cite the same thing without any of them having to agree on
document structure.

## Specification-first

This document states how VTI is to work. It is not a description of an existing
implementation, and where a running system behaves differently it is the system
that diverges. Known divergences are recorded in Appendix F of the
specification, with the requirement, the observed behaviour and the intended
resolution. An entry there is a statement about an implementation and never a
qualification of a requirement.

The practical consequence for contributors: a proposal that weakens a
requirement because an implementation does something else belongs in a change
proposal against the requirement, argued on its merits. A proposal that records
what the implementation does belongs in the register. The two are different and
should not arrive as the same pull request.

## Ownership tags

Every candidate requirement carries an ownership classification while it is
being drafted — `COMPONENT-OWNED`, `COMPOSITION-OWNED`, `JOINTLY-OWNED`,
`ASSURANCE-ONLY`, `UNRESOLVED` — using the vocabulary developed in the RAHP
composition work. Tags come out of the published text, except in Appendix E,
where unresolved propositions are published with their status visible.

## Decisions

| # | Decision | Resolution |
|---|---|---|
| **D1** | Is the VTC in scope, or referenced? | **In scope, fully.** Chapters 11–13, with `spec/11-vtc.md` stating which authority decisions are the VTC's and which remain the VTA's. |
| **D2** | Is the post-enrolment key roll MUST or SHOULD? | **SHOULD.** The strength of the recommendation tracks the exposure of the bootstrap identifier. The *mechanism* is REQUIRED of a VTA, an unrolled bootstrap entry MUST expire, and a roll that does happen MUST be atomic and preserve scope exactly. |
| **D3** | Trust Tasks or bespoke interfaces? | **Trust Task wherever one exists.** A private interface is permitted only where no canonical task covers the operation, and must be namespaced, discoverable, and carry a retirement path. Private is a waiting room, not a destination. |
| **D4** | Who defines conformance? | **VTI does.** Conformance (does an implementation satisfy the spec) and assurance (does a deployment hold its properties in composition) are separated. VTI defines an *assessment interface* so that any number of programmes can contribute evidence, none of which holds normative authority. |
| **D5** | Nested contexts in this version? | **Yes.** Path-encoded identifiers with a segment-aware ancestry gate; re-parenting deferred, with the cost stated. |
| **D6** | Are capabilities a closed set or a registry? | **Extensible registry**, with a versioned annex (Appendix C) recording the set at publication. Extension is made safe by the rule that an unrecognised capability MUST NOT be treated as granted. |
| **D7** | Which identifier methods and algorithms are required? | **`did:webvh` and `did:key` are REQUIRED; all other methods OPTIONAL. Curve25519 is mandatory to implement** (Ed25519 signing, X25519 key agreement); all other algorithms OPTIONAL. |
| **D8** | Publish the composition chapter with unresolved propositions in it? | **Yes.** A backlog stated in public is worth more than a chapter withheld until it is complete. |
| **D9** | Is the VTN a distinct node type or a role of the VTC? | **Open.** The chapter is written so either resolution is reachable: it states what a network must do, not what it is built from. The `VTN` area is allocated and the conformance target is marked provisional. Closing it either way leaves the requirement identifiers untouched. |
| **D10** | Does listing require the listed party's agreement? | **No — and the asymmetry is the point.** A VTN may list a party that never asked, which is what makes curation possible at all. The cost is paid in `VTI-VTN-011` and `VTI-VTN-012`: an entry carries its acknowledgement state, and repudiations are published alongside it. |

## Status

| Chapter | State |
|---|---|
| Introduction and scope | **drafted** |
| Terminology | **drafted** — 31 terms, cross-linked, none dangling |
| Architecture and conformance targets | **drafted** |
| Trust contexts | **drafted** (`CTX`) |
| Access control and authority | **drafted** (`ACL`) |
| Approvals, consent and step-up | **drafted** (`APV`) |
| Audit | **drafted** (`AUD`) |
| Identity, DIDs and key management | **drafted** (`KEY`) |
| Transports, messaging and delivery | **drafted** (`TRN`) |
| Sessions and authentication | **drafted** (`SES`) |
| Client onboarding and lifecycle | **drafted** (`CLT`) |
| Operation surface | **drafted** (`OPS`) |
| Credentials and presentations | **drafted** (`CRD`) |
| The Verifiable Trust Agent | **drafted** (`VTA`) |
| The Verifiable Trust Community | **drafted** (`VTC`) |
| The Verifiable Trust Network | **drafted** (`VTN`) — specification-first; no implementation, and D9 open |
| Membership lifecycle | **drafted** (`MEM`) |
| Trust registries and recognition | **drafted** (`REG`) |
| Composition requirements | **drafted** (`CMP`) |
| Operational and assurance | **drafted** (`OPR`) |
| Considerations | **drafted** |
| Conformance | **drafted** (`CNF`) |
| References | **drafted**; some entries pending a stable citation |
| Appendix A — client onboarding | **drafted** — diagrams, worked example, test vectors |
| Appendix B — entry schema | **drafted** |
| Appendix C — role and capability annex | **drafted as a proposal** for WG ratification |
| Appendix D — context path grammar | **drafted** |
| Appendix E — composition catalogue | **drafted** — E.1 (record form) normative, the rest informative |
| Appendix F — divergence register | **drafted**; seeded, and open for implementer entries |

Known gaps. Every chapter and appendix now has text; what remains are the
items only the working group can close:

- **The IPR working-group name** in `spec/header.md`. The editor list has its
  first entry; further editors are added as the Working Group appoints them.
- **Ratification of Appendix C** — the role set and capability registry are
  drafted as a proposal. The requirements referencing it (VTI-ACL-010,
  VTI-ACL-011, VTI-ACL-030 through VTI-ACL-034) are written so the annex can be
  settled without changing them.
- **Canonical identifiers for the required catalogue.** The Operation Surface
  chapter now states which operations a conforming node exposes, by family and
  operation; binding those to canonical task identifiers needs the catalogue
  specification reference fixed.
- **Reference locations** — the entries in the References chapter marked
  *(pending)*, and confirmation of the rest against their published versions.
- **The test suite** — location and governance (VTI-CNF-007 onward). Until
  it exists, VTI-CNF-006's coverage obligation is an open defect of the
  specification, not a condition on any implementation.
- **D9, the VTN's status as a target.** The network chapter is drafted and its
  requirements are stated; whether the VTN is its own conformance target or a
  curation role of the VTC is for the working group. The conformance table
  carries the target as provisional until it decides.

## Drafting sequence

1. Repo hygiene and the conformance targets in `02-architecture.md` — every
   later requirement binds to those names and to the identifier scheme.
2. `03-trust-contexts.md` — the largest gap, and a prerequisite for the
   sessions, client, VTA and VTC chapters. Drafted as one chapter and since
   split into `03`–`03c` along its four requirement areas.
3. `07-clients.md` — the smallest self-contained normative chapter with
   running implementations behind it; the first real test of the structure.
4. `08-operations.md` — settles Trust Task precedence early, before more
   private interfaces accumulate.
5. `14-composition.md` — parallel track, fed by composition assessment work;
   drafted as a tagged catalogue rather than held until complete.
6. The remaining common-model and node chapters.
7. Considerations and conformance last, once there are requirement identifiers
   to point at.
