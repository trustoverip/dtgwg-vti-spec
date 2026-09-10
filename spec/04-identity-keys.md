## Identity, DIDs and Key Management

{{This section is normative.}}

This chapter specifies the identifiers a VTI node uses, the cryptography behind
them, and the lifecycle of both.

### Identifier methods by node type

{{`did:webvh` and `did:key` are REQUIRED; every other method is OPTIONAL.

- `did:webvh` is the REQUIRED method for the durable identity of a VTA, a VTC
  and a host service: these identities outlive their keys, must be resolvable
  by parties that have never interacted with them, and must carry a verifiable
  history of their own updates.
- `did:key` is the REQUIRED method for client bootstrap identities (see the
  Client Onboarding chapter), where the identifier must be mintable offline
  with no host, no registration and no network.

A conforming node MAY support further methods, and MUST NOT require a peer to
support any method beyond the two above. This section also states the
*properties* each of the two positions depends on, so that a future method can
be evaluated against them.}}

### Mandatory cryptography

{{Curve25519 is the mandatory-to-implement curve: Ed25519 for signatures and
X25519 for key agreement. Every conforming node MUST implement it, and two
conforming nodes are therefore always able to interoperate without negotiation.

All other algorithms are OPTIONAL. A node MAY support additional curves or
suites, and MUST NOT assume a peer supports any of them.}}

### Key purposes

{{Authentication, assertion and key agreement, and the separation between them:
a key authorized for one purpose MUST NOT be used for another.}}

### Derivation and context binding

{{Hierarchical derivation, and how a key's derivation path binds to the context
tree defined in the Trust Contexts chapter. A context's derivation base is
immutable and nests as the context does.}}

### Rotation

{{Rotation and pre-rotation of node keys; what MUST survive a rotation; what a
peer MUST do on observing one; and how rotation interacts with the client key
roll specified in the Client Onboarding chapter.}}

### Service endpoints

{{The service entries an identifier document carries, and how a peer selects
among them.}}

### Resolution, caching and deactivation

{{Resolution requirements; cache behaviour, including the hazard that a
negative cache entry outlives the condition that produced it — which presents
in practice as an identifier that remains unresolvable long after it became
resolvable; and the required behaviour on a deactivated identifier.}}
