## Architecture and Conformance Targets

This section is informative.

This chapter names the node types that the rest of the specification binds
requirements to. Every normative statement in this document applies to at least
one of these targets, and the Conformance chapter states which profiles each
target implements.

### Conformance targets

| Target | Role |
|---|---|
| **Verifiable Trust Agent (VTA)** | The key, credential and authority root for a principal — a person, an organisation, or a software agent. Owns trust contexts and key derivation. |
| **Verifiable Trust Community (VTC)** | A community authority: membership, lifecycle, recognition and publication. Provisioned on top of an existing VTA. |
| **Client** | Any consumer acting under a granted credential: an interactive operator client, a headless application, an AI-agent runtime, or a mobile authorizer. |
| **Mediator / relay** | A store-and-forward transport node. Sees routing metadata, not content. |
| **Host service** | Identifier-log host, witness, push gateway, or room host. |
| **Trust registry** | Authority lookup for issuers and communities. |

### The trust boundary

{{The trust boundary of a deployment is drawn **per operator, not per
service**: two services run by one operator are inside one boundary, and two
instances of the same software run by different operators are not. This chapter
states that model and the consequences that follow from it for the rest of the
document.}}

### Layering

{{Component specifications below; this specification in the middle; governance
frameworks above. A diagram, and the rule for deciding which layer owns a
requirement.}}

### A request, end to end

{{One worked interaction traced through every target, as the orientation
example the later chapters refer back to.}}
