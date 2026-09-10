## Transports, Messaging and Delivery

{{This section is normative.}}

This chapter specifies how VTI nodes reach each other, and — more importantly —
what a sender is entitled to conclude when a send returns successfully.

### Transport options

{{The permitted transports, what each provides, and the rule that authorization
outcomes are **transport-independent**: the same request under the same
authority MUST produce the same decision on every transport.}}

### Liveness

{{A node MUST retain at least one working transport; the requirement exists to
prevent an administrative change from making a node permanently unreachable.}}

### Mediators and store-and-forward

{{The mediator's role, what it observes, and what it MUST NOT be able to
observe.}}

### Delivery confirmation

{{What counts as confirmed delivery, stated exactly: a transport-layer receipt,
an outbox drain, or a protocol-level reply. An accepted frame is not an applied
effect, and an implementation MUST NOT report the former as the latter.}}

### Retry, idempotency and timeouts

{{Which layer owns retry; the idempotency contract for a retried operation; and
the requirement that every network wait is bounded and fails closed.}}
