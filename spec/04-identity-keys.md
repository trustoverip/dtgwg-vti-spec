## Identity, DIDs and Key Management

This section is normative.

This chapter specifies the identifiers a VTI node uses, the cryptography behind
them, the roles a node's keys play, and the lifecycle of all three.

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

*Note.* For the durable identity of a VTA, a VTC or a VTN, the purposes of
VTI-KEY-020 are stated as key roles, and VTI-KEY-021 and VTI-KEY-022 are
applied through them: see [Key roles](#key-roles), VTI-KEY-070 onward.

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

*Note.* How rotation applies to each key role — planned rotation against
revocation for compromise, overlap, and how material signed before a rotation
is judged afterwards — is specified in
[Rotation and revocation by role](#rotation-and-revocation-by-role) and
[Judging material signed before a rotation](#judging-material-signed-before-a-rotation).
For the identifier-log update authority, VTI-KEY-124 makes the commitment of
VTI-KEY-043 a requirement.

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

### Key roles

A durable node identity signs two very different kinds of thing. Some of what
it signs is a decision other parties rely on long after delivery, often without
ever contacting the node: a membership credential, a role credential, an
endorsement, a status list. The rest is the node's own traffic: a Trust Task
response, a DIDComm or TSP message, an invitation, an audit checkpoint. A key
that signs both is a key whose theft converts the compromise of a running
service — the process that answers messages, and therefore the process most
exposed — into the ability to forge the community's decisions. This section
separates the two, and names each separation, so that a verifier can tell which
kind of key produced what it is looking at and refuse the wrong one.

The separation is by [[ref: key role]]. A key role is a named, spec-defined position
a key occupies in an identity: what it may sign, which verification
relationship of the identifier document it may appear in, which proof purpose
its proofs may carry, and who may hold it. A key role is not a [[ref: role]] in
the sense of the Access Control and Authority chapter, which is a position of a
subject; the two share a word and nothing else.

The key role named `approver` below is likewise distinct from an
[[ref: approver]] — a subject that satisfies a consent requirement under the
Approvals, Consent and Step-Up chapter. The two are related in one direction
only: an approval rule may govern whether a node decides to issue an approval
artefact, and the `approver` key is what makes the decision, once taken,
verifiable by a third party.

*Open issue.* The identifier `approver` collides with the defined term
[[ref: approver]], and "approval artefact" sits close to the approvals of the
Approvals, Consent and Step-Up chapter. The working group may prefer a
different identifier for the role (for example `issuer` or `attestation`) before
the registry is fixed. The requirements below do not depend on the choice.

**VTI-KEY-070** — The durable identity of a VTA, a VTC and a VTN MUST bind every
key it publishes, and the key or keys holding its identifier-log update
authority, to exactly one key role registered under VTI-KEY-072. This binding is
how such an identity satisfies VTI-KEY-020.

**VTI-KEY-071** — A key MUST NOT be bound to more than one key role. Key
material MUST NOT be reused across key roles, across the identities of different
nodes, or between a node's identity and the identity of any service operated
alongside it.

**VTI-KEY-072** — The key roles are an extensible registry. This specification
registers the roles in the table below. A registration MUST state, for the
role: what it signs or is used for; the verification relationships it may
appear in; the proof purposes its proofs may carry; and its custody class.

| Key role | Signs or is used for | Verification relationship | Proof purpose | Custody |
|---|---|---|---|---|
| `approver` | approval artefacts (VTI-KEY-080) | `assertionMethod`, and no other | `assertionMethod` | held only by the VTA; non-exportable (VTI-KEY-110) |
| `operational` | the node's own operational material (VTI-KEY-081) | `authentication`, and no other | `authentication` | MAY be held by the node's service process (VTI-KEY-114) |
| `messaging` | key agreement for encrypted transports; signs nothing | `keyAgreement`, and no other | none | MAY be held by the node's service process (VTI-KEY-114) |
| `update` | entries in the identifier's verifiable history | none: named only as the method's update authority, never as a verification method | as the identifier method defines for log entries | held only by the VTA; non-exportable (VTI-KEY-113) |

**VTI-KEY-073** — A registration MUST NOT widen what an existing role permits,
and MUST NOT permit a verification relationship or proof purpose already
permitted to another role unless it states why the two cannot be confused by a
verifier.

**VTI-KEY-074** — A verifier MUST ignore a key role identifier it does not
recognise. It MUST NOT treat a verification method bound to an unrecognised
role as bound to any role it recognises, and MUST NOT rely on such a method for
any purpose.

**VTI-KEY-075** — No registered role permits the `capabilityInvocation` or
`capabilityDelegation` relationships. A node MUST NOT list a verification method
in either relationship until a role permitting it is registered, and a verifier
MUST NOT rely on a method found only there.

*Rationale for VTI-KEY-070 and VTI-KEY-071.* A published purpose (VTI-KEY-020)
that the verifier has to reconstruct from which relationship a key happens to
appear in is a purpose the verifier cannot check: a key in both
`assertionMethod` and `authentication` is authorized for both, and nothing tells
the verifier which the node meant. Naming the role, one per key, is what turns
VTI-KEY-022 from an intention into a check. Reusing material across roles would
undo the separation without changing a single document; reusing it across
identities would make the compromise of one identity the compromise of another,
and would link the two for every observer.

*Rationale for VTI-KEY-074.* Extension is made safe the same way the capability
registry makes it safe (D6): a role nobody recognises confers nothing. The
failure this closes is the verifier that, not recognising a role, falls back to
the relationship the method appears in and so treats it as whatever that
relationship ordinarily means.

*Open issue.* Whether an operational request that invokes authority at a peer —
a Trust Task request, as opposed to a response — should use
`capabilityInvocation` rather than `authentication` is not settled.
VTI-KEY-075 keeps both capability relationships empty until it is.

#### Services operated alongside a node

A node is often deployed with companion services: a forge bridge beside a VTC, a
status-list host, a website host. Each is a separate process with a separate
exposure, and the question is whether it signs as the node or as itself.

**VTI-KEY-076** — A service operated alongside a node MUST have an identifier
and keys of its own, and MUST sign as itself. Its keys are not key roles of the
node, and the node's identifier document MUST NOT list them.

**VTI-KEY-077** — A verifier MUST NOT accept a signature by such a service as a
signature of the node. Authority the service holds on the node's behalf MUST be
recorded as an access control entry (VTI-VTA-032) or by an approval artefact
the node issues to the service, and never by the service's key appearing in the
node's document.

*Rationale.* A service that signs as the node is a copy of the node's signing
authority in another process, reachable by whoever compromises that process, and
indistinguishable from the node in every record. A service that signs as itself
can be scoped, revoked and audited separately — which is the argument of
VTI-VTA-040 applied to infrastructure rather than to agents.

### What each role signs

**VTI-KEY-080** — The following are [[ref: approval artefact]]s, and a node MUST sign
each with an `approver` key and with no key of another role:

1. every membership credential, including each credential of a pair that
   commit to one another (VTI-MEM-021);
2. every role credential;
3. every endorsement credential, including one of a community-defined
   endorsement type, and every grant that confers the standing to vet or
   endorse others;
4. every status list credential, and every other artefact that states the
   status of an approval artefact; and
5. every other verifiable credential the node issues.

**VTI-KEY-081** — A node MUST sign the following with an `operational` key and
with no key of another role: invitations; removal and departure notices; Trust
Task request and response documents (VTI-OPS-020); signatures carried by
DIDComm or TSP messages; and audit checkpoints.

**VTI-KEY-082** — An artefact not named in VTI-KEY-080 or VTI-KEY-081 MUST be
treated as an approval artefact if it asserts, to any party other than its
immediate recipient, a decision of the node about a subject's membership,
standing, authority or status. Every other artefact the node signs MUST be
signed with an `operational` key.

**VTI-KEY-083** — A verifier MUST NOT treat material signed with an
`operational` key as evidence of a decision that VTI-KEY-080 requires an
approval artefact to evidence. A removal notice informs its recipient; the
removal takes effect for relying parties through the status an `approver` key
signs (VTI-MEM-030).

*Rationale for status lists in VTI-KEY-080.* A status list is the one artefact
whose forgery reverses a decision rather than inventing one. A forged
credential admits a stranger; a forged status list re-admits everyone the
community has suspended or removed, at once and silently, because a verifier
that checks status is exactly the verifier that trusts the list. It is signed
by the key the community's decisions are signed by, or those decisions can be
undone by the key that answers messages.

*Rationale for VTI-KEY-081.* Operational material is either consumed at once by
the party it is sent to, or is evidence of what the node did rather than of what
the community decided. An invitation confers nothing until an admission
decision is made, and the membership credential that follows is an approval
artefact. Keeping this material on a key the service process holds is what lets
the service run without asking the VTA to sign every message, and VTI-KEY-083
is what keeps that convenience from reaching the decisions.

#### Registry records

What a Trust Registry answers about a community is only as authentic as the
channel it arrives by, unless the answer carries a proof. Registries do not yet
store the proof of the artefact a record was derived from, or sign their
answers, so an `approver` proof on a registry record is a target rather than a
present fact.

**VTI-KEY-084** — A node writing to a Trust Registry MUST do so by an operation
document signed with its `operational` key, and a registry MUST attribute a
write to a node only where the document's proof verifies under that node's
`operational` key.

**VTI-KEY-085** — A record a registry publishes on a node's behalf SHOULD carry
the node's `approver` proof over the record's content, and a registry SHOULD
return that proof with every answer derived from the record.

**VTI-KEY-086** — Until a registry answer carries a proof under
VTI-KEY-085, a consumer MUST treat it as an assertion of the registry, bounded
by VTI-REG-010 and VTI-REG-011, and MUST NOT treat it as an approval artefact
of the node.

*Open issue.* The registry query protocol this specification binds carries no
member for a record's proof. VTI-KEY-085 becomes a MUST when it does; the
change is to that protocol, not to this requirement.

### Binding roles in the identifier document

A node's identifier document states each key's role twice: once through the
standard verification relationship, which every DID-aware verifier already
reads, and once through a `keyRoles` property, which says which role the node
meant. The relationships are what generic tooling checks; the property is what
lets a VTI verifier distinguish two keys that share a relationship, and refuse
the one that should not have signed.

**VTI-KEY-090** — A node's identifier document MUST carry a `keyRoles` property:
a map from each registered key role identifier the node uses to a non-empty
array of the identifiers of the verification methods bound to that role. Every
verification method the document lists in any verification relationship MUST
appear in `keyRoles` under exactly one role.

**VTI-KEY-091** — A verification method MUST appear in the verification
relationship its role requires, and in no other.

**VTI-KEY-092** — A verifier MUST NOT rely, for any purpose, on a verification
method that is absent from `keyRoles`, listed under more than one role, or
listed in a relationship its role does not permit.

**VTI-KEY-093** — `keyRoles` MUST NOT list the `update` role. The update
authority is stated only where the identifier method states it — for
`did:webvh`, the log's update keys — and an update key MUST NOT appear as a
verification method in the document.

**VTI-KEY-094** — A verifier MUST select a node's key by role, through
`keyRoles` and the verification relationship together. It MUST NOT select or
accept a key by its fragment, its position in the document, its type or its
algorithm.

A non-normative example, for a VTC with an Ed25519 and an ML-DSA-44 `approver`
key:

```json
{
  "id": "did:webvh:{SCID}:community.example",
  "verificationMethod": [
    { "id": "#z6Mkq…", "type": "Multikey", "publicKeyMultibase": "z6Mkq…" },
    { "id": "#z2Sy…",  "type": "Multikey", "publicKeyMultibase": "z2Sy…" },
    { "id": "#z6Mkv…", "type": "Multikey", "publicKeyMultibase": "z6Mkv…" },
    { "id": "#z6LSb…", "type": "Multikey", "publicKeyMultibase": "z6LSb…" }
  ],
  "assertionMethod": ["#z6Mkq…", "#z2Sy…"],
  "authentication": ["#z6Mkv…"],
  "keyAgreement": ["#z6LSb…"],
  "keyRoles": {
    "approver": ["#z6Mkq…", "#z2Sy…"],
    "operational": ["#z6Mkv…"],
    "messaging": ["#z6LSb…"]
  }
}
```

The fragments in the example are derived from the key rather than named for
it; see VTI-KEY-150.

*Rationale for VTI-KEY-092.* A document in which the two statements disagree has
been produced by a node that is misconfigured or by a party that is not the
node, and in neither case does the verifier know which statement is true.
Refusing the method rather than the whole document keeps a node reachable while
its operator fixes the error, and fails closed on the one key in doubt.

*Rationale for VTI-KEY-094.* A fragment is a name the node chose, and a
verifier that selects by it — `#key-0` is the signing key — has made the node's
naming convention part of its security decision. The convention is not stated
anywhere a verifier can check, it changes with every template, and it is exactly
what a migration (VTI-KEY-140) has to change.

*Open issue.* `keyRoles` needs a term definition — a JSON-LD context and a
stable IRI — before a document carrying it is valid JSON-LD. Whether it belongs
in the identifier document, in the identifier method's own parameters, or in a
resource the document links to is also open; the requirements above are stated
so that any of the three can satisfy them.

### Selecting and verifying by role

The rule that a proof's verification method must be listed in the relationship
its proof purpose names is already the rule of [VC-DATA-INTEGRITY]. It is
restated here because a verifier that skips it — accepting any method the
controller lists — is exactly the verifier the role separation cannot protect.
The Trust Tasks framework states the same rule for task documents in its Proof
section ([TRUST-TASKS]); this section applies it to everything a VTI node signs
and does not restate the framework.

**VTI-KEY-100** — A verifier MUST establish that a proof's verification method
is listed in the verification relationship its `proofPurpose` names, in the
version of the signer's identifier document that applies under
VTI-KEY-130, and MUST refuse a proof where it is not.

**VTI-KEY-101** — A verifier MUST accept an approval artefact only where it
verifies under a method bound to `approver`, and MUST accept material that
VTI-KEY-081 assigns to the `operational` role only where it verifies under a
method bound to `operational`. Material signed under a key of the other role
MUST be refused, as VTI-KEY-022 requires.

**VTI-KEY-102** — Where an artefact carries a set of proofs, every proof that
the verifier evaluates MUST verify and MUST satisfy VTI-KEY-100 and
VTI-KEY-101, and at least one proof MUST be evaluated. A proof whose
cryptosuite is outside the verifier's accepted set (VTI-KEY-013) is not
evaluated. A verifier MUST refuse the artefact where any evaluated proof fails;
it MUST NOT accept an artefact because some proof in the set verifies.

**VTI-KEY-103** — A node MAY sign an approval artefact with more than one
`approver` key of different algorithms — an Ed25519 proof and an ML-DSA-44
proof, for example — as a proof set. Every key in the set MUST be bound to
`approver`, and every proof MUST be by the same controller. A verifier MAY
require that a proof under a particular cryptosuite be present.

*Rationale for VTI-KEY-102.* A proof set that is accepted when any member
verifies is as strong as its weakest member, and it can be extended by anyone:
an attacker who can forge a proof under a weak or compromised key appends it to
a genuine artefact, or strips the genuine proof and substitutes their own. The
rule that every evaluated proof must verify makes the set as strong as the
strongest proof the verifier can check. The exemption for an unaccepted
cryptosuite is what lets a hybrid set reach verifiers that implement only one
of its algorithms — a verifier that has not implemented ML-DSA checks the
Ed25519 proof — without letting an algorithm the verifier has retired veto an
artefact it can otherwise establish.

*Open issue.* A Data Integrity proof with the `authentication` purpose ordinarily
carries a challenge and a domain, and a signed operational document — a Trust
Task response, an audit checkpoint — has neither in that sense: it is
addressed and dated by its own members (VTI-OPS-020). Which proof purpose an
`operational` key's proofs carry on such documents, and whether the Trust Tasks
framework's rule accepts it, has to be settled with that framework. The
examples in the framework today use `assertionMethod` for task documents, which
VTI-KEY-091 would forbid an `operational` key to appear in.

### Custody of role keys

**VTI-KEY-110** — An `approver` key MUST be generated within the VTA's
protection boundary and marked non-exportable at creation. It MUST NOT be
exportable by any operation, including an export under VTI-VTA-003, and MUST be
used only by the VTA, as the signing oracle of the Verifiable Trust Agent
chapter.

**VTI-KEY-111** — For a VTC or a VTN, the `approver` keys MUST be held by the VTA
the node is provisioned on, and the node's service process MUST NOT hold them.
The node obtains every approval artefact by a signing request to that VTA,
which applies VTI-VTA-004 through VTI-VTA-007 to it.

**VTI-KEY-112** — An `approver` key or an `update` key MUST NOT be included in
any backup, export or state transfer of a service, including a backup of a VTC.

**VTI-KEY-113** — An `update` key MUST be held by the VTA with the custody
VTI-KEY-110 requires of an `approver` key, and MUST be distinct from every key
of every other role. A process that holds an `operational` or `messaging` key
MUST NOT be able to produce an entry in the identifier's verifiable history.

**VTI-KEY-114** — An `operational` or `messaging` key MAY be held by the node's
service process. It MUST NOT be derived from, or be used to derive, any other
secret: a storage encryption key, an audit chaining key, an installation or
enrolment secret, a session secret, or a key of another role. Each such secret
MUST be generated or derived independently of every signing key.

**VTI-KEY-115** — A deployment MUST be such that compromise of a node's service
process, including every key and secret it holds, does not yield the ability to
produce an approval artefact or an identifier-log entry other than by a request
the VTA authorizes and records.

*Rationale for VTI-KEY-110 and VTI-KEY-111.* The service process is the part
of a node that parses untrusted input from the network, and it is therefore the
part most likely to be compromised. What VTI-KEY-111 buys is that such a
compromise yields a caller of the signing oracle rather than a holder of the
key: every approval it can obtain is one the VTA parsed, constrained to its
shape and addressee, and recorded (VTI-VTA-006), and the caller's authority can
be withdrawn at once (VTI-VTA-002). A stolen key signs whatever the thief
likes, for as long as it stays published, and leaves no record.

*Rationale for VTI-KEY-114.* A secret derived from a signing key falls with it,
and one from which a signing key is derived exposes it. The closed entry for
VTI-AUD-005 in Appendix F records the same reasoning applied to the audit key:
a secret that shares an origin with another is backed up, recovered and
compromised together with it, whether or not anyone intended the two to be
linked.

*Open issue.* VTI-KEY-030 requires a context's keys to be derived from the
context's derivation base, and VTI-VTA-050 permits root derivation material to
leave the VTA in a backup. A key derived from a base that can be backed up is
not non-exportable in the sense VTI-KEY-110 means: whoever holds the backup and
its separately held material can re-derive it. Either `approver` and `update`
keys are exempted from VTI-KEY-030 and generated, which makes their loss
unrecoverable except by rotation under a separate authority (VTI-KEY-124), or
they are derived and VTI-KEY-110 is restated as "never exported except in a
VTA backup". The first is stronger and is the intent of this section; the
working group has to choose, and VTI-KEY-030 changes with the choice.

*Open issue.* Signing an approval artefact is a constrained signing capability
under VTI-VTA-007, distinct from the generic `sign` and from `sign-trust-task`.
Appendix C does not yet register a capability for it.

### Rotation and revocation by role

Two events retire a key, and a verifier has to be able to tell them apart. A
**planned rotation** replaces a key that is believed sound: what it signed
before it was retired stays valid. A **revocation for compromise** withdraws a
key that is believed to be in someone else's hands from some time — the
compromise time, which is often earlier than the moment the compromise is
discovered — and from that time the key's signatures establish nothing.

**VTI-KEY-120** — A node MUST support, for each key role, both planned rotation
and revocation for compromise, and MUST record each in the identifier's
verifiable history (VTI-KEY-041) such that a verifier can distinguish the two.

**VTI-KEY-121** — A revocation for compromise MUST state the compromise time:
the earliest time from which the key is to be treated as compromised. A node
that cannot establish when a compromise began MUST state the time the key was
first published.

**VTI-KEY-122** — A planned rotation of an `approver`, `operational` or
`messaging` key MUST publish the new key, bound to the same role, before the
node first uses it, and at least the document's stated validity period
(VTI-KEY-060) earlier. The retiring key MAY remain listed during the overlap.
From the first use of the new key, the node MUST NOT sign with the retiring key.
A retiring `messaging` key MAY remain listed for as long as messages encrypted to
it can still be in transit, and MUST NOT be used to encrypt.

**VTI-KEY-123** — A revocation for compromise MUST remove the key from every
verification relationship and from `keyRoles` in the same history entry that
records the revocation. It MUST NOT use an overlap.

**VTI-KEY-124** — The `update` role MUST be used with commitment to the next
update key made (VTI-KEY-043). Where an update key is compromised, the node MUST
rotate to the committed key. Where no uncompromised committed key remains, the
identifier MUST be deactivated, and a successor identity established; a node
MUST NOT continue to operate an identifier whose history it no longer solely
controls.

**VTI-KEY-125** — A node MUST destroy an `approver` key within its protection
boundary when that key is retired by planned rotation.

*Rationale for VTI-KEY-122.* A verifier holding a cached document learns of a new
key only when it re-resolves. A key used before every cache could have seen it
fails at every verifier holding the old document, and the failure looks like a
forgery. Publishing first, and using only after the validity period has run, is
what makes a planned rotation invisible to the parties relying on the node;
ceasing to sign with the old key at once is what keeps the overlap from
becoming a second active key.

*Rationale for VTI-KEY-125.* Destruction is what lets a retired `approver` key's
past signatures be judged without evidence of when they were made: a key that
no longer exists cannot have signed anything after it was destroyed, so the
retirement time bounds the issuance time from above (VTI-KEY-132). A retired key
that is kept can still sign, and its signatures carry whatever dates the signer
chooses.

*Open issue.* No identifier method this specification requires records the
reason for a key's removal. VTI-KEY-120 and VTI-KEY-121 need an encoding — a
member of the log entry or of `keyRoles` naming each revoked key and its
compromise time, or a status entry the VTA writes — and the working group has to
fix one. Until it does, VTI-KEY-120 cannot be met, and the only reading of a
removed `approver` key that is safe for a verifier is as revoked for compromise
from the time it was first published — which turns every planned rotation into
a re-issuance.

### Judging material signed before a rotation

A verifier that finds an approval artefact signed by a key no longer in the
current document has to decide whether the artefact was issued while the key was
the node's, and the artefact's own dates cannot tell it: they are written by the
signer, and a signer holding a retired key writes whatever dates it likes.

**VTI-KEY-130** — A verifier MUST accept an approval artefact under a key that
is not in the current document only where: the key was bound to `approver`, in
`assertionMethod`, in the version of the document current at the artefact's
issuance time; and the key has not been revoked for compromise with a compromise
time at or before the issuance time.

**VTI-KEY-131** — The issuance time used by VTI-KEY-130 MUST be established from
evidence the signer cannot backdate — the identifier's verifiable history, or a
status entry written by the VTA at issuance and published under an `approver`
key that is not itself revoked for compromise. It MUST NOT be established from
the artefact's own dates alone.

**VTI-KEY-132** — For a key retired by planned rotation under VTI-KEY-125, the
history entry that retired it bounds the issuance time of everything it signed,
and a verifier MAY accept the artefact under VTI-KEY-130 without further
evidence of issuance time.

**VTI-KEY-133** — For a key revoked for compromise, a verifier MUST refuse an
approval artefact unless evidence under VTI-KEY-131 establishes that it was
issued before the compromise time. On such a revocation the node MUST re-issue,
under a sound `approver` key, every approval artefact still in force that its
VTA's record of what it signed (VTI-VTA-006) shows it issued, and MUST re-sign
every status list at once.

**VTI-KEY-134** — A verifier MUST re-resolve the signer's identifier before
relying on an approval artefact where it has not resolved the identifier within
the document's stated validity period (VTI-KEY-060), and where the artefact
names a verification method the verifier's copy of the document does not list.
A verifier MUST NOT use a copy of a document older than 24 hours for a decision
under VTI-KEY-101 or VTI-KEY-130, whatever validity period the document states.

**VTI-KEY-135** — Material whose time of creation is established only by the
signer's own assertion MUST be verified against the keys current at the time of
verification, as VTI-KEY-042 requires. In particular, a commit signed by a
member's key that has since been retired, for any reason, MUST NOT be accepted
by a verification made after the retirement's effective time, and a change that
is still open when the member's key is retired MUST be re-signed under the
current key before it is accepted.

*Rationale for VTI-KEY-131.* Every date in a credential — its validity period,
the proof's creation time — is a claim by the signer. For a key that is sound
that is enough, because the signer is the node. For a key that has been stolen,
it is the thief's claim, and a verifier that trusts it accepts every backdated
forgery as having been issued before the theft.

*Rationale for VTI-KEY-135.* The difference from VTI-KEY-132 is custody. The
node destroys its retired `approver` key, so the retirement bounds what the key
can have signed. A member's key is held by the member's own VTA, whose custody
the community does not govern (VTI-VTC-013), and a commit's date is the
author's assertion. A retired member key that is still accepted for new
verification is a key that can still add commits to a repository under a date
of its holder's choosing. Re-signing open changes is the cost of that
protection, and it falls on exactly the changes that have not yet been relied
on.

*Open issue.* The identifier's verifiable history establishes when a key was
listed, not when a given artefact was signed. For VTI-KEY-131 to admit anything
under a key revoked for compromise, the VTA needs a published issuance record —
a status entry allocated at issuance, or a commitment anchored in the history
or with its witnesses — whose form is not yet specified. Until it is,
VTI-KEY-133 refuses every artefact under such a key and relies on re-issuance.

*Open issue.* The 24-hour bound in VTI-KEY-134 is a proposal. It trades the
load of re-resolution against the time a verifier can go on accepting a key the
node has revoked, and the working group may set it per role or leave the
tighter bound to the document's own validity period.

### Migration to key roles

An identity created before this section — the `vtc-host` form, with one
Ed25519 key in both `assertionMethod` and `authentication`, signing everything
the node signs and holding the identifier's update authority — is a **legacy
identity**. Its key has been held by the service process, so everything it
signed carries the custody of an `operational` key, whatever relationship it
was listed in.

**VTI-KEY-140** — A node with a legacy identity MUST migrate it by one entry in
the identifier's verifiable history that: adds one or more `approver` keys
generated under VTI-KEY-110; adds a newly generated `operational` key; adds
`keyRoles`; removes the legacy key from `assertionMethod`; and transfers the
update authority to an `update` key held under VTI-KEY-113, by way of the
committed next key where one exists.

**VTI-KEY-141** — The legacy key MUST NOT be bound to `approver`. It MAY remain
in `authentication`, bound to `operational`, for the overlap of VTI-KEY-122 and
no longer, and MUST then be removed.

**VTI-KEY-142** — The node MUST re-sign every status list under an `approver`
key when it publishes the migration entry, and a verifier that has resolved the
migration entry MUST NOT accept a status list signed by the legacy key.

**VTI-KEY-143** — The node MUST state, in the identifier's verifiable history,
the end of a grace period, and MUST re-issue under an `approver` key every other
approval artefact still in force no later than that end, at the artefact's
renewal where it falls before. The grace period MUST NOT extend beyond the
latest expiry of any approval artefact the legacy key signed, and MUST NOT
exceed the maximum membership period the community's policy permits
(VTI-MEM-040).

**VTI-KEY-144** — Until the end of the grace period, a verifier MAY accept an
approval artefact under the legacy key where the key was in `assertionMethod`
in a version of the document before the migration entry and has not been
revoked for compromise. After it, a verifier MUST refuse every approval
artefact under the legacy key.

**VTI-KEY-145** — A VTA's own legacy identity MUST be migrated under the same
requirements. A VTA SHOULD migrate its own identity before, or together with,
the identities of the nodes provisioned on it.

*Rationale for VTI-KEY-141 and VTI-KEY-144.* The legacy key cannot become an
`approver` key after the fact: it has been in the service process, so its
custody cannot be retrofitted, and it is not destroyed at migration, so
VTI-KEY-132 cannot bound what it signs. The grace period is therefore a
stated, bounded acceptance of risk that already existed — the verifier accepts
what it accepted yesterday, for a period the node has published — and not a
judgement that the legacy signatures are sound. Status lists get no grace
period because they are the artefact whose forgery undoes revocation
(VTI-KEY-080) and the one the node can re-sign in full at once.

*Open issue.* The encoding of the grace period's end in the identifier's
history is not yet fixed, and shares the encoding question of VTI-KEY-120.

### Privacy of key roles

**VTI-KEY-150** — `keyRoles` MUST contain only registered role identifiers and
verification method identifiers. A node MUST NOT publish in its identifier
document, in a verification method identifier or elsewhere, the custody,
location, host, operator, device or personal data behind a key, nor any
structure of the node's deployment beyond the roles a verifier needs.
Verification method identifiers SHOULD be derived from the key, or otherwise
opaque.

**VTI-KEY-151** — A node's durable identifier document MUST NOT list a key that
exists for a single member, context or relationship. Such a key belongs to an
identifier for that relationship (VTI-KEY-005).

**VTI-KEY-152** — Where material sent to a single recipient need not be relied
on by any other party, a node SHOULD authenticate it by authenticated encryption
under its `messaging` key rather than by a signature, so that the recipient does
not hold transferable proof that the node sent it.

**VTI-KEY-153** — A node SHOULD rotate its `approver` keys on a schedule that is
independent of any member's admission, renewal or departure, and SHOULD NOT
rotate them more often than its security policy requires.

*Rationale for VTI-KEY-153.* Every artefact names the key that signed it, and
the identifier's history publishes when each key was listed, so a key is a
coarse timestamp: whoever sees which key signed a member's credential learns
the interval in which it was issued. Frequent rotation partitions the
membership by issuance interval, which is the allocation VTI-CRD-014 forbids a
status list to follow, reached by another route; rotation timed to a member's
event identifies the member outright. Long-lived keys trade the other way — a
larger crowd per key, and a longer exposure if the key is stolen — which is why
the requirement is a SHOULD and the schedule is the node's policy.

*Note.* A node's `approver` and `operational` keys are long-lived and
publicly resolvable, as its identifier is (VTI-KEY-007). Everything they sign is
linkable to the node by design: an issuer that could not be identified could
not be trusted. What these requirements prevent is the keys revealing more than
the issuer — how the node is built, who operates it, or whom it has been
dealing with.
