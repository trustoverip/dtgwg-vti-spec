## Client Onboarding and Lifecycle

This section is normative.

This chapter specifies how a client obtains standing against a VTA, and how it
holds that standing over time. The onboarding sequence has four steps, and the
order carries the security properties, so the chapter is written in that order.

Throughout this chapter, *the node* means the VTA or VTC the client obtains
standing against, and *the operator* means the human responsible for the client.

### Client classes

A client is anything acting under granted authority rather than its own. Four
classes recur, and they differ in ways this chapter accounts for:

| Class | Holds its key | Human present at the moment of action | Can be woken |
|---|---|---|---|
| Interactive operator client | on the operator's machine | yes | not applicable |
| Headless application | on a server | no | no |
| AI-agent runtime | on a host the operator controls | not usually | yes |
| Mobile authorizer | in device-protected storage | yes, by definition | yes |

What does not vary across them: the client's authority is an artefact held by
the node, narrower than the node's own, revocable at the node, and never
inferred from the client's ability to execute a protocol.

### Step 1 — mint an ephemeral identifier client-side

**VTI-CLT-001** — A client MUST generate its own key pair, using a
cryptographically secure random source.

**VTI-CLT-002** — The private key MUST NOT leave the client. A client MUST NOT
transmit it to the node, to an administrator, or to any third party, and a node
MUST NOT require its disclosure as a condition of enrolment.

**VTI-CLT-003** — The resulting identifier MUST be a `did:key` identifier.

**VTI-CLT-004** — A client MUST record the identifier minted in this step as a
**bootstrap identity**, distinguishable in its local state from a long-term
identity.

**VTI-CLT-005** — This step MUST NOT require the node to be reachable.

**VTI-CLT-006** — A client SHOULD hold its private key in storage protected by
the platform — an operating-system keychain, a secure element, or equivalent —
and MUST NOT hold it in storage readable by other principals on the host.

*Rationale.* Client-side minting is what allows the node never to hold a
client's private key: a compromised node can revoke its clients, impersonate
none of them, and cannot repudiate what it signed by pointing at a key it also
held. `did:key` is required here because this step happens before the client
has any standing, any network, or anywhere to publish — an identifier that
requires a host or a registration is not mintable at this point in the
sequence.

### Step 2 — enrol the identifier

The bootstrap identifier is conveyed out-of-band to an administrator — a chat
message, an email, a scanned code — who records an access control entry for it.

**VTI-CLT-010** — Enrolment MUST result in an access control entry as defined
in the Trust Contexts chapter. A node MUST NOT grant a client standing by any
other means.

**VTI-CLT-011** — The enrolling administrator MUST hold act authority covering
the context the entry is scoped to.

**VTI-CLT-012** — The entry created at enrolment MUST be scoped to the narrowest
context and capability set the client's purpose requires.

**VTI-CLT-013** — A bootstrap identifier MUST be treated as low-trust: it has
crossed a channel outside the trust system, and the node has no evidence of
which party controls it beyond the assertion of the person who relayed it.

**VTI-CLT-014** — The entry created at enrolment SHOULD carry a short expiry.
Where the client does not perform the rotation in Step 3, the entry MUST carry
an expiry.

**VTI-CLT-015** — The enrolling administrator SHOULD confirm the identifier
with the client's operator over a channel distinct from the one that delivered
it.

**VTI-CLT-016** — A client that repeatedly fails to obtain standing after
enrolment MUST report that condition to its operator distinguishably from a
transport failure. A client MUST NOT rely on the node disclosing whether an
entry exists, which VTI-SES-007 prohibits: the signal is the client's own
knowledge that it enrolled and has not obtained standing.

*Rationale for VTI-CLT-015 and VTI-CLT-016.* The enrolment channel is the weak
point of the sequence, and the threat is substitution rather than disclosure:
an attacker who can modify the message that carries the identifier can have the
administrator enrol an identifier the attacker controls. Rotation in Step 3
does not address this — the substituted identifier would simply rotate onto
another identifier the attacker controls. What addresses it is confirming the
identifier over a second channel, and noticing that the legitimate client never
obtained standing. VTI-CLT-016 exists so that the second signal reaches a human
instead of appearing as an ordinary failure to connect.

*Rationale for VTI-CLT-012.* Enrolment is where scope is decided. Widening it
afterwards is a separate act with its own audit record, whereas a grant that
starts wide is rarely narrowed at all.

### Step 3 — connect, then roll the key

**VTI-CLT-020** — A VTA MUST implement the rotation operation specified in this
section — the `acl` family's key-swap operation in the required catalogue
(VTI-OPS-080) — so that the decision whether to rotate belongs to the
deployment rather than to the implementation.

**VTI-CLT-021** — On its first successful authentication, a client SHOULD
rotate off its bootstrap identifier onto a newly minted identifier and cause the
bootstrap identifier to lose standing.

*Rationale.* The strength of this recommendation tracks the exposure of the
bootstrap identifier. A deployment that delivered the identity inside a sealed
provisioning bundle has a defensible reason not to rotate. A deployment whose
identifier was pasted into a chat channel, forwarded, and quoted in a ticket
does not: that identifier is known to parties whose interest in it is unknown,
and it remains the name of a live grant until it is replaced.

Where rotation is performed, it MUST follow the sequence in this section.

#### Mint the replacement

**VTI-CLT-022** — The replacement key pair MUST be generated under the same
requirements as Step 1 (VTI-CLT-001, VTI-CLT-002, VTI-CLT-006).

#### Prove reachability before committing

**VTI-CLT-023** — Where the client will reconnect over a mediated transport,
the client MUST establish that the replacement identifier can be reached over
that transport before the rotation is committed, within a bounded timeout.

**VTI-CLT-024** — A client MUST NOT commit the rotation when reachability under
VTI-CLT-023 has not been established.

*Rationale.* This is not an authorization check: before the rotation the
replacement identifier has no entry and cannot authenticate at all. It is a
liveness check, and it is placed before the commit because that is the last
moment at which failure is free. A rotation committed to an identifier that
cannot be reached is unrecoverable — the bootstrap entry is already gone, and
the client holds a key nothing will accept. Before the commit, the same failure
costs a retry.

#### Swap atomically

**VTI-CLT-025** — The rotation MUST be a single operation that moves the
existing entry to the new subject. An implementation MUST NOT compose it from a
separate grant and revocation.

**VTI-CLT-026** — The request MUST carry a proof of control of the replacement
identifier that is bound to the node as its audience, is short-lived, and is
not replayable.

**VTI-CLT-027** — The node MUST verify that the authenticated caller is the
current subject of the entry being moved.

**VTI-CLT-028** — The node MUST verify that the declared new subject is the
holder proven by the accompanying proof.

**VTI-CLT-029** — The node MUST preserve the entry's role, context scope,
capability set, key-identifier narrowing, approve scope and expiry exactly. A
rotation request that would alter any of them MUST be refused.

**VTI-CLT-030** — The node MUST refuse a rotation of an entry whose current
subject is not the authenticated caller.

**VTI-CLT-031** — On completion, the bootstrap identifier MUST have no standing.
A node MUST NOT retain it as a grace-period credential.

**VTI-CLT-032** — The node MUST audit the rotation, identifying both the
previous and the new subject.

*Rationale for VTI-CLT-029 and VTI-CLT-030.* A rotation is a change of name,
not a change of authority. Together these two make the operation safe to expose
to the subject itself — which it has to be, since the point is that the client
performs it unattended — by ensuring that the most a caller can do with it is
rename its own grant. Without VTI-CLT-029 the operation is a self-service
escalation; without VTI-CLT-030 it is a way to take over someone else's.

#### Commit, then persist, before anything else

**VTI-CLT-033** — After the rotation is committed and before performing any
other operation, the client MUST persist the replacement key material.

**VTI-CLT-034** — A client MUST destroy the bootstrap private key once the
rotation has completed and the replacement key material has been persisted.

**VTI-CLT-035** — A failure occurring before the commit MUST leave the
bootstrap entry authoritative, and the sequence MUST be retryable in full.

*Rationale for VTI-CLT-033.* Between the commit and the persist, the previous
identifier has lost standing while the replacement key exists only in volatile
memory. Any work performed in that window — reporting, registration, discovery,
anything that can block or crash — risks leaving the operator with no usable
credential and no way back. The window cannot be eliminated, so the requirement
is that nothing optional is placed inside it.

### Step 4 — steady-state use

**VTI-CLT-040** — A client MUST maintain standing by refreshing its session as
specified in the Sessions chapter, and MUST NOT re-enrol as a means of
recovering from an expired session.

**VTI-CLT-041** — A client MUST NOT treat a previously successful authorization
as evidence that a later request is authorized.

**VTI-CLT-042** — Where an operation requires re-authentication, a client with a
human operator present MUST surface the demand to that operator. A client MUST
NOT attempt to satisfy a consent requirement on its own behalf.

**VTI-CLT-043** — A client MUST distinguish a refusal from a transport failure,
and MUST NOT retry a refusal as though it were one.

**VTI-CLT-044** — For an operation classified as requiring an idempotency key,
a client MUST hold that key stable across every attempt at the same logical
operation, and MUST NOT wrap the retrying layer in a retry loop of its own. See
the Operation Surface chapter.

**VTI-CLT-045** — A client MUST tolerate bounded clock skew when evaluating
credential and token validity, and MUST NOT extend that tolerance to material
it produces.

*Rationale for VTI-CLT-042.* A client that can satisfy a consent requirement is
a client for which the requirement does not exist. The requirement's purpose is
that a party other than the caller agreed, and a delegate acting for the caller
is not that party.

### Lifecycle

**VTI-CLT-050** — A client MUST support rotating its identifier again at any
time, under the requirements of Step 3.

**VTI-CLT-051** — A client that discovers its authority has been withdrawn or
narrowed MUST stop attempting the affected operations, MUST report the
condition to its operator, and MUST NOT attempt to re-enrol automatically.

**VTI-CLT-052** — A client identity belongs to exactly one client instance. Key
material MUST NOT be shared between instances, copied to another device, or
included in a machine image, template or backup from which further instances
are created.

**VTI-CLT-053** — Removing a client's entry MUST be sufficient to offboard it,
and MUST take effect as required by VTI-ACL-080.

**VTI-CLT-054** — A node MUST NOT move an entry to a new subject without a proof
of control from that subject. Recovery of a lost client key MUST be performed by
enrolling a new identifier.

*Rationale for VTI-CLT-052.* A shared identity cannot be revoked per instance:
withdrawing it stops every instance that holds it, so the operator facing one
compromised host is choosing between an outage and leaving the compromise in
place. It is also a durable correlator across every device carrying it, which
defeats the context boundary the rest of this model rests on.

Machine images are named explicitly because sharing is rarely a decision. An
image captured after enrolment carries the key, and every instance started from
it is the same subject as far as the node is concerned.

*Rationale for VTI-CLT-054.* An administrative move without proof of control is
a way to hand a grant to a subject that never asked for it and cannot be shown
to control it, which is the same power as forging the client. Deleting the
entry and enrolling the replacement produces the same operational outcome with
an audit trail that says what actually happened.

### Client conformance checklist

An implementer of a client target satisfies this chapter by implementing, in
order:

| Step | Requirements |
|---|---|
| Mint | VTI-CLT-001 – VTI-CLT-006 |
| Enrol | VTI-CLT-013, VTI-CLT-016 (the client's half; the rest bind the node and the administrator) |
| Roll | VTI-CLT-021 – VTI-CLT-026, VTI-CLT-033 – VTI-CLT-035 |
| Use | VTI-CLT-040 – VTI-CLT-045 |
| Lifecycle | VTI-CLT-050 – VTI-CLT-052 |

A node target additionally implements VTI-CLT-010 – VTI-CLT-012, VTI-CLT-014,
VTI-CLT-020, VTI-CLT-027 – VTI-CLT-032, VTI-CLT-053 and VTI-CLT-054.

Sequence diagrams, a worked example and test vectors — including the failure
cases either side of the commit point — appear in Appendix A.
