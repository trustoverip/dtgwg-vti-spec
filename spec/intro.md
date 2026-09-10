## Introduction

This section is informative.

### What this specification covers

A [[ref: VTI]] is the running infrastructure in which Decentralized Trust Graph
(DTG) components are composed into an operating trust system. This
specification defines:

- **the common model** every node shares — [[ref: trust context]]s, the
  authority model built over them, identity and keys, transports and delivery,
  sessions, the [[ref: client]] lifecycle, the operation surface, and credential
  handling;
- **the nodes** themselves — the [[ref: VTA]], the [[ref: VTC]] and the
  supporting hosts — and, where two of them could each plausibly own an
  authority decision, which one actually does;
- **the composition requirements** — properties that must hold across a
  complete interaction and that no single component specification can
  establish.

### What this specification does not cover

VTI does not define identifier methods, credential data models,
[[ref: trust task]] definitions, transport protocols, or governance frameworks. It
references those specifications and states how they compose. Where a
requirement belongs to a component specification, this document says so rather
than restating it.

### The vocabulary in one paragraph

A [[ref: principal]] — a person, an organisation or a software agent — is
represented by a [[ref: VTA]], which holds their keys and organises what it
holds into [[ref: trust context]]s. A [[ref: client]] acts under authority the
VTA granted it, recorded in an [[ref: access control entry]]; so does any other
[[ref: delegate]]. A client with no standing yet begins with a
[[ref: bootstrap identity]] and rotates off it. Authority in a context may be
unrestricted, in which case the entry is a [[ref: super-administrator]], or
scoped to part of the tree, in which case it is a [[ref: context administrator]]
of that subtree. Sensitive operations may additionally require the caller to
re-prove control — [[ref: step-up]] — or require a different party, an
[[ref: approver]], to give [[ref: consent]]. Nodes reach each other through a
[[ref: mediator]] and depend on [[ref: host service]]s, and each node and client
is run by an [[ref: operator]] — the boundary within which two services are one
trust domain. A [[ref: VTC]] decides
who is a [[ref: member]] of a community and publishes to a
[[ref: trust registry]] so that other communities can extend
[[ref: recognition]]. Everything above is a component or a node; what happens
when they are operated together is [[ref: composition]], and a property that no
component can establish on its own is a
[[ref: composition-owned requirement]]. Where the evidence does not settle a
question, the answer is [[ref: indeterminate]], which is a result rather than
an error.

### This specification is written first

This document states how a Verifiable Trust Infrastructure is to work. It is
not a description of any existing implementation, and where a running system
behaves differently, it is the system that diverges.

That direction matters more here than it does for a specification written after
the fact. Several requirements in this document prohibit an encoding or a
default that is easy to implement, widely used, and wrong in a way that only
appears under adversarial conditions — an authority inferred from an empty
collection, a query direction supplied by default, a send reported as
successful because nothing objected. A specification written to describe such
an implementation would record the behaviour and add a warning. This one states
the behaviour that is correct and treats the difference as work to be done.

Known divergences between this specification and implementations are recorded
in Appendix F. The register exists so that the difference is visible and
tracked rather than argued about, and so that a reader evaluating an
implementation knows which requirements to ask about. An entry in it is a
statement about the implementation, never a qualification of the requirement.

### Structure of this document

The specification is in four parts.

| Part | Chapters | Binds |
|---|---|---|
| **A — Foundations** | Introduction; Terminology; Architecture and conformance targets | nothing normatively; fixes the vocabulary and names the targets |
| **B — The common model** | Trust contexts and the authority model; Identity, DIDs and key management; Transports, messaging and delivery; Sessions and authentication; Client onboarding and lifecycle; Operation surface; Credentials and presentations | every node type |
| **C — Node specifications** | The Verifiable Trust Agent; The Verifiable Trust Community; Membership lifecycle and community credentials; Trust registries and cross-community recognition | the named node |
| **D — System properties** | Composition requirements; Operational and assurance requirements; Considerations; Conformance | the deployment and the relying party |

A node type's chapter in Part C states what it adds to the common model, not
what it replaces in it. The Conformance chapter states which parts each target
implements.

### How requirements are stated

Normative statements carry stable identifiers of the form `VTI-<AREA>-<nnn>` —
for example `VTI-CTX-016`. The area is a three-letter code for the subject
matter, listed in the Conformance chapter, and the number is allocated in order
within its area.

Identifiers are permanent. A withdrawn requirement is marked withdrawn rather
than renumbered, and an identifier is never reused for different text. This is
what allows a conformance test, an implementation's conformance claim, and an
independent assessment to cite the same requirement without any of them having
to agree on the document's structure.

Paragraphs marked *Rationale* record why a requirement is written as it is.
They carry no normative force. They are kept in the document because the common
failure mode for these requirements is a reader reasonably concluding that the
obvious simpler formulation would do — and in most cases the simpler
formulation is one that has already been implemented, and has already failed.

### Reading this document for a purpose

| If you are | Read |
|---|---|
| Deciding whether VTI fits your ecosystem | This chapter, then Architecture and conformance targets |
| Implementing a client | Client onboarding and lifecycle; Sessions and authentication; Operation surface |
| Implementing or operating a VTA | Trust contexts and the authority model; The Verifiable Trust Agent; Operational and assurance requirements |
| Implementing or operating a VTC | Trust contexts and the authority model; the three Part C community chapters |
| Assessing a deployment | Composition requirements; Conformance; Appendix E |
| Writing a governance framework | Trust contexts and the authority model; Governance Considerations |

## Requirements Language

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in [IETF RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).
