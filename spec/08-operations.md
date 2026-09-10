## Operation Surface

{{This section is normative.}}

This chapter specifies how a VTI node exposes operations, and the relationship
between the canonical Trust Task catalogue and any interface a node defines for
itself.

### Precedence

{{Where a canonical Trust Task exists for an operation, a conforming node MUST
use it. A private or bespoke interface is permitted only where no canonical
task covers the operation.

A node that carries both a private interface and the canonical task for the
same operation MUST treat the canonical task as authoritative, and MUST publish
a retirement path for the other.}}

### Requirements on a private interface

{{A private interface MUST be namespaced so that it cannot collide with a
future canonical task; MUST follow the same envelope, error and idempotency
conventions; and MUST be discoverable, so that a peer can determine what a node
offers. Where the operation is plausibly general, an upstream proposal SHOULD
accompany it. A private interface is a waiting room, not a destination.}}

### Extension rather than divergence

{{Where a canonical task is nearly right, the correct response is the defined
extension mechanism, not an additional top-level member: canonical payloads are
closed, and a locally added member is divergence under another name. This
section states the extension rule and gives the worked example of a query
parameter that had to be proposed upstream rather than added locally.}}

### Versioning and negotiation

{{How two peers on different versions of a task find a common one; the
versioning contract that must exist for negotiation to mean more than exact
match; and which side speaks down.}}

### Envelope conformance

{{Conformance covers requests **and responses**. A conformance check that
validates only requests measures its own fixtures rather than the service; in
practice, closing that gap in one implementation surfaced a large body of real
violations that a request-only check had reported as clean.}}

### The policy gate

{{Approval and step-up rules are evaluated before the operation's handler, on
every transport.}}

### Minimum operation set

{{The operations a conforming VTA and a conforming VTC each expose.}}
