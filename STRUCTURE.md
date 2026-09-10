# VTI specification — structure and decision record

This file records *why* the specification is broken up the way it is, and the
decisions that fixed the shape. It is working-group material, not part of the
rendered specification.

## Scope

VTI is the **system specification**: it defines the infrastructure that DTG
components are composed into, and it specifies the nodes of that
infrastructure — including the Verifiable Trust Community in full. It does not
restate the component specifications it binds (identifier methods, credential
data models, Trust Task definitions, transports); it references them and
states how they compose.

Three bodies of normative material:

1. **The common model** — trust contexts and the authority model built over
   them, identity and keys, transports and delivery, sessions, the client
   lifecycle, the operation surface, credential handling. Binds to every node.
2. **The node specifications** — VTA, VTC, supporting hosts — and, where two
   nodes could each plausibly own an authority decision, which one does.
3. **The composition requirements** — properties that must hold across a
   complete interaction and that no single component can establish.

## Chapter map

| Part | Chapter | File |
|---|---|---|
| A — Foundations | Introduction and scope | `spec/intro.md` |
| | Terminology | `spec/terms-and-definitions-intro.md` |
| | Architecture and conformance targets | `spec/02-architecture.md` |
| B — Common model | Trust contexts and the authority model | `spec/03-trust-contexts.md` |
| | Identity, DIDs and key management | `spec/04-identity-keys.md` |
| | Transports, messaging and delivery | `spec/05-transports.md` |
| | Sessions and authentication | `spec/06-sessions-auth.md` |
| | Client onboarding and lifecycle | `spec/07-clients.md` |
| | Operation surface | `spec/08-operations.md` |
| | Credentials and presentations | `spec/09-credentials.md` |
| C — Nodes | The Verifiable Trust Agent | `spec/10-vta.md` |
| | The Verifiable Trust Community | `spec/11-vtc.md` |
| | Membership lifecycle and community credentials | `spec/12-vtc-lifecycle.md` |
| | Trust registries and cross-community recognition | `spec/13-registries.md` |
| D — System properties | Composition requirements | `spec/14-composition.md` |
| | Operational and assurance requirements | `spec/15-operational.md` |
| | Considerations (security, privacy, governance, i18n, accessibility) | `spec/90-considerations.md` |
| | Conformance | `spec/95-conformance.md` |
| | References | `spec/96-references.md` |
| | Appendices A–F | `spec/appendix.md` |

One chapter per file, because `markdown_paths` order is the rendered order and
the file split becomes the working group's unit of assignment and review.

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

## Status

| Chapter | State |
|---|---|
| Introduction and scope | **drafted** |
| Terminology | **drafted** — 29 terms, cross-linked, none dangling |
| Architecture and conformance targets | **drafted** |
| Trust contexts and the authority model | **drafted** (`CTX`, `ACL`, `APV`, `AUD`) |
| Identity, DIDs and key management | **drafted** (`KEY`) |
| Transports, messaging and delivery | **drafted** (`TRN`) |
| Sessions and authentication | **drafted** (`SES`) |
| Client onboarding and lifecycle | **drafted** (`CLT`) |
| Operation surface | **drafted** (`OPS`) |
| Credentials and presentations | **drafted** (`CRD`) |
| The Verifiable Trust Agent | **drafted** (`VTA`) |
| The Verifiable Trust Community | **drafted** (`VTC`) |
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
| Appendix E — composition catalogue | **drafted** |
| Appendix F — divergence register | **drafted**; seeded, and open for implementer entries |

Known gaps. Every chapter and appendix now has text; what remains are the
items only the working group can close:

- **Editors and the IPR working-group name** in `spec/header.md`.
- **Ratification of Appendix C** — the role set and capability registry are
  drafted as a proposal. The requirements referencing it (VTI-ACL-010,
  VTI-ACL-011, VTI-ACL-030 through VTI-ACL-034) are written so the annex can be
  settled without changing them.
- **The minimum operation set** in the Operation Surface chapter, which needs
  the canonical catalogue references fixed first.
- **Reference locations** — the entries in the References chapter marked
  *(pending)*, and confirmation of the rest against their published versions.
- **The test suite** — location and governance (VTI-CNF-007 onward).

## Drafting sequence

1. Repo hygiene and the conformance targets in `02-architecture.md` — every
   later requirement binds to those names and to the identifier scheme.
2. `03-trust-contexts.md` — the largest gap, and a prerequisite for the
   sessions, client, VTA and VTC chapters.
3. `07-clients.md` — the smallest self-contained normative chapter with
   running implementations behind it; the first real test of the structure.
4. `08-operations.md` — settles Trust Task precedence early, before more
   private interfaces accumulate.
5. `14-composition.md` — parallel track, fed by composition assessment work;
   drafted as a tagged catalogue rather than held until complete.
6. The remaining common-model and node chapters.
7. Considerations and conformance last, once there are requirement identifiers
   to point at.
