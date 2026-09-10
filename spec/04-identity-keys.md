## Identity, DIDs and Key Management

This section is normative.

This chapter specifies the identifiers a VTI node uses, the cryptography behind
them, and the lifecycle of both.

### Identifier methods

**VTI-KEY-001** — The durable identity of a VTA, a VTC or a host service MUST
be a `did:webvh` identifier.

**VTI-KEY-002** — A client bootstrap identity MUST be a `did:key` identifier, as
required by VTI-CLT-003.

**VTI-KEY-003** — A node MAY support further identifier methods, and MUST NOT
require a peer to support a method other than those named in VTI-KEY-001 and
VTI-KEY-002.

**VTI-KEY-004** — An identifier method serving the durable position MUST provide:
resolution by a party with no prior relationship to the subject; a verifiable
history of the identifier's updates; rotation of key material without change of
the identifier; and deactivation.

*Rationale.* The two positions have opposite requirements, which is why two
methods are required rather than one. A node's identity outlives its keys and
has to be resolvable by strangers, so it needs a log and a history. A client's
bootstrap identity is minted before the client has standing, network access or
anywhere to publish, so it has to be derivable from a key and nothing else.
VTI-KEY-004 states what the durable position depends on, so that a future
method can be evaluated against the requirement rather than against the name.

### Correlation and identifier reuse

**VTI-KEY-005** — A node SHOULD use a distinct identifier for each relationship
it holds, where that relationship does not require a publicly resolvable
identity.

**VTI-KEY-006** — A client identifier MUST NOT be reused across trust contexts.

**VTI-KEY-007** — A durable node identifier is publicly resolvable and
therefore inherently correlatable. A node MUST NOT use its durable identifier
for interactions in which correlation of the parties is not intended.

*Rationale.* The context boundary is the deployment's primary privacy control,
and an identifier that spans contexts defeats it without touching a single
credential: the observer does not need to read anything, only to notice the
same name in two places. This is the identifier-layer form of the composition
requirement on privacy — a set of unlinkable credentials presented under one
stable identifier composes into a linkable interaction.

### Cryptography

**VTI-KEY-010** — Ed25519 for signatures and X25519 for key agreement are
mandatory to implement. Every conforming node MUST support both.

**VTI-KEY-011** — A node MAY support further algorithms, and MUST NOT assume a
peer supports any algorithm other than those in VTI-KEY-010.

**VTI-KEY-012** — A node MUST identify the algorithm of every key it publishes,
and MUST NOT infer the algorithm of a peer's key from context.

**VTI-KEY-013** — A node MUST maintain a set of accepted algorithms, MUST
refuse a proof whose algorithm is outside it, and MUST be able to retire an
algorithm from that set without a software change.

*Rationale for VTI-KEY-013.* An algorithm is retired at the moment it is broken,
which is not a moment anyone schedules. A deployment that can only retire an
algorithm by shipping a release will keep accepting it for as long as the
release takes, and the decision will be made by whoever owns the build rather
than by whoever owns the risk.

*Rationale.* One mandatory suite is what makes two conforming nodes
interoperable without negotiation. Everything else is a local optimisation that
a peer is entitled not to have.

### Key purposes

**VTI-KEY-020** — Every published key MUST state the purposes it is authorized
for.

**VTI-KEY-021** — A key authorized for one purpose MUST NOT be used for another.

**VTI-KEY-022** — A verifier MUST check that the key used is authorized for the
purpose the material is being relied on for, and MUST refuse where it is not.

### Derivation

**VTI-KEY-030** — A key belonging to a context MUST be derived from that
context's derivation base.

**VTI-KEY-031** — A context's derivation base MUST be immutable, as required by
VTI-CTX-021, and a child context's base MUST nest under its parent's, as
required by VTI-CTX-022.

**VTI-KEY-032** — Derivation MUST NOT permit a key belonging to one context to
be derived from the base of a context that is not its ancestor.

**VTI-KEY-033** — Root derivation material MUST NOT leave the node's protection
boundary except through the backup mechanism specified in the Verifiable Trust
Agent chapter.

### Rotation

**VTI-KEY-040** — Rotation of a durable identity's key material MUST NOT change
the identifier.

**VTI-KEY-041** — Rotation MUST be recorded in the identifier's verifiable
history.

**VTI-KEY-042** — A verifier that observes a rotation MUST verify subsequent
material against the current key, and MUST NOT accept material created after the
rotation's effective time under the retired key.

**VTI-KEY-043** — An identifier method that supports commitment to the next key
SHOULD be used with that commitment made.

### Service endpoints

**VTI-KEY-050** — An identifier document MUST carry the transports at which its
subject can be reached.

**VTI-KEY-051** — A peer MUST NOT assume a single transport, and MUST select
from what the document offers.

### Resolution, caching and deactivation

**VTI-KEY-060** — A resolver MUST respect the validity period stated by a
resolution result, and MUST NOT extend it.

**VTI-KEY-061** — A resolver MUST NOT cache a negative resolution result beyond
a short bounded period, and MUST NOT cache one at all where the failure was a
transport failure rather than an authoritative statement that the identifier
does not exist.

**VTI-KEY-062** — A node MUST provide a means of forcing re-resolution of an
identifier.

**VTI-KEY-063** — A deactivated identifier MUST be treated as conferring
nothing.

*Rationale for VTI-KEY-061.* A negative cache entry outlives the condition that
produced it, and the symptom is an identifier that cannot be resolved long
after it can — indistinguishable, from the caller's side, from one that does
not exist. A newly published identifier is the common case: it is looked up
before it exists, and the answer is then remembered for as long as the cache
holds it.

Caching a transport failure is the specific error the second half prohibits. A
timeout is evidence about the network and none at all about the identifier, so
recording it as a fact about the identifier converts a transient condition into
a durable wrong answer.
