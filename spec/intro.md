## Introduction

This section is informative.

{{**Outline revision.** This document currently fixes the chapter structure of
the specification. Section bodies state what each chapter will specify;
normative text is drafted chapter by chapter per the sequencing in
`STRUCTURE.md` at the root of this repository.}}

### What this specification covers

A Verifiable Trust Infrastructure (VTI) is the running infrastructure in which
DTG components are composed into an operating trust system. This specification
defines:

- **the common model** every node shares — trust contexts, the authority model
  built over them, identity and keys, transports and delivery, sessions, the
  client lifecycle, the operation surface, and credential handling;
- **the nodes** themselves — the Verifiable Trust Agent (VTA), the Verifiable
  Trust Community (VTC) and the supporting hosts — and, where two of them could
  each plausibly own an authority decision, which one actually does;
- **the composition requirements** — properties that must hold across a
  complete interaction and that no single component specification can
  establish.

### What this specification does not cover

VTI does not define DID methods, credential data models, Trust Task
definitions, transport protocols, or governance frameworks. It references those
specifications and states how they compose. Where a requirement belongs to a
component specification, this document says so rather than restating it.

### Structure of this document

{{Reading guide: which chapters bind which conformance target, and the four
parts — foundations, the common model, the node specifications, and system
properties.}}

### Requirement identifiers

{{Every normative statement in this specification carries a stable identifier
of the form `VTI-<AREA>-<nnn>` (for example `VTI-CTX-014`). Identifiers are
permanent: a withdrawn requirement is marked withdrawn and is never renumbered
or reused. Conformance tests, implementation conformance claims, and
independent assessments all cite these identifiers — see the Conformance
chapter.}}

## Requirements Language

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in [IETF RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).
