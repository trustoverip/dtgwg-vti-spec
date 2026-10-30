## Identity, DIDs and Key Management

This section is normative.

This chapter specifies the identifiers a VTI node uses, the cryptography behind
them, the roles a node's keys play, and the lifecycle of all three.

### Identifier methods

**VTI-KEY-001** — The durable identity of a VTA, a VTC or a host service MUST
be a `did:webvh` identifier, or a `did:scid:vh:1` identifier whose source is a
`did:webvh` log (VTI-KEY-160). `did:webvh` is the default (VTI-KEY-165).

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
Which methods can carry a key-role identity is a stricter question, answered
by VTI-KEY-170 to VTI-KEY-173.

### The `did:scid` form of a `did:webvh` identifier

`did:scid` [DID-SCID] is a metamethod: a `did:scid` identifier is a
self-certifying identifier of a source method, and its `src` parameter says
where the source method publishes its verification metadata. For the `vh`
format, the source method is `did:webvh`, so a `did:scid:vh:1` identity is
verified by the same log as a `did:webvh` one, but is not bound to the host
that serves it: the log can move and the identifier stays the same. This
specification admits only that format to the durable position; other
`did:scid` formats are OPTIONAL to resolve and are not durable identities.

**VTI-KEY-160** — A `did:scid:vh:1` identity is the DID `did:scid:vh:1:<scid>`.
The `src` query parameter is a locator, not part of the identity:
`did:scid:vh:1:<scid>?src=<source>` says that the identity's log is published
as `did:webvh:<scid>:<tail>`, where `<tail>` is `<source>` with each `/`
replaced by `:`. A producer MUST write `<source>` as `<tail>` with each `:`
replaced by `/`, keeping the percent-encoding of the `did:webvh` form (a port
is `%3A<port>`). A reader MUST also accept a `<source>` with an `https://`
scheme, a trailing `/` or a literal `:` before a port, and normalise it to
`<tail>`. A node MUST compare `did:scid` identities by DID, with any `src`
removed: two DID URLs that differ only in `src` name the same subject.

**VTI-KEY-161** — A node MUST resolve a `did:scid:vh:1` identity by deriving
the `did:webvh` form from a source, resolving it, and verifying the log as
`did:webvh` requires. The source is the `src` of the DID URL being resolved
where it has one, and otherwise one the node already holds for that SCID
(VTI-KEY-162); a node holding none MUST fail resolution without caching the
failure as a statement that the identifier does not exist (VTI-KEY-061). The
resolved document MUST be the identity's own: its `id` is the `did:scid` DID;
every reference in it to the `did:webvh` DID, with or without a fragment, is
the same reference under the `did:scid` DID; and its `alsoKnownAs` lists the
`did:webvh` DID and the `did:scid` DID URL with the `src` it was resolved
through.

**VTI-KEY-162** — A node SHOULD keep, for each SCID, the sources it has
verified the identity's log at, and use them to resolve the DID when no `src`
is given. A source is untrusted: the SCID makes the log verifiable wherever it
is fetched from, but a source can serve an older version of it. A node MUST
NOT accept, for a SCID, a log whose latest version precedes one it has already
verified for that SCID, whatever the source. When it resolves a DID URL whose
`src` is not one it holds for that identity, a node that holds other sources
MUST also check what they serve, and MUST refuse the presented one if they
serve a later version. A node MUST persist the sources and latest verified
version of every `did:scid` identity it authorises — an access control
entry's subject, an owner, a member, an administrator — so that a restart does
not reset what it refuses.

**VTI-KEY-163** — A node presenting a `did:scid` identity MUST give it out as a
DID URL with `src` wherever the recipient may not hold a source for it: in
trigger links, invitations, credentials it issues and the first message it
sends a peer. The identity's document lists its key and service references
under the DID (`did:scid:vh:1:<scid>#<fragment>`). A reference the recipient
uses to resolve the key, such as a DIDComm `skid` or a proof's
`verificationMethod`, MAY carry `src` before the fragment
(`did:scid:vh:1:<scid>?src=<source>#<fragment>`), and so MAY a field that
names the sender, such as a DIDComm `from`. A recipient MUST dereference such a
reference by DID and fragment, ignoring `src`, and MUST match a reference to its
sender or issuer by DID, ignoring `src`; it MUST NOT ignore any other query
parameter on that ground. Where a format binds a key reference to a named
issuer by exact string, as a Trust Task binds its proof's `verificationMethod`
to its `issuer`, a producer MUST write the two in the same form.

**VTI-KEY-164** — A node MUST use a `did:scid` identity as itself: it MUST
address, encrypt to, verify, authorise and record the subject under the
`did:scid` DID, and MUST NOT substitute the `did:webvh` form, or the reverse.
The two forms are distinct identifiers. A node MUST NOT treat them as one
subject unless the `did:webvh` form's document lists the `did:scid` form in
`alsoKnownAs` (VTI-KEY-166), and MAY where it does, for example to carry an
access control entry or a membership from one form to the other.

**VTI-KEY-165** — A node that creates durable identities MUST create them as
`did:webvh` logs, and MUST present each new identity in the `did:webvh` form
unless the request, or the configuration of VTI-VTA-034 or VTI-VTC-004, selects
the `did:scid` form. The log is the same either way; the form decides which
identity the node gives out.

**VTI-KEY-166** — When it creates a `did:webvh` log, a node MUST, unless the
request or its configuration says otherwise, list the `did:scid` DID URL of the
new log, with its `src`, in the first version's `alsoKnownAs`. It MUST list it
where the identity is presented in the `did:scid` form, and MUST NOT offer an
option to leave it out in that case.

*Rationale for VTI-KEY-160 to VTI-KEY-166.* The `did:scid` form costs the
durable position nothing it depends on: VTI-KEY-004's four properties come
from the `did:webvh` log. What it adds is an identity that does not name its
host. A `did:webvh` identifier changes when its log moves; a `did:scid`
identity keeps its DID and changes only its `src`, which is why the DID and
not the DID URL is the identity (VTI-KEY-160), and why the resolved document
must be the identity's own (VTI-KEY-161): DID Core requires a document's `id`
to be the DID resolved, and a party verifying a key under the `did:scid` DID
must find it there.

Moving `src` out of the identity is what makes sources a concern (VTI-KEY-162).
Forgery is not one, because the SCID commits to the log's first entry and every
later entry is signed under keys that entry commits to, and `did:webvh`
verification binds each entry to the host its own identifier names, so a host
the log never named cannot serve it. Replay of an old version is: a portable
log that has moved still names its former host in its early entries, and
whoever holds that host later can serve the log as it stood before the move,
with keys its controller has since rotated away. Under `did:webvh` that replay
yields a different identifier; under `did:scid` it yields the same identity,
which is why VTI-KEY-162 checks a new source against the known ones and makes
the floor durable for every identity a node grants authority to.

VTI-KEY-164 keeps the two forms apart because a subject that chose one has not
necessarily chosen the other. The alias in `alsoKnownAs` is the controller
saying that it has, which is why it is listed by default (VTI-KEY-166): an
identity created without it cannot later be recognised in the other form
until it publishes a new version.

*Note.* `did:webvh` stays the default form presented, so a deployment that
configures nothing presents exactly what it did before. The new requirements
on such a deployment are to resolve the `did:scid` form (VTI-KEY-161, VTI-KEY-162)
and to accept a peer that presents it.

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

*Note.* For a service DID at key assurance level 2 or 3, the purposes of
VTI-KEY-020 are stated as key roles, and VTI-KEY-021 and VTI-KEY-022 are
applied through them: see [Key roles](#key-roles), VTI-KEY-070 onward. For a
relationship DID, a persona DID's keys other than its passkeys, and a level-1
service DID, they are stated by the verification relationships alone (VTI-KEY-190,
VTI-KEY-191, VTI-KEY-146).

### Derivation

**VTI-KEY-030** — A key belonging to a context MUST be derived from that
context's derivation base. An `attestation` or `update` key of a level-3 identity is
exempt: it is generated, not derived, under VTI-KEY-110 and VTI-KEY-116.

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
For the identifier-log update authority of a level-2 or level-3 identity,
VTI-KEY-124 makes the commitment of VTI-KEY-043 a requirement.

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

### DID classes

What a key-management requirement costs, and what it buys, depends on what an
identifier is for. A pairwise identifier that exists for one conversation has
nothing to gain from a recovery key; a community's identity, whose credentials
strangers rely on for years, has a great deal to lose without one. This
specification therefore sorts DIDs into three [[ref: DID class]]es, by use and not
by identifier method, and states which requirements of this chapter bind each.

| Class | What it is | Typical methods | Keys |
|---|---|---|---|
| [[ref: relationship DID]] | An identifier for one relationship, one community or one trust context: a pairwise identifier, a client's bootstrap identity, a member's per-community identifier | `did:peer`, `did:key`, any | One key is normal; no key roles, no level |
| [[ref: persona DID]] | A person's own identity: the identifier under which a person, through their VTA, acts as themselves | `did:webvh`, any | One key, and passkeys bound to `authenticator`; no other key role, no level |
| [[ref: service DID]] | The durable identity of a node or a service: a VTA, a VTC, a VTN, a trust registry, a host service, a mediator, an integration | `did:webvh`, `did:scid:vh:1` (VTI-KEY-001) | Chosen by its operator as a [[ref: key assurance level]] (VTI-KEY-194) |

**VTI-KEY-190** — A relationship DID is an identifier a party uses for one
relationship, one community or one trust context and for nothing else, as
VTI-KEY-005, VTI-KEY-006 and VTI-KEY-151 require such identifiers to exist. A
client bootstrap identity (VTI-KEY-002) is one. A relationship DID MAY use any
identifier method, and MAY use one key for every purpose its verification
relationships state; VTI-KEY-020 to VTI-KEY-022 apply to it through those
relationships alone. Key roles, key assurance levels and the requirements from
VTI-KEY-070 onward do not apply to it, except VTI-KEY-154 and VTI-KEY-155: where a
relationship DID lists a person's passkey, it carries `keyRoles` as VTI-KEY-191
requires of a persona DID.

**VTI-KEY-191** — A persona DID is a person's own identity. It MAY have a single
key, and MAY list passkeys bound to the `authenticator` role (VTI-KEY-154). It binds
no other key role and has no key assurance level. Where it lists an `authenticator`
key, its document MUST carry `keyRoles` listing every such key under
`authenticator`, and MUST NOT list any other role there. A verifier MUST treat a
method that a persona DID lists under `authenticator` as VTI-KEY-155 requires, and
every other method of a persona DID through its verification relationships alone.

**VTI-KEY-192** — A service DID is the durable identity of a VTA, a VTC, a VTN, a
trust registry, a host service, a mediator, or an integration a VTA provisions. Its
operator chooses its key assurance level under VTI-KEY-194, and the requirements of
this chapter that bind it are those of its level (VTI-KEY-195).

**VTI-KEY-193** — A DID MUST belong to one class only. A persona DID MUST NOT be
used as the identity of a node, a service or an integration; a service DID MUST
NOT list a key held by a person (VTI-KEY-154); a relationship DID MUST NOT be used
outside the relationship it exists for (VTI-KEY-007).

*Rationale for VTI-KEY-190 to VTI-KEY-193.* The requirements that follow protect
artefacts that third parties rely on without contacting the signer, and a service
that must survive the compromise or loss of the machine that runs it. A
relationship DID signs nothing a stranger relies on, and is replaced rather than
recovered when its key is lost; binding it to key roles would cost every pairwise
exchange a document it does not need. A persona DID is a person's, and the one
thing a verifier must be able to tell about its keys is which one is the person's
own authenticator, because only an assertion by that key shows that the person was
present; a key held by the person's VTA can sign anything the VTA is asked to
sign, so a verifier that could mistake one for the other would accept a VTA's
signature as a person's presence. That is the whole of what `keyRoles` does on a
persona DID. A DID used in two classes inherits the obligations of neither: a
persona that is also a node's identity puts a person's key in a node's document,
which VTI-KEY-154 forbids for that reason.

*Note.* Witnesses, identifier-log hosts, push gateways, room hosts and mediators are
service DIDs, so their operators may choose any level, and a verifier's policy may
ask for one. A service that confers no authority — a mediator, which a recipient
never relies on for anything but routing — loses nothing at level 1. Where an
identifier method fixes the form of a key a service signs with for that method —
`did:webvh` names a witness by a `did:key` — that key is the method's and not a key
role of the service DID.

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

The key role named `attestation` below is named so as not to be confused with
an [[ref: approver]] — a subject that satisfies a consent requirement under the
Approvals, Consent and Step-Up chapter. The two are related in one direction
only: an approval rule may govern whether a node decides to issue an
attestation artefact, and the `attestation` key is what makes the decision,
once taken, verifiable by a third party.

**VTI-KEY-070** — A service DID at key assurance level 2 or 3 MUST bind every
key it publishes, and the key or keys holding its identifier-log update
authority, to exactly one key role registered under VTI-KEY-072. This binding is
how such an identity satisfies VTI-KEY-020. It applies whatever the identity's
identifier method; what depends on the method is stated by the method's profile
(VTI-KEY-172). An identity satisfying VTI-KEY-070 through VTI-KEY-096 is a
[[ref: key-role identity]]. A service DID at level 1 binds no key role, and a
persona DID binds only `authenticator` (VTI-KEY-191).

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
| `attestation` | attestation artefacts (VTI-KEY-080) | `assertionMethod`, and no other | `assertionMethod` | held only by the VTA; at level 3 generated, non-exportable and in no backup (VTI-KEY-110) |
| `operational` | the node's own operational material (VTI-KEY-081) | `authentication`, and no other | `authentication` | MAY be held by the node's service process (VTI-KEY-114) |
| `messaging` | key agreement for encrypted transports; signs nothing | `keyAgreement`, and no other | none | MAY be held by the node's service process (VTI-KEY-114) |
| `update` | entries in the identifier's verifiable history | none: named only as the method's update authority, never as a verification method | as the identifier method defines for log entries | the routine key held only by the VTA (VTI-KEY-113); at level 3, a recovery key held by a recovery custodian outside the VTA's protection boundary (VTI-KEY-116, VTI-KEY-205) |
| `authenticator` | a person's WebAuthn assertions — sign-in and step-up evidence; signs no Data Integrity proof | `authentication`, and no other | none | held on the person's own authenticator; never by a VTA, and never on a node's identity (VTI-KEY-154) |

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

*Rationale for VTI-KEY-075.* An operational request that invokes authority at
a peer — a Trust Task request, as opposed to a response — is signed with an
`operational` key in `authentication`, like the rest of the node's operational
material. The authority it invokes is established by the peer's access control
entry for the node, not by the relationship the key appears in, so moving
requests to `capabilityInvocation` would add a second operational key without
adding a check. The capability relationships are reserved for a role that is
registered for them, and until one is, a method listed there is one a verifier
has no rule for.

#### Services operated alongside a node

A node is often deployed with companion services: a forge bridge beside a VTC, a
status-list host, a website host. Each is a separate process with a separate
exposure, and the question is whether it signs as the node or as itself.

**VTI-KEY-076** — A service operated alongside a node MUST have an identifier
and keys of its own, and MUST sign as itself. Its keys are not key roles of the
node, and the node's identifier document MUST NOT list them.

**VTI-KEY-077** — A verifier MUST NOT accept a signature by such a service as a
signature of the node. Authority the service holds on the node's behalf MUST be
recorded as an access control entry (VTI-VTA-032) or by an attestation artefact
the node issues to the service, and never by the service's key appearing in the
node's document.

*Rationale.* A service that signs as the node is a copy of the node's signing
authority in another process, reachable by whoever compromises that process, and
indistinguishable from the node in every record. A service that signs as itself
can be scoped, revoked and audited separately — which is the argument of
VTI-VTA-040 applied to infrastructure rather than to agents.

### Key assurance levels

The protections of this chapter are layered, and each layer answers one threat:

- **Separating roles, with the `attestation` and `update` keys held by the VTA**,
  protects against compromise of the node's service process: the process that
  parses untrusted network input holds only keys that sign traffic, so its
  compromise yields a caller of the VTA's signing oracle and not the community's
  seal (VTI-KEY-111, VTI-KEY-115).
- **Generating those keys and keeping them out of every backup** protects against
  theft of a backup or exposure of the seed: neither yields the seal or the
  identifier.
- **A recovery key held outside the VTA** protects against compromise or loss of
  the VTA itself. It is needed against loss *because* of the previous layer: once
  the role keys are in no backup, a lost VTA loses them, and only a key held
  elsewhere can take the identifier back.

Not every service needs every layer, and each one has a cost the operator pays.
A [[ref: key assurance level]] names how many of them a service DID has. The
operator chooses it; every level is conformant; and each verifier's policy decides
which level it requires for which use.

| Level | Name | Shape | Protects against | After the VTA is lost | After the VTA is compromised |
|---|---|---|---|---|---|
| 1 | single key | One signing key for everything, as every node identity had before key roles; no `keyRoles` | — | restore from backup | the identity is lost |
| 2 | role-separated | `keyRoles`; the VTA holds the `attestation` and `update` keys; they MAY be derived from the seed and MAY be in the VTA's backup | service compromise | restore from backup | deactivate, and establish a successor identity |
| 3 | recoverable | Level 2, with `attestation` and `update` keys generated and in no backup, a recovery key held by a recovery custodian outside the VTA, and a witness enforcing it | service compromise, backup theft and seed exposure, VTA compromise, VTA loss | recover with the recovery key | recover with the recovery key |

**VTI-KEY-194** — Every service DID has a key assurance level of 1, 2 or 3, chosen
by its operator, and is conformant at every level. A service DID satisfies the
requirements of its own level and of every level below it, as VTI-KEY-195 states.
Nothing in this specification requires a verifier to refuse a level; VTI-KEY-200
leaves that to its policy.

**VTI-KEY-195** — The requirements of this chapter bind a service DID as follows. A
requirement not listed binds every service DID whatever its level.

| Requirements | Level 1 | Level 2 | Level 3 |
|---|---|---|---|
| VTI-KEY-070 – VTI-KEY-075 (binding key roles) | — | ✓ | ✓ |
| VTI-KEY-080 – VTI-KEY-083, VTI-KEY-101 – VTI-KEY-105 (what each role signs; verifying by role) | — : one key signs all, judged by relationship (VTI-KEY-022) | ✓ | ✓ |
| VTI-KEY-090 – VTI-KEY-096 (`keyRoles`) | — | ✓ | ✓ |
| VTI-KEY-110 (`attestation` custody) | — | held only by the VTA; MAY be derived; MAY be in the VTA's backup | generated, non-exportable, in no backup |
| VTI-KEY-111, VTI-KEY-113, VTI-KEY-115 (the service process holds no `attestation` or `update` key) | — | ✓ | ✓ |
| VTI-KEY-112 (keys in no backup) | — | only in the VTA's own backup | ✓ |
| VTI-KEY-116 (generated update keys; the recovery key) | — | — : successors MAY be derived; no recovery key | ✓ |
| VTI-KEY-117 (recovery after loss) | restore from backup | restore from backup | ✓ |
| VTI-KEY-118, VTI-KEY-119, VTI-KEY-128, VTI-KEY-205, VTI-KEY-206 (protection at rest; the recovery commitment and its custodian) | — | — | ✓ |
| VTI-KEY-120 – VTI-KEY-127 (rotation and revocation by role) | — : VTI-KEY-040 – VTI-KEY-043 | ✓ | ✓ |
| VTI-KEY-129 (a witness enforcing the level and the recovery commitment) | — | SHOULD | ✓ |
| VTI-KEY-132 (retirement bounds issuance) | — | ✓, resting on the custody of the VTA's backups | ✓ |
| VTI-KEY-170 – VTI-KEY-173 (method eligibility) | — : VTI-KEY-004 | ✓ | ✓ |
| VTI-KEY-180 – VTI-KEY-183 (approving changes) | — | ✓ | ✓ |
| VTI-KEY-184, its X25519 clause (no X25519 key computed from an Ed25519 key) | SHOULD, for a new identity | ✓ | ✓ |

VTI-KEY-210 to VTI-KEY-221 bind wherever a threshold custodian is used.

**VTI-KEY-196** — A VTC and a VTN SHOULD be at level 2 or higher, and SHOULD be at
level 3 where the attestation artefacts it issues confer authority on others — a
grant of the standing to vet or endorse, or a listing in a network. A VTA's own
identity SHOULD be at level 2 or higher.

**VTI-KEY-197** — A service DID at level 2 or 3 MUST declare its level in its
identifier document, in a top-level `keyAssurance` member: an object whose `level`
is the integer 2 or 3. At level 3, `keyAssurance` MUST also carry
`recoveryCommitment`: the recovery commitment of VTI-KEY-116 as it appears among the
identifier method's next-key commitments in the same version, in the form the
method profile states (VTI-KEY-172). A service DID at level 1 MAY declare
`{ "level": 1 }`, and MAY omit `keyAssurance`; a document without it is at level 1.
`keyAssurance` MUST carry nothing else.

**VTI-KEY-198** — Where an identifier document is processed as JSON-LD, its
`@context` MUST define `keyAssurance` with the term definition below:

```json
{ "keyAssurance": { "@id": "https://w3id.org/vti#keyAssurance", "@type": "@json" } }
```

**VTI-KEY-199** — A verifier MUST establish a service DID's level for a version of
its document from that version and, where the method has one, its verifiable
history, and MUST NOT take a level higher than the structure supports:

1. The level is the declared `keyAssurance.level`, or 1 where none is declared.
2. A version declaring level 2 or 3 is at level 1 where its document carries no
   `keyRoles`, or lists one verification method in more than one verification
   relationship.
3. A version declaring level 3 is at most level 2 where `keyAssurance` carries no
   `recoveryCommitment`; where the recovery commitment is not among the method's
   next-key commitments in that version, or is the only one; where the method has
   witnesses and the version is not witnessed (VTI-KEY-129); or where the
   recovery commitment differs from the previous version's and the version is
   neither authorized by the previous recovery key nor witnessed.
4. Where the history shows a version declaring a lower level than an earlier one
   (VTI-KEY-148), every version from it on is at level 1.

A method that cannot show what an item needs — a method whose next-key commitments
a verifier cannot read, for example — supports no level above the one the items it
can show allow.

**VTI-KEY-200** — A verifier's policy decides the minimum level it accepts for each
use: for each kind of attestation artefact, for operational material, and for each
issuer or class of issuer it relies on. A verifier MUST apply that minimum to the
level established under VTI-KEY-199, MUST refuse material from a service DID below
it, and MUST NOT treat a service DID as conforming to a level its document does not
show. A verifier MAY accept level 1.

**VTI-KEY-201** — A verifier MUST judge an attestation artefact at the level of the
signer's identity in the versions in which the artefact's signing key is bound to
`attestation`, or, at level 1, listed in `assertionMethod`, and not at the level of
the current version. Because a raise in level replaces every key whose custody the
new level tightens (VTI-KEY-147), an `attestation` key is bound at one level only,
so the level of an artefact is fixed by the key that signed it, whatever dates the
artefact carries.

*Rationale for VTI-KEY-194 and VTI-KEY-200.* Earlier drafts made a single-key node
identity non-conformant and required every verifier to refuse its credentials. That
put the whole cost of the transition on one coordinated date and gave no operator a
way to choose less than the most expensive custody. The levels let each operator
buy the protection its service needs, and let each verifier say what it needs; the
specification's job is to make the level something a verifier can establish
rather than take on trust, and to say where it cannot.

*Rationale for VTI-KEY-197 to VTI-KEY-199.* `keyRoles` alone cannot carry the
level. It is visible, so level 2 could be inferred from it, but a persona DID can
carry `keyRoles` too (VTI-KEY-191), and level 3 cannot be inferred at all: a
recovery commitment is a hash among the method's next-key commitments, and nothing
distinguishes it from a second routine successor. A declared level, checked against
the structure the level requires, gives the verifier one value to apply its policy
to and caps it at what the document and history show. Naming the recovery
commitment in the document is what lets a witness enforce its continuity
(VTI-KEY-129) and a verifier check that it is carried forward; it reveals which
committed key is held elsewhere, which an observer could guess from the level, and
nothing about who holds it. Treating a lowered level as level 1 fails closed on
the one change a compromised VTA would make to disarm recovery.

*Note — what the level does not show.* A level is partly a statement the operator
makes and a verifier cannot check. A verifier can establish that the roles are
separate keys in the right relationships (level 2), that a distinct recovery
commitment is named, carried forward and witnessed (level 3), and that the level
never fell. It cannot establish, from the document or the history:

- which process holds the `attestation` and `operational` keys — that the service
  process holds no `attestation` key (VTI-KEY-111) is the operator's statement at
  level 2 and at level 3;
- that the `attestation` and `update` keys were generated rather than derived, are
  in no backup and are protected at rest under a key the seed cannot produce
  (VTI-KEY-110, VTI-KEY-112, VTI-KEY-118);
- that the recovery key is held outside the VTA's protection boundary, or by which
  kind of custodian (VTI-KEY-205) — the VTA checks a proof of possession and refuses
  a key it generated or can derive, but cannot see where the key is held;
- that a witness is outside the VTA's boundary and applies the policy VTI-KEY-129
  requires.

A verifier whose policy requires more than the structure shows needs evidence
outside this chapter — an attestation of the VTA's environment, or an agreement
with the operator. A declared level is to be read as what it is: a checked
structure and an unchecked custody claim.

*Note — choosing a level.* Level 1 suits a service whose signatures nobody relies on
after delivery. Level 2 is the step with the largest return for the least cost: it
needs no new party and no new ceremony, backups keep working, and a compromise of
the most exposed process no longer yields the community's seal. Level 3 adds a
recovery custodian and a witness outside the VTA, and changes what a backup
restores; it is for an identity whose loss or capture would harm parties who cannot
easily be told to trust a successor.

### What each role signs

The requirements from here to the end of *Binding roles in the identifier
document* bind a service DID at level 2 or 3 (VTI-KEY-195). At level 1, the
identity's one signing key signs everything below, and a verifier applies
VTI-KEY-022 through the verification relationship the key is listed in.

**VTI-KEY-080** — The following are [[ref: attestation artefact]]s, and a node MUST sign
each with an `attestation` key and with no key of another role:

1. every membership credential, including each credential of a pair that
   commit to one another (VTI-MEM-021);
2. every role credential;
3. every endorsement credential, including one of a community-defined
   endorsement type, and every grant that confers the standing to vet or
   endorse others;
4. every status list credential, and every other artefact that states the
   status of an attestation artefact; and
5. every other verifiable credential the node issues.

**VTI-KEY-081** — A node MUST sign the following with an `operational` key and
with no key of another role: invitations; removal and departure notices; Trust
Task request and response documents (VTI-OPS-020); signatures carried by
DIDComm or TSP messages; and audit checkpoints.

**VTI-KEY-082** — An artefact not named in VTI-KEY-080 or VTI-KEY-081 MUST be
treated as an attestation artefact if it asserts, to any party other than its
immediate recipient, a decision of the node about a subject's membership,
standing, authority or status. Every other artefact the node signs MUST be
signed with an `operational` key.

**VTI-KEY-083** — A verifier MUST NOT treat material signed with an
`operational` key as evidence of a decision that VTI-KEY-080 requires an
attestation artefact to evidence. A removal notice informs its recipient; the
removal takes effect for relying parties through the status an `attestation` key
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
decision is made, and the membership credential that follows is an attestation
artefact. Keeping this material on a key the service process holds is what lets
the service run without asking the VTA to sign every message, and VTI-KEY-083
is what keeps that convenience from reaching the decisions.

#### Registry records

What a Trust Registry answers about a community is only as authentic as the
channel it arrives by, unless the answer carries a proof. Registries do not yet
store the proof of the artefact a record was derived from, or sign their
answers, so an `attestation` proof on a registry record is a target rather than a
present fact.

**VTI-KEY-084** — A node writing to a Trust Registry MUST do so by an operation
document signed with its `operational` key (at level 1, its signing key, under
`authentication`), and a registry MUST attribute a write to a node only where the
document's proof verifies under that key.

**VTI-KEY-085** — A record a registry publishes on a node's behalf SHOULD carry
the node's `attestation` proof over the record's content, and a registry SHOULD
return that proof with every answer derived from the record. Where the
registry's query protocol defines a member for a record's proof, the record
MUST carry the proof and the registry MUST return it.

**VTI-KEY-086** — Until a registry answer carries a proof under
VTI-KEY-085, a consumer MUST treat it as an assertion of the registry, bounded
by VTI-REG-010 and VTI-REG-011, and MUST NOT treat it as an attestation artefact
of the node.

*Open issue.* The registry query protocol this specification binds carries no
member for a record's proof, so the MUST in VTI-KEY-085 binds nothing yet. The
change needed is to that protocol, not to this requirement.

### Binding roles in the identifier document

A node's identifier document states each key's role twice: once through the
standard verification relationship, which every DID-aware verifier already
reads, and once through a `keyRoles` property, which says which role the node
meant. The relationships are what generic tooling checks; the property is what
lets a VTI verifier distinguish two keys that share a relationship, and refuse
the one that should not have signed.

**VTI-KEY-090** — The identifier document of a service DID at level 2 or 3 MUST carry a `keyRoles` property:
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
authority is stated only where the identifier method states it, as its method
profile says (VTI-KEY-172) — for `did:webvh` and its `did:scid` form, the log's
update keys and next-key commitments — and an update key MUST NOT appear as a
verification method in the document.

**VTI-KEY-094** — A verifier MUST select a node's key by role, through
`keyRoles` and the verification relationship together. It MUST NOT select or
accept a key by its fragment, its position in the document, its type or its
algorithm.

A non-normative example, for a VTC with an Ed25519 and an ML-DSA-44 `attestation`
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
    "attestation": ["#z6Mkq…", "#z2Sy…"],
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
anywhere a verifier can check, and it changes with every template.

**VTI-KEY-095** — `keyRoles` MUST be a top-level member of the identifier
document. It MUST NOT be carried instead in the identifier method's own
parameters, or in a resource the document links to.

**VTI-KEY-096** — Where an identifier document is processed as JSON-LD, its
`@context` MUST define `keyRoles` with the term definition below, and role
identifiers are index keys, compared as strings:

```json
{ "keyRoles": { "@id": "https://w3id.org/vti#keyRoles", "@container": "@index", "@type": "@id" } }
```

*Rationale for VTI-KEY-095.* The property qualifies the verification
relationships, so it has to arrive with them. Method parameters differ by
method and many resolvers do not return them; a linked resource is a second
fetch whose authenticity the verifier has to establish separately, and whose
absence a verifier would read as "no roles" — the failure VTI-KEY-092 exists to
make safe, not to invite. In the document, it is covered by everything that
makes the document authentic.

*Open issue.* The IRI `https://w3id.org/vti#keyRoles` and the context URL
`https://w3id.org/vti/v1` need a w3id.org registration redirecting to a context
document the working group publishes. The term definition is fixed by
VTI-KEY-096; only where it is served remains to be done.

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

**VTI-KEY-101** — Where the signer is a service DID at level 2 or 3, a verifier
MUST accept an attestation artefact only where it verifies under a method bound to
`attestation`, and MUST accept material that VTI-KEY-081 assigns to the
`operational` role only where it verifies under a method bound to `operational`.
Material signed under a key of the other role MUST be refused, as VTI-KEY-022
requires. Where the signer is at level 1, VTI-KEY-100 is the check: an attestation
artefact verifies under a method listed in `assertionMethod`.

**VTI-KEY-102** — Where an artefact carries a set of proofs, every proof that
the verifier evaluates MUST verify and MUST satisfy VTI-KEY-100 and
VTI-KEY-101, and at least one proof MUST be evaluated. A proof whose
cryptosuite is outside the verifier's accepted set (VTI-KEY-013) is not
evaluated. A verifier MUST refuse the artefact where any evaluated proof fails;
it MUST NOT accept an artefact because some proof in the set verifies.

**VTI-KEY-103** — A node MAY sign an attestation artefact with more than one
`attestation` key of different algorithms — an Ed25519 proof and an ML-DSA-44
proof, for example — as a proof set. Every key in the set MUST be bound to
`attestation`, and every proof MUST be by the same controller.

**VTI-KEY-104** — A verifier's policy MAY require that a proof set include a
proof under a named cryptosuite — a verifier that implements ML-DSA MAY require
the ML-DSA-44 proof, for example. A verifier with such a policy MUST refuse an
artefact that does not carry a verifying proof under each cryptosuite it
requires, whatever other proofs verify.

**VTI-KEY-105** — A VTA, a VTC and a VTN SHOULD publish the cryptosuites its
attestation artefacts carry, in a form a verifier can read before verifying, so
that a verifier whose policy requires a cryptosuite can tell an artefact from
which a proof has been stripped from one that never carried it.

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

*Rationale for VTI-KEY-104 and VTI-KEY-105.* The same exemption means that an
artefact stripped of the proof a verifier cannot evaluate looks, to that
verifier, like an artefact that never carried it — harmless to a verifier that
could not check the proof anyway, but not to one that can. A post-quantum-aware
verifier that requires the ML-DSA proof closes the gap for itself; publishing
which cryptosuites a node's artefacts carry is what lets it decide to require
one, and lets a verifier that has not decided detect the stripping.

**VTI-KEY-106** — A proof made with an `operational` key MUST carry the proof
purpose `authentication`, including a proof on a Trust Task request or response
document. An attestation artefact carried inside such a document keeps its own
`assertionMethod` proof.

**VTI-KEY-107** — A verifier that accepts an `authentication` proof carrying no
challenge or domain MUST refuse it unless the document it covers carries an
intended recipient, a time of issue and a document identifier (VTI-OPS-020,
VTI-OPS-025), and MUST apply to that document VTI-OPS-023 (it is the intended
recipient), VTI-OPS-024 (the time of issue is within its acceptance window) and
VTI-OPS-026 and VTI-OPS-027 (the identifier has not already been accepted, on
any binding). A verifier that does not apply all four MUST refuse such a proof.

*Rationale for VTI-KEY-107.* Challenge and domain are what bind an
`authentication` proof to one verifier and one exchange. Without them the proof
is bound by nothing but the document's own members and the verifier's checks on
them, so those checks are the replay protection, and a verifier that skips any
one accepts a proof that can be replayed to it, or to another recipient, or
after its window has closed.

*Note.* The examples in the Trust Tasks framework show task documents with the
proof purpose `assertionMethod`, which VTI-KEY-091 forbids an `operational` key
to appear under. They are to be updated to `authentication` together with the
proposal, made separately to that framework's Proof section, of the rule that a
proof's verification method is listed in the relationship its proof purpose
names ([TRUST-TASKS]).

### Custody of role keys

This section binds a service DID at level 2 or 3, each requirement as VTI-KEY-195
states. Where a requirement differs by level, it says how.

**VTI-KEY-110** — An `attestation` key MUST be held only by the VTA and used only by
it, as the signing oracle of the Verifiable Trust Agent chapter, and MUST NOT be
exportable by any operation, including an export under VTI-VTA-003, other than in
the VTA's own backup where VTI-KEY-112 permits that. At level 3, it MUST be
generated within the VTA's protection boundary, not derived — it is exempt from
VTI-KEY-030 — and marked non-exportable at creation. At level 2, it MAY be derived
from the context's derivation base or generated.

**VTI-KEY-111** — For a VTC or a VTN, the `attestation` keys MUST be held by the VTA
the node is provisioned on, and the node's service process MUST NOT hold them.
The node obtains every attestation artefact by a signing request to that VTA,
which applies VTI-VTA-004 through VTI-VTA-007 to it.

**VTI-KEY-112** — At level 3, an `attestation` key or an `update` key MUST NOT be
included in any backup, export or state transfer, including a backup of a VTC and
a backup of the VTA under VTI-VTA-050. At level 2, such a key MAY be included in a
backup of the VTA that holds it, protected as VTI-VTA-050 requires, and MUST NOT be
included in any other backup, export or transfer, including a backup of a VTC and
a provisioning bundle (VTI-VTA-033).

**VTI-KEY-113** — An `update` key MUST be held by the VTA with the custody
VTI-KEY-110 requires of an `attestation` key at the identity's level, and MUST be
distinct from every key of every other role. A process that holds an
`operational` or `messaging` key MUST NOT be able to produce an entry in the
identifier's verifiable history.

**VTI-KEY-114** — An `operational` or `messaging` key MAY be held by the node's
service process. It MUST NOT be derived from, or be used to derive, any other
secret: a storage encryption key, an audit chaining key, an installation or
enrolment secret, a session secret, or a key of another role. Each such secret
MUST be generated or derived independently of every signing key. At level 1, this
binds the identity's signing and key-agreement keys.

**VTI-KEY-115** — A deployment MUST be such that compromise of a node's service
process, including every key and secret it holds, does not yield the ability to
produce an attestation artefact or an identifier-log entry other than by a request
the VTA authorizes and records.

**VTI-KEY-116** — Every version of a level-2 or level-3 identity's history MUST
commit to a routine successor of its update key, held by the VTA (VTI-KEY-124). At
level 2, the successor MAY be derived. At level 3, an `update` key, and every
committed successor update key, MUST be generated rather than derived, and is
exempt from VTI-KEY-030; and every version MUST also commit, beside the routine
successor, to a **recovery key** held by a [[ref: recovery custodian]] in a
[[ref: protection boundary]] other than the VTA's (VTI-KEY-205). The VTA MUST NOT
hold, generate or be able to derive the recovery key.

**VTI-KEY-117** — At level 3, recovery of a node's identity after the loss of its
VTA MUST rely on the recovery key (VTI-KEY-116) and on re-issuance, and on nothing
restored from a backup. The recovering VTA MUST publish, in one history entry
authorized by the recovery key, new `attestation` keys, new routine update keys,
a fresh recovery commitment, and the retirement of the lost `attestation` keys.
It MUST re-sign every status list at once, and MUST re-issue every other
attestation artefact still in force no later than its renewal. Where the loss
may have exposed the keys rather than destroyed them, the retirement MUST be a
revocation for compromise. At level 1 and level 2, a lost VTA is recovered by
restoring its backup, which carries the identity's keys (VTI-KEY-112); where that
backup is also lost, no committed key remains, and VTI-KEY-124 applies.

**VTI-KEY-118** — Two keys are in the same protection boundary whenever one
compromise or one loss reaches both — including through a backup, a seed, a key
derived from a seed, or credentials one holds to the other's store. At level 3, an
`attestation` or `update` key MUST be protected at rest under a key that is not
derivable from the VTA's seed and is not present in, or recoverable from, any
backup.

**VTI-KEY-119** — A VTA MUST carry a level-3 identity's recovery commitment forward
unchanged in every version it publishes, except one authorized by the recovery key
or one that replaces the commitment under VTI-KEY-181, and MUST name it in
`keyAssurance.recoveryCommitment` (VTI-KEY-197). It MUST accept a recovery
commitment only with a proof of possession of the recovery key bound to the change
that commits it, and MUST refuse to create a level-3 identity, to raise an identity
to level 3, or to publish a version of a level-3 identity, that would leave it
without one. A VTA MUST obtain the recovery commitment from a recovery custodian
under VTI-KEY-205, and MUST NOT create or raise a level-3 identity without one.

*Note — protection boundaries in practice.* A plain VTA is one boundary: its
host, the store on it and the credentials it holds to its seed backend. A
hardened VTA encrypts its store, but under a key derived from its seed, so it is
the same one boundary. A TEE VTA is a stronger boundary — the host cannot read
inside the enclave — but every key it generates is inside it, so it too is one.
A mnemonic locked in a safe is a second boundary against loss but not against
compromise of any key derived from it, because the running VTA holds the seed.
A second boundary is a party holding a key no VTA of the identity can reach: a
second VTA with its own seed, operator and host; a hardware token kept offline;
a hardware security module or cloud key service whose use needs credentials the
VTA does not hold; or a threshold group of VTAs (VTI-KEY-210). Every VTA is
therefore, by itself, one boundary, which is why level 3 needs a custodian outside
it.

*Rationale for VTI-KEY-110 and VTI-KEY-111.* The service process is the part
of a node that parses untrusted input from the network, and it is therefore the
part most likely to be compromised. What VTI-KEY-111 buys is that such a
compromise yields a caller of the signing oracle rather than a holder of the
key: every attestation it can obtain is one the VTA parsed, constrained to its
shape and addressee, and recorded (VTI-VTA-006), and the caller's authority can
be withdrawn at once (VTI-VTA-002). A stolen key signs whatever the thief
likes, for as long as it stays published, and leaves no record. That is the whole
of level 2, and it needs nothing from the operator but a VTA that holds the keys:
a derived key, or one in the VTA's own backup, is as far from the service process
as a generated one.

*Rationale for VTI-KEY-114.* A secret derived from a signing key falls with it,
and one from which a signing key is derived exposes it. The closed entry for
VTI-AUD-005 in Appendix F records the same reasoning applied to the audit key:
a secret that shares an origin with another is backed up, recovered and
compromised together with it, whether or not anyone intended the two to be
linked.

*Rationale for VTI-KEY-110, VTI-KEY-112 and VTI-KEY-116 to VTI-KEY-118 at level
3.* A key derived from a base that can be backed up is not non-exportable:
whoever holds the backup and its separately held material can re-derive it, so the
backup becomes a second copy of the community's seal and of control of its
identifier, kept wherever backups are kept. Generating the keys removes that copy.
The cost is that a lost VTA loses them, and level 3 pays it with mechanisms that
exist for other reasons: the recovery key recovers control of the identifier, and
re-issuance replaces the artefacts. The recovery key is mandatory at level 3, and
only there, because of that trade: a level-2 identity's keys come back with its
backup, so its loss needs no recovery key; a level-3 identity's do not, so without
one its loss would be permanent. Commitment to a next key protects an identity
against compromise only if the key that authorizes the next change is out of reach
of whoever compromised the current one; a successor key generated and held by the
same VTA is pre-rotation in form and nothing in substance, which is why VTI-KEY-116
requires the recovery key to be held elsewhere and VTI-KEY-118 says what elsewhere
means. A lost `attestation` key is destroyed rather than stolen, so what it signed
before the loss stays valid under VTI-KEY-132 and is replaced at renewal; only the
status lists, which must go on being updated, need re-signing at once.

*Open issue.* Signing an attestation artefact is a constrained signing capability
under VTI-VTA-007, distinct from the generic `sign` and from `sign-trust-task`.
Appendix C does not yet register a capability for it.

#### Recovery custodians

**VTI-KEY-205** — At level 3, the recovery key MUST be held by a recovery custodian
of one of these kinds:

1. **A separately held key**: generated away from every VTA that holds a key of
   the identity, held where none of them can read it, and used on a machine none of
   them controls — a passphrase-encrypted key file kept offline, a hardware token,
   or a hardware security module or key service whose use needs credentials no
   such VTA holds. A key derived from a seed that no such VTA holds, such as a
   recovery mnemonic generated for this purpose, is a separately held key.
2. **A second VTA**, with its own seed, operator and host, holding the recovery key
   with the custody VTI-KEY-110, VTI-KEY-112 and VTI-KEY-118 require of a level-3
   `attestation` key.
3. **A [[ref: threshold custodian]]** (VTI-KEY-210).

A key derivable from the seed of a VTA that holds a key of the identity is never a
recovery key, however it is stored (VTI-KEY-116).

**VTI-KEY-206** — Every recovery custodian MUST check a change it is asked to
authorize as VTI-KEY-128 requires, and a custodian that is a VTA, or a group of
VTAs, MUST require an approval of its own operator, bound to the change it checked
(VTI-KEY-180), before it signs. A VTA MAY record the custodian's kind, and MUST NOT
publish it or anything that identifies the custodian (VTI-KEY-150).

*Rationale for VTI-KEY-205.* What matters about a recovery custodian is that no
single compromise or loss reaches both it and the VTA, not the technology it uses,
which is why the kinds are stated by boundary. A recovery mnemonic is acceptable
for the same reason the VTA's own seed is not: the running VTA holds its own seed,
so any key derived from it falls with the VTA, while a mnemonic generated for the
recovery key alone is reachable only through wherever it is kept. A second VTA and
a threshold group check what they sign themselves, which a key file cannot; a
threshold group also survives the loss of a member and needs more than one
operator to act.

### Threshold custody

A recovery key, and at most an identity's routine update keys, MAY be held by a
group of VTAs none of which holds the key: FROST [RFC 9591], with the Ed25519
ciphersuite, lets any t of n participants produce one ordinary Ed25519 signature
together, while fewer than t can produce nothing and learn nothing about the key.
To the identifier method, to a witness and to a verifier, the group's key is one
Ed25519 key and its signature one Ed25519 signature, so no identifier method has
to change.

**VTI-KEY-210** — A [[ref: threshold custodian]] is a group of n VTAs, its
participants, holding shares of one Ed25519 key under FROST(Ed25519, SHA-512)
[RFC 9591] with a threshold t, where 2 ≤ t < n. A group signature MUST be one that
Ed25519 verification [RFC 8032] accepts, and MUST be used wherever the identifier
method, a proof of possession or a recovery proof expects an Ed25519 signature,
with no change to its form.

**VTI-KEY-211** — Every participant MUST be in a protection boundary of its own,
outside the boundary of every VTA that holds a key of the identity and outside
every other participant's. In particular:

1. no two participants share a seed, a store, a host machine, or — for a virtual
   machine or an enclave — a host or a cloud account;
2. no participant holds credentials that reach another participant's seed, store,
   secret backend or backups, or those of a VTA holding a key of the identity;
3. no participant's backups are readable by another participant or by such a VTA;
4. no subject — an administrator, a delegate, or any credential — holds, at t or
   more participants, the authority to approve a signing.

Participants SHOULD be operated by different people or organisations, and SHOULD be
in different failure domains, such as different providers or regions.

**VTI-KEY-212** — The group's key MUST be generated by a distributed key generation
with no dealer, such that no party, at any time, holds or can compute the group's
secret: the key generation of [FROST20], in which every participant proves
knowledge of its secret term and commits to its polynomial. Every participant MUST
verify each other participant's proof and each share it receives against its
sender's commitments, and MUST abort on any failure; MUST confirm with every other
participant, by comparing a digest of the commitments each received, that all
received the same commitments, before it accepts the group's key; and MUST record
the group's key and every participant's verification share. Key generation by a
trusted dealer (RFC 9591, Appendix C) MUST NOT be used.

**VTI-KEY-213** — A participant MUST hold its share with the custody VTI-KEY-110,
VTI-KEY-112 and VTI-KEY-118 require of a level-3 `attestation` key: generated within
the participant, non-exportable, protected at rest under a key its seed cannot
produce, and included in no backup, export or transfer. A lost share is replaced by
refresh (VTI-KEY-218), never by restore.

**VTI-KEY-214** — Before it contributes to a signing, every participant MUST obtain
the identity's history itself, compute from the unsigned change and the proof
options the exact message the signature will cover, and check the change as
VTI-KEY-128 requires of a recovery custodian, or, for a proof of possession, that
the message is the proof of possession of the group's key for the change it was
shown. It MUST NOT sign a message it did not compute, or on the strength of the
description sent with it, and MUST obtain its own operator's approval under
VTI-KEY-206.

**VTI-KEY-215** — A participant MUST generate its signing nonces as RFC 9591
requires, from fresh randomness and its share, for one signing only. It MUST keep
them only for that signing, in no persistent store and in no backup, and MUST delete
them once its signature share is produced, or when the signing is aborted, expires,
or the participant restarts. It MUST NOT produce a signature share for nonces it
cannot show are unused, and MUST refuse a signing whose commitment list omits its
own commitment, names a participant twice, or has fewer than t members.

**VTI-KEY-216** — The party that coordinates a signing — any participant, or the VTA
that asked for the signature — MUST verify every signature share against its
participant's verification share before aggregating (RFC 9591), and where a share
fails MUST name the participant that produced it, record the failure in its audit
trail and report it to every participant, and exclude that participant from any
retry, which uses fresh nonces. The coordinator is trusted with nothing secret and
with nothing a participant checks for itself.

**VTI-KEY-217** — Participants MUST exchange every message of key generation,
signing and refresh as Trust Tasks over a transport that authenticates the sender
and encrypts to the recipient — TSP where both parties advertise it, otherwise
DIDComm authenticated encryption — and MUST refuse a message not from a participant
of the group, or one already accepted (VTI-OPS-026). The shares of key generation
and refresh MUST be encrypted end to end to the participant they are for.

**VTI-KEY-218** — A group MAY refresh its shares, and in doing so replace, add or
remove participants or change t, without changing the group's key, by a
distributed protocol among at least t current participants in which no party learns
the group's secret. Every participant of the new set MUST verify its new share
against the commitments of the refresh, and every participant of the old set MUST
delete its old share once the refresh completes. The new set MUST satisfy
VTI-KEY-210 and VTI-KEY-211. A refresh changes no identifier document. A group
SHOULD refresh when a participant leaves, is lost, or may be compromised.

**VTI-KEY-219** — Where fewer than t participants can be reached, no signature can
be produced, and the change MUST wait. A participant or coordinator MUST NOT fall
back to reconstructing the key, to a dealer, to a lower threshold, or to a key
outside the group. A group SHOULD confirm regularly that at least t + 1
participants can sign, by signing a challenge that cannot be read as any change of
an identity, and SHOULD refresh to replace a participant before only t remain.
Where fewer than t participants remain for good, the key is lost; for a recovery
key, the operator MUST replace the recovery commitment under VTI-KEY-181 while the
identity's VTA still holds its routine authority.

**VTI-KEY-220** — A key split by secret sharing and reconstructed in one place to
sign — Shamir's scheme, or SLIP-39 — is not a threshold custodian, and MUST NOT be
described or counted as one. Such a split MAY protect a separately held key against
loss, where the reconstructed key and the machine it is reconstructed on meet
VTI-KEY-205 item 1.

**VTI-KEY-221** — A threshold custodian MAY hold a recovery key and the routine
update keys of a level-3 identity, and SHOULD NOT hold an `attestation` key.

*Rationale for VTI-KEY-210 and VTI-KEY-212.* A threshold of at least two means no
participant can sign alone, and a threshold below n means the group survives the
loss of a participant, which is the reason a recovery custodian exists. A dealer
would hold the whole key at the moment it is made, which is the one property the
group exists to avoid; the key generation of [FROST20] has every participant
contribute randomness and prove it, so the key exists only as its shares. Comparing
what each participant received is what stops a participant from sending different
commitments to different peers and splitting the group into parties holding shares
of different keys.

*Rationale for VTI-KEY-214 and VTI-KEY-206.* Every participant checks the change and
asks its own operator, so t participants are t independent checks of the same
entry, each by a party in its own boundary. Where the participants have different
operators, that is the two-approver rule of VTI-KEY-181 enforced by the signature
itself rather than by the VTA's policy; where one person approves at every
participant, it is one approver however large t is, which is why VTI-KEY-211 item 4
forbids one subject approving at t participants.

*Rationale for VTI-KEY-215 and VTI-KEY-216.* A nonce used for two different messages
lets anyone holding both signature shares compute the participant's share, so a
nonce is used once and does not survive anything that could replay it, a restart
or a restore included. FROST is not robust: one participant can make a signing
fail. What it guarantees is that the participant can be identified, so the
remedy is to name it and sign again without it.

*Rationale for VTI-KEY-219 and VTI-KEY-220.* A fallback that reconstructs the key
when too few participants answer is the attack: an adversary who can make
participants unreachable chooses when the key exists in one place. Reconstructing
to sign has the same flaw on every use: at the moment the key matters, it exists
whole on one machine, whose compromise yields it, and only that machine checks what
is signed. Threshold signing never has that moment.

*Rationale for VTI-KEY-221.* A recovery key signs once in the life of most
identities, and a routine update key once per change of the identity, so waiting
for t operators costs little against what it protects. An `attestation` key signs
every credential and every status-list update. Holding it in a group would stop the
community issuing whenever fewer than t operators answer, would multiply the
signing rounds and the nonces whose reuse is fatal by every credential issued, and
would make every participant parse and constrain every artefact (VTI-VTA-004 to
VTI-VTA-007); its compromise, unlike the update authority's, is recoverable by
revocation and re-issuance. Where the identifier method moves the update authority
on every change, as `did:webvh` does, a threshold routine authority also needs a key
generation for every change, so it suits identities that change rarely; its method
profile says what it costs.

### Rotation and revocation by role

Two events retire a key, and a verifier has to be able to tell them apart. A
**planned rotation** replaces a key that is believed sound: what it signed
before it was retired stays valid. A **revocation for compromise** withdraws a
key that is believed to be in someone else's hands from some time — the
compromise time, which is often earlier than the moment the compromise is
discovered — and from that time the key's signatures establish nothing.

This section binds a service DID at level 2 or 3. At level 1, VTI-KEY-040 to
VTI-KEY-043 govern rotation.

**VTI-KEY-120** — A node MUST support, for each key role, both planned rotation
and revocation for compromise, and MUST record each in the identifier's
verifiable history (VTI-KEY-041) such that a verifier can distinguish the two.

**VTI-KEY-121** — A revocation for compromise MUST state the compromise time:
the earliest time from which the key is to be treated as compromised. A node
that cannot establish when a compromise began MUST state the time the key was
first published.

**VTI-KEY-122** — A planned rotation of an `attestation`, `operational` or
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
update key made (VTI-KEY-043). Where a routine update key is compromised and the
VTA's protection boundary is not, the node MUST rotate to the committed routine
successor. Where the boundary may be compromised, a level-3 identity MUST rotate to
the recovery key (VTI-KEY-116); a level-2 identity has no committed key outside the
boundary, so its node MUST deactivate the identifier, if it still can, and
establish a successor identity. Where no uncompromised committed key remains, the
identifier MUST be deactivated, and a successor identity established; a node
MUST NOT continue to operate an identifier whose history it no longer solely
controls.

**VTI-KEY-125** — A node MUST destroy an `attestation` key within its protection
boundary when that key is retired by planned rotation. At level 2, where the key is
derived or is in the VTA's backup, the node MUST destroy the copy the VTA holds,
and copies in backups persist until those backups are destroyed.

*Rationale for VTI-KEY-122.* A verifier holding a cached document learns of a new
key only when it re-resolves. A key used before every cache could have seen it
fails at every verifier holding the old document, and the failure looks like a
forgery. Publishing first, and using only after the validity period has run, is
what makes a planned rotation invisible to the parties relying on the node;
ceasing to sign with the old key at once is what keeps the overlap from
becoming a second active key.

*Rationale for VTI-KEY-125.* Destruction is what lets a retired `attestation` key's
past signatures be judged without evidence of when they were made: a key that
no longer exists cannot have signed anything after it was destroyed, so the
retirement time bounds the issuance time from above (VTI-KEY-132). A retired key
that is kept can still sign, and its signatures carry whatever dates the signer
chooses.

**VTI-KEY-126** — A revocation for compromise MUST be recorded in the
identifier document by keeping the revoked verification method in
`verificationMethod`, outside every verification relationship and `keyRoles`,
with the `revoked` property of W3C Controlled Identifiers [CID] set to the
compromise time. A planned retirement MUST remove the verification method from
the document and MUST NOT set `revoked`.

**VTI-KEY-127** — A node MUST keep a revoked verification method, with its
`revoked` value, in every later version of the document for the life of the
identity. A verifier MUST treat a verification method carrying `revoked` as
revoked for compromise from that time, under VTI-KEY-133.

**VTI-KEY-128** — A history entry authorized by the recovery key MUST commit a
fresh recovery key held as VTI-KEY-116 requires, so that a recovery key
authorizes one entry. A recovery custodian MUST check the entry it is asked to
authorize — that it reveals the key the custodian holds and commits to a fresh
one it also holds, or one held by the custodian the entry is moving the recovery
key to — and MUST NOT authorize on the strength of the requester's description of
it.

**VTI-KEY-129** — Where the identifier method has witnesses, a level-3 identity
MUST be witnessed by at least one witness outside the VTA's protection boundary
whose policy refuses an entry that:

1. declares a lower key assurance level than the entry before it (VTI-KEY-148);
2. declares level 3 without naming, in `keyAssurance.recoveryCommitment`, a value
   among the entry's own next-key commitments and distinct from every other
   (VTI-KEY-197); or
3. names a different recovery commitment from the entry before it, or omits it,
   unless the entry is authorized by the previous recovery key, or the witness is
   shown the approvals VTI-KEY-181 requires for a replacement.

A level-2 identity SHOULD be witnessed by a witness whose policy refuses the first.
Where the method can require the recovery key for every rotation of the change
authority by a next-key threshold, its profile SHOULD so require, and that
threshold MAY stand in for the witness. A witness MAY be operated by the identity's
recovery custodian.

*Rationale for VTI-KEY-126 and VTI-KEY-127.* `revoked` is already defined, already
in the contexts a Multikey document uses, and already acted on by generic Data
Integrity verifiers, which will not verify a proof by a revoked method; a bespoke
member would protect only VTI verifiers. Keeping the method listed outside every
relationship and outside `keyRoles` makes it inert to a VTI verifier (VTI-KEY-092)
while publishing the compromise time where every verifier resolving the current
document will see it, without walking the history. Reserving `revoked` for
compromise is what makes the two events distinguishable (VTI-KEY-120): a verifier
reading it refuses everything the key signed that is not otherwise proven
earlier, which a planned retirement must not cause.

*Rationale for VTI-KEY-129.* VTI-KEY-119 binds a conforming VTA, and a
compromised VTA is not one. Under a method in which every entry is authorized by
a committed key, as in `did:webvh`, the routine successor is in the VTA, so a
party holding the VTA can append an entry dropping the recovery commitment, or
lowering the level, before the operator uses it. Against loss the recovery key
always works; against compromise only a second party can enforce it. Earlier drafts
made the witness a SHOULD, which let an identity claim protection against VTA
compromise that it had only against loss; level 3 claims both, so it needs the
witness. A method whose next-key threshold can require the recovery key for
rotation, as KERI's can, closes the gap without one. Because the recovery
custodian is already outside the VTA's boundary, it is a natural operator of the
witness, so the requirement adds a duty to an existing party rather than a new
party.

### Judging material signed before a rotation

A verifier that finds an attestation artefact signed by a key no longer in the
current document has to decide whether the artefact was issued while the key was
the node's, and the artefact's own dates cannot tell it: they are written by the
signer, and a signer holding a retired key writes whatever dates it likes.

**VTI-KEY-130** — A verifier MUST accept an attestation artefact under a key that
is not in the current document only where: the key was bound to `attestation`, in
`assertionMethod`, in the version of the document current at the artefact's
issuance time; and the key has not been revoked for compromise with a compromise
time at or before the issuance time.

**VTI-KEY-131** — The issuance time used by VTI-KEY-130 MUST be established from
evidence the signer cannot backdate — the identifier's verifiable history, or a
status entry written by the VTA at issuance and published under an `attestation`
key that is not itself revoked for compromise. It MUST NOT be established from
the artefact's own dates alone.

**VTI-KEY-132** — For a key retired by planned rotation under VTI-KEY-125, the
history entry that retired it bounds the issuance time of everything it signed,
and a verifier MAY accept the artefact under VTI-KEY-130 without further
evidence of issuance time. At level 2, that bound holds only as far as the VTA's
backups, and the seed where the key was derived, have not been taken, which is the
risk a verifier accepting level 2 accepts (VTI-KEY-200).

**VTI-KEY-133** — For a key revoked for compromise, a verifier MUST refuse an
attestation artefact unless evidence under VTI-KEY-131 establishes that it was
issued before the compromise time. On such a revocation the node MUST re-issue,
under a sound `attestation` key, every attestation artefact still in force that its
VTA's record of what it signed (VTI-VTA-006) shows it issued, and MUST re-sign
every status list at once.

**VTI-KEY-134** — A verifier MUST re-resolve the signer's identifier before
relying on an attestation artefact where it has not resolved the identifier within
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
node destroys its retired `attestation` key, so the retirement bounds what the key
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

### Approving changes to key roles

**VTI-KEY-180** — A VTA MUST require a step-up bound to a preview the VTA
computed — the complete resulting identifier document, and every change the
entry makes, including any movement of the update authority — before creating a
level-3 identity, before raising an identity's level (VTI-KEY-147), before adding,
rotating, retiring or revoking an `attestation` or `update` key, before replacing a
recovery commitment, and before a recovery under VTI-KEY-117. It SHOULD require one
before creating a level-2 identity. A session elevation MUST NOT satisfy it, and
the VTA MUST apply exactly the previewed change or nothing.

**VTI-KEY-181** — A recovery commitment MUST be replaced by an entry authorized by
the current recovery key (VTI-KEY-128), unless the recovery key is lost. Where it is
lost, the replacement MUST require approvals from two distinct approvers wherever
two eligible approvers exist, and so MUST raising an identity to level 3. Adding,
rotating or retiring an `attestation` key, and rotating a routine update key,
SHOULD. The initiator counts once at most. Where the recovery custodian is a
threshold custodian whose participants satisfy VTI-KEY-211, a replacement it
authorizes needs no further approval: its t participants' approvals (VTI-KEY-206)
are the quorum.

**VTI-KEY-182** — A revocation for compromise MUST NOT require more than one
approval, and MUST NOT be blocked by a pending, staged or overlapping change to
the same role, which it supersedes. Revocation of an `attestation`, `operational`
or `messaging` key MUST be within the authority of the identity's context
administrator.

**VTI-KEY-183** — An approval of a change to a key-role identity MUST be the
approver's own evidence: a proof by a key of the approver's own identifier, or an
assertion by a passkey registered to the approver. A proof by a key that a node
records as acting for the approver — a console signing key, or any other
delegation — MUST NOT count as an approval; no key of the identity being changed,
and no key of any node's own identity, MUST count as an approver.

*Rationale.* The two changes that matter most are the two an attacker holding one
administrator's credential would make first: replacing the seal, and disarming
the recovery. A preview bound into the approval is what makes the approval mean
the change rather than a window in which any change can be made (VTI-APV-013).
Revocation runs the other way: it removes authority and cannot gain any, and
every approval it waits for is time during which a stolen key is trusted.
Replacing the recovery commitment is never urgent, which is why it can afford the
quorum the others cannot always. Having the current recovery key authorize its own
replacement moves the check from the VTA, which a compromise of the VTA disarms, to
the custodian, which it does not reach, and lets a witness verify the replacement
from the entry alone; the quorum remains only for the case the custodian cannot
answer. Raising an identity to level 3 commits a recovery key for the first time,
which is a replacement of nothing by something an attacker would want to choose,
so it takes the same quorum. Creating a level-2 identity commits nothing outside
the VTA and confers nothing that the administrator's credential does not already
reach, so a bound step-up there is a SHOULD.

### Algorithms of role keys

**VTI-KEY-184** — A successor key MUST keep its predecessor's algorithm unless the
request names another. A node MUST NOT substitute an algorithm for one a request
names or a template lists, and MUST refuse a change it cannot make with it. At
level 2 and 3, a node MUST NOT publish an X25519 key computed from an Ed25519 key,
or any key computed from a key of another role. A new level-1 identity SHOULD NOT
publish an X25519 key computed from its Ed25519 key; an existing one MAY keep the
key it has.

*Rationale.* A substitution is a downgrade nobody approved: a deployment meant to
carry a post-quantum `attestation` proof would ship classical keys and report
nothing. A key-agreement key computed from a signing key makes the `messaging`
role a function of the `operational` one, which VTI-KEY-071 forbids by another
route.

### Identifier method profiles

**VTI-KEY-170** — An identifier method can carry a key-role identity only if it
provides: a verifiable history with a change authority; commitment to more than
one successor of the change authority; rotation without change of the
identifier, and deactivation; verification relationships the controller chooses,
with the change authority in none of them; resolution of a past version of the
document; and a document in which `keyRoles` and `revoked` can be published.

**VTI-KEY-171** — A node MUST refuse to create a key-role identity under a method
that does not meet VTI-KEY-170, or for which it holds no method profile.
`did:key`, `did:jwk`, `did:peer` and `did:web` do not meet it.

**VTI-KEY-172** — What this chapter leaves to the identifier method — what the
change authority is, how routine and recovery keys are committed, which changes
move it, versions and past resolution, how roles, `keyRoles` and `revoked` are
rendered, how a recovery commitment is named in `keyAssurance`, how a level and a
raise in level are rendered, the validity period, the artefacts published and the
form of a recovery proof — MUST be stated by a method profile, and only there. The method
profiles are published with the definitions of the key-role operations
([TRUST-TASKS], `vta/dids/_shared/0.1/method-profiles`).

**VTI-KEY-173** — `did:webvh` and its `did:scid:vh:1` form meet VTI-KEY-170.
`did:webs`, as published, does not: its document is derived from its key event
log, every key of the current key state is placed in both `authentication` and
`assertionMethod`, and there is no member for `keyRoles` or `revoked`. A node
MUST NOT create a key-role identity under `did:webs` until the method, or an
extension to it, lets the controller bind keys to roles in the derived document
and keep the key-state keys out of every relationship.

*Open issue.* Making `did:webs` eligible needs either an extension to that method
in which role keys are anchored in the key event log and the key-state keys are
rendered into no relationship, or an amendment to VTI-KEY-093 tolerating a
change-authority key that the method itself renders, absent from `keyRoles` and
never used to sign anything but log events. The first keeps VTI-KEY-093 intact and
is preferred; the method profile states the mapping either would use.

### Level-1 identities and raising a level

Every node identity created before key roles has one key in both
`assertionMethod` and `authentication`, held by the service process and signing
everything. That is level 1. It is conformant, and it stays usable for every
verifier whose policy accepts level 1; what it cannot do is meet a policy that
requires more. This section says how an identity moves to a higher level without
changing its identifier, and why it can never move down.

**VTI-KEY-140** — *Withdrawn.*

**VTI-KEY-141** — *Withdrawn.*

**VTI-KEY-142** — *Withdrawn.*

**VTI-KEY-143** — *Withdrawn.*

**VTI-KEY-144** — *Withdrawn.*

**VTI-KEY-145** — *Withdrawn.*

*Note.* VTI-KEY-140 through VTI-KEY-145 specified an in-place migration with a
grace period, and are withdrawn. VTI-KEY-146 through VTI-KEY-149 replace them.

**VTI-KEY-146** — A service DID whose document declares no key assurance level, or
declares level 1, is at level 1, including one that carries no `keyRoles` or that
lists one key in more than one verification relationship. Such an identity is
conformant, and no requirement of this chapter that VTI-KEY-195 does not apply to
level 1 binds it.

**VTI-KEY-147** — A service DID MAY be raised from level 1 to level 2 or 3, or from
level 2 to level 3, without changing its identifier, by one history entry,
authorized by its current update authority, that:

1. publishes fresh keys, minted as the new level requires, for every role whose
   custody the new level tightens: from level 1, every role, with the routine
   update key and its committed successor; from level 2 to level 3, the
   `attestation` keys and the routine update key and its committed successor;
2. removes every key it replaces from every verification relationship and from
   `keyRoles` — retiring it, or, where it may have been exposed, revoking it under
   VTI-KEY-126 with a compromise time under VTI-KEY-121;
3. declares the new level in `keyAssurance` (VTI-KEY-197), and, at level 3, commits
   the recovery key (VTI-KEY-119) and is witnessed (VTI-KEY-129).

No key MAY keep its place across a raise unless its custody already meets the new
level, as the `operational` and `messaging` keys of a level-2 identity meet level 3.
Key material MUST NOT move from one role to another in a raise (VTI-KEY-071): the
single key of a level-1 identity becomes no role's key.

**VTI-KEY-148** — A service DID's key assurance level MUST NOT be lowered. A VTA MUST
refuse to publish a version that declares a lower level than the version before
it, and an operator wanting a lower level MUST create a new identity, from which the
node issues afresh, and SHOULD then deactivate the old one (VTI-KEY-063).

**VTI-KEY-149** — On a raise, a node MUST re-sign every status list under the new
`attestation` keys at once. An attestation artefact signed before the raise stays an
artefact of the lower level (VTI-KEY-201); the node SHOULD re-issue under the new
keys every one still in force whose relying parties require the higher level, and
MUST re-issue every one signed by a key the raise revoked (VTI-KEY-133).

*Rationale for VTI-KEY-146 and VTI-KEY-147.* Earlier drafts allowed no in-place
change, on the ground that a level-1 key has had operational custody and so cannot
be retrofitted as an `attestation` key, and is not destroyed, so its retirement
bounds nothing it signed. Both are true of the old key, and neither requires the
identifier to change. A raise never retrofits the old key: it replaces it, and the
new `attestation` key is bound at the new level only. A verifier judges an artefact
at the level of the key that signed it (VTI-KEY-201), so whatever the old key signs
— before the raise, or after it under a backdated date by someone who stole it —
remains a level-1 artefact, which a verifier requiring level 2 refuses and a
verifier accepting level 1 would have accepted from that key anyway. The raise moves
the update authority to keys the old key's custodian never held, so the old key can
no longer change the identity. What a raise cannot certify is the past: if the
level-1 key was stolen before the raise, the thief may have acted first, which is
true of every rotation and is what a revocation in item 2 is for. Keeping the
identifier spares members, registries and relying parties a migration that would
have bought them nothing.

*Rationale for VTI-KEY-148.* A level is a promise to every verifier that has applied
its policy to it. Lowering it is the change an attacker holding the VTA would make
to disarm a level-3 identity's recovery, so making it a legitimate change would make
the attack indistinguishable from the operator's decision, to every witness. Because
no conforming VTA lowers a level, a witness can refuse every entry that does
(VTI-KEY-129), and a verifier can treat one as a compromise (VTI-KEY-199 item 4).

*Note.* In-place raising applies to an identifier whose method meets VTI-KEY-170 for
the target level. An identifier under a method that does not — a `did:web` identity,
for example — reaches a higher level only as a new identity.

### Privacy of key roles

**VTI-KEY-150** — `keyRoles` MUST contain only registered role identifiers and
verification method identifiers, and `keyAssurance` only what VTI-KEY-197 permits.
A node MUST NOT publish in its identifier document, in a verification method
identifier or elsewhere, the custody, location, host, operator, device or personal
data behind a key, the kind or identity of a recovery custodian, nor any structure
of the node's deployment beyond the roles and the level a verifier needs.
A verification method identifier MUST NOT reveal the key's role or anything
about the key beyond its own identity, and SHOULD be derived from the key.

**VTI-KEY-151** — A node's durable identifier document MUST NOT list a key that
exists for a single member, context or relationship. Such a key belongs to a
relationship DID (VTI-KEY-005, VTI-KEY-190).

**VTI-KEY-152** — Where material sent to a single recipient need not be relied
on by any other party, a node SHOULD authenticate it by authenticated encryption
under its `messaging` key rather than by a signature, so that the recipient does
not hold transferable proof that the node sent it.

**VTI-KEY-153** — A node SHOULD rotate its `attestation` keys on a schedule that is
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

*Note.* A node's `attestation` and `operational` keys are long-lived and
publicly resolvable, as its identifier is (VTI-KEY-007). Everything they sign is
linkable to the node by design: an issuer that could not be identified could
not be trusted. What these requirements prevent is the keys revealing more than
the issuer — how the node is built, who operates it, or whom it has been
dealing with.

### Authenticator keys

**VTI-KEY-154** — A key held on a person's own authenticator — a WebAuthn passkey —
MAY appear in the identifier document of that person's own identity — a persona
DID, or a relationship DID the person holds — bound to the `authenticator` role as
VTI-KEY-191 states, in `authentication` and no other relationship. It MUST NOT
appear in the identifier document of a service DID — a VTA, a VTC, a VTN, or any
service or integration — and a VTA MUST refuse to enrol one there.

**VTI-KEY-155** — A verifier MUST accept a key bound to `authenticator` only to
verify a WebAuthn assertion of the person — a sign-in, or step-up evidence — and
MUST NOT accept under it a Data Integrity proof, or any material that VTI-KEY-080
or VTI-KEY-081 assigns to another role. A verifier of operational material MUST
NOT accept an `authenticator` key, though both appear in `authentication`.

*Rationale.* A passkey belongs to a person, and no other role fits it: it is not
the node's voice, because the node does not hold it, and it signs no proof a
relying party keeps. In a node's document it would be a key the node does not
hold that can speak as the node, and it would name a person in a document that
is public by design (VTI-KEY-151). The `authenticator` and `operational` roles
share `authentication`, which VTI-KEY-073 permits only with a reason the two
cannot be confused: `keyRoles` states which is which, and the two are verified
in different formats, so a verifier that selects by role (VTI-KEY-094) never
takes one for the other.
