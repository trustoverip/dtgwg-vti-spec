## Operation Surface

This section is normative.

### In plain terms

A node has one front door.

Every operation it offers is a defined task with a name, a shape and a stated
authority, and the different transports — a web request, a message, whatever
comes next — are doors into the same building rather than separate premises
with separate rules. When a node grows a second, private way in, everything written
about the first has to be written again about the second, by someone who may
not know it exists.

That is also why the pieces tasks are built from are defined once: a page
cursor, a refusal, a timestamp. Left to themselves they start identical and
drift apart, and a rule stated about "the cursor" quietly becomes true of four
cursors out of six.

### What this chapter defines

Everything a VTI node exposes, it exposes as a [[ref: trust task]]. This
chapter defines that surface: the catalogue as the API, the components every
task is built from, how transports bind to it, how versions are selected, and
what a caller may conclude when a reply does not arrive.

### The catalogue is the API

**VTI-OPS-001** — Every operation this specification requires of a node MUST be
exposed as a Trust Task drawn from the canonical catalogue.

**VTI-OPS-002** — A node MUST NOT expose a second, parallel interface for an
operation the catalogue already defines. There is no REST API, DIDComm protocol
or TSP protocol *beside* the task catalogue; each is a binding of it.

**VTI-OPS-003** — A node MAY define a private task for an operation only where
no canonical task covers it.

**VTI-OPS-004** — Where a node carries both a private task and a canonical task
for the same operation, the canonical task MUST be authoritative and the node
MUST publish a retirement path for the private one.

**VTI-OPS-005** — A node MUST make the tasks and versions it serves
discoverable, and the discovery response MUST itself be a task.

**VTI-OPS-006** — Where a privately defined operation is of general
applicability, its definer SHOULD propose it to the canonical catalogue.

*Rationale.* One API surface is worth more than the sum of three good ones.
Where a node grows an endpoint beside a task, the two acquire separate
authorization paths, separate error vocabularies, separate versioning and
separate tests — and the security argument then has to be made twice, by two
people who do not necessarily agree. Every requirement in this specification
about authorization, approval, idempotency and refusal is written once, against
the task; making the task the only surface is what causes those requirements to
hold on every path into the node rather than on the paths somebody remembered.

The consequence for implementers is deliberate. Adding an operation means
adding it to the catalogue, and adding it to the catalogue means writing down
its payload, its refusals, its retry class and the authority it needs. An
operation that is not worth that is not worth exposing.

### Requirements on a private task

**VTI-OPS-007** — A private task MUST be identified within a namespace its
definer controls, so that it cannot collide with a canonical task defined
later.

**VTI-OPS-008** — A private task MUST use the shared components defined below,
and MUST follow the envelope, refusal, versioning and retry requirements of
this chapter.

**VTI-OPS-009** — A private task MUST be discoverable under VTI-OPS-005, so
that a peer can determine what a node offers without attempting each operation.

*Rationale.* A private task is a staging post, not a destination. The
requirements above are what make it one: it is visible, it behaves
like everything else, and its name cannot become an obstacle to the canonical
definition that replaces it.

### Extension rather than divergence

**VTI-OPS-010** — A node MUST NOT add a member to a canonical task payload. A
canonical payload is closed, and a member outside the definition MUST be
refused.

**VTI-OPS-011** — Where a canonical task defines an extension slot, a node MAY
carry ecosystem-defined content in that slot, and a receiving node MUST NOT
refuse a document solely because the slot is populated.

**VTI-OPS-012** — A node MUST NOT derive authority from the content of an
extension slot. See VTI-ACL-005.

*Rationale.* Keeping the payload closed while admitting a declared extension
slot preserves the property that a misspelled member is refused rather than
ignored, which is the guard the closure exists for. Adding a top-level member
locally has the opposite effect on both counts: it is invisible to the peer that
does not know it, and it makes the misspelling of it indistinguishable from a
deliberate extension. Where a canonical task is nearly right, the correct
response is a proposal to the catalogue, not a local member — and the
difference is not stylistic: a member added locally means the two ends no longer
agree about what a conforming document is.

### The operation document

**VTI-OPS-020** — An operation document MUST identify the operation, the issuer,
the intended recipient, and the time of issue, and MUST carry a proof by the
issuer.

**VTI-OPS-021** — A node MUST apply the same document requirements on every
transport. A transport that authenticates its sender MUST NOT be treated as
relieving a producer of addressing or signing the document it sends.

**VTI-OPS-022** — A receiving node MUST verify that the proof covers every
member it acts on, and MUST refuse a document where it does not.

**VTI-OPS-023** — A receiving node MUST verify that it is the intended
recipient, and MUST refuse a document addressed elsewhere.

**VTI-OPS-024** — A node MUST refuse a document whose time of issue lies outside
its acceptance window.

*Rationale for VTI-OPS-021.* Transport-level sender authentication and
document-level proof answer different questions. The first says who opened this
connection; the second says who authored this document, what it covers, and to
whom it was addressed. Only the second survives the message being stored,
forwarded, replayed on another transport, or produced in evidence afterwards.

Making the requirement uniform across transports is what keeps the security
argument uniform. Where a document is signed on one transport and merely
authenticated on another, the deployment's real guarantee is the weaker of the
two, and which transport carries it is not recorded anywhere. See Appendix F.

### Replay

**VTI-OPS-025** — Every operation document MUST carry an identifier unique to
that document.

**VTI-OPS-026** — A node MUST refuse a document whose identifier it has already
accepted within the acceptance window of VTI-OPS-024.

**VTI-OPS-027** — The record of accepted document identifiers MUST be shared
across every binding a node exposes. A document accepted on one binding MUST NOT
be acceptable on another.

*Rationale.* Signing a document makes it durable evidence of intent, and
durable evidence is replayable unless something remembers it. The acceptance
window bounds how long the record must be kept; the identifier is what makes
the record possible.

VTI-OPS-027 closes the case a per-binding implementation misses. A document
captured from one binding — a log, a proxy, a stored message — is a
well-formed, correctly signed, correctly addressed document, and the second
binding has no reason to doubt it. Where the two bindings do not share a
record, the node's replay protection is exactly as good as its least-used
transport.

### Responses and refusals

**VTI-OPS-030** — A response MUST conform to the response definition of the task
it answers.

**VTI-OPS-031** — A conformance assessment MUST validate responses as well as
requests.

**VTI-OPS-032** — A refusal MUST carry a machine-readable reason drawn from the
defined set for the operation.

**VTI-OPS-033** — Where a condition has no defined code, the refusal MUST carry
a machine-readable discriminator in its details rather than being reported only
as a generic failure.

**VTI-OPS-034** — A refusal MUST be distinguishable by a caller from a transport
failure and from a delivery failure.

**VTI-OPS-035** — A refusal MUST NOT disclose information the caller is not
authorized to learn. In particular, a refusal MUST NOT reveal the existence of
a subject, a context, a credential or an entry to a caller with no authority
over it.

*Rationale for VTI-OPS-035.* A refusal is an answer, and a pair of refusals
that differ tells the caller something even when neither says anything. The
common form is a lookup that distinguishes *not found* from *not permitted*,
which turns an authorization boundary into a directory.

*Rationale for VTI-OPS-031.* A conformance check that validates only what an
implementation sends measures its own fixtures rather than the service under
test: the requests were built by the same codebase the check is meant to
assess, so agreement between them establishes nothing. A response is the half a
peer depends on, and it is the half no producer-side test exercises.

*Rationale for VTI-OPS-033.* "Not found" is the recurring example: a framework
that defines no code for it leaves an implementation reporting it as a generic
failure, which a caller cannot distinguish from a genuine error, so callers
either treat a normal condition as an outage or paper over real ones.

### Shared components

A task definition is assembled from components defined once and referenced by
every task that needs them:

| Component | Carries |
|---|---|
| **Envelope** | the operation identifier and version, issuer, recipient, issue time, a unique document identifier, and the issuer's proof |
| **Refusal** | a machine-readable reason from the defined set, with a discriminator in its details where the set has no code for the condition |
| **Page request** / **page response** | a limit and an opaque cursor, with the cursor's binding rules |
| **Scope filter** | a context path and a direction, as defined in the Trust Contexts chapter |
| **Idempotency key** | the identifier under which a repeat of one logical operation is recognised |
| **Entry**, **context record**, **credential reference** | the authority and object shapes the chapters define |
| **Instant** | a point in time, in one format, UTC |

**VTI-OPS-070** — A task definition MUST express these concepts by reference to
the shared component, and MUST NOT restate or redefine one.

**VTI-OPS-071** — A node MUST NOT accept a task-local variant of a shared
component. A document carrying one MUST be refused.

**VTI-OPS-072** — A concept that appears in more than one task MUST be promoted
to a shared component before the second task is published.

**VTI-OPS-073** — A change to a shared component is a change to every task that
references it, and MUST be versioned as such.

*Rationale.* The failure this prevents is silent divergence between two
definitions of one idea. Where each task carries its own notion of a cursor, a
refusal or a time, they begin identical and drift: one gains a binding rule,
another gains a timezone, a third gains a member the others treat as unknown.
Consumers then need per-task handling for concepts that are not per-task, and a
security rule stated about "the cursor" turns out to hold for four cursors out
of six.

VTI-OPS-072 is what keeps the component set honest. The second occurrence is
the moment a concept is shown to be general, and the last moment at which
promoting it is cheap.

### The required catalogue

**VTI-OPS-080** — A conforming node MUST expose the operations listed for its
profile below. The canonical identifier and payload of each is defined by the
catalogue specification; this table states which operations a node is required
to have.

`Core` — every node:

| Family | Operations |
|---|---|
| `discovery` | tasks and versions served; profiles and capabilities claimed |
| `auth` | challenge; authenticate; refresh; terminate |
| `acl` | create; get; update; delete; list *(scope filter, page)*; swap-key |
| `contexts` | create; get; list *(scope filter, page)*; delete |
| `keys` | create; get; list *(page)*; sign; rotate |
| `credentials` | receive; get; list *(page)*; present; archival lifecycle |
| `approvals` | list rules; set rule; delete rule; explain; request consent; approve; deny |
| `audit` | query *(page)* |

`Delivery` — every node that sends:

| Family | Operations |
|---|---|
| `delivery` | receipt; status of an outbound message |

`Community` — a VTC:

| Family | Operations |
|---|---|
| `membership` | request admission; admit; reject; get; list *(page)*; suspend; reinstate; remove *(with disposition)*; renew; rotate |
| `registry` | publish; withdraw; look up |
| `recognition` | challenge; present |

**VTI-OPS-081** — A node MUST refuse an operation it does not serve using the
refusal component, naming the condition. It MUST NOT fail silently and MUST NOT
allow the request to fail by timeout.

**VTI-OPS-082** — A node MAY expose further catalogue tasks beyond those
required for its profile.

**VTI-OPS-083** — Every operation in the catalogue MUST state, as part of its
definition, the authority its caller needs: the capability or role, and the
context that authority must cover.

**VTI-OPS-084** — Every operation MUST state its retry class, per VTI-OPS-060.

*Rationale for VTI-OPS-083.* An operation whose authority requirement lives
only in an implementation cannot be reviewed, cannot be tested by a party
without the source, and cannot be relied on by a peer. Stating it in the
definition makes the authorization surface of a node readable from the
catalogue it claims to serve — which is also what allows VTI-OPR-050's
questions to be answered without reading code.

### Transport bindings

**VTI-OPS-090** — A transport binding MUST define how the envelope, the refusal
and the payload map onto that transport, and MUST NOT define operations of its
own.

**VTI-OPS-091** — A binding MUST NOT add, remove or rename a member of a task's
payload or response.

**VTI-OPS-092** — The same task invoked under the same authority MUST produce
the same authorization decision and the same refusal on every binding, as
required by VTI-TRN-002.

**VTI-OPS-093** — A binding MUST NOT weaken the document requirements of this
chapter on the basis of a property of the transport, as required by
VTI-OPS-021.

**VTI-OPS-094** — Where a binding cannot represent a component faithfully, the
binding MUST be documented as not supporting the tasks that use it, rather than
representing it approximately.

*Rationale.* A binding is a mapping, not a dialect. Once a binding may add a
parameter, that parameter exists only for callers of that binding: it either
carries no authority, in which case it is noise, or it carries authority peers
on other bindings cannot express, which makes a node's behaviour depend on how
its caller arrived.

### The catalogue and its registries

Task identifiers, capability names and refusal codes are extension points.
Extensibility without a registration policy produces collisions and private
forks, so each has one.

**VTI-OPS-100** — A canonical task identifier, capability name or refusal code
MUST be registered before use. Registration MUST record the name, its meaning,
the specification defining it, and its status.

**VTI-OPS-101** — Registration of a canonical name MUST require a published
specification of the thing named.

**VTI-OPS-102** — A private name MUST be namespaced under a domain its definer
controls, and MUST NOT be registered as canonical.

**VTI-OPS-103** — A registered name MUST NOT be reused with a different
meaning. A name that falls out of use MUST be marked retired and MUST retain
its meaning.

**VTI-OPS-104** — A consumer MUST NOT treat an unregistered name as equivalent
to a registered one, and MUST NOT grant authority on the strength of an
unrecognised capability name, per VTI-ACL-032.

*Rationale.* The registries are where this specification's extension points
either stay coherent or quietly stop meaning anything. Requiring a
specification for a canonical name keeps the catalogue reviewable; requiring a
namespace for a private one lets an implementation move quickly without taking
a name someone else will need; and prohibiting reuse means a consumer built
years after a producer can still rely on what a name meant.

### Versioning and negotiation

**VTI-OPS-040** — An operation identifier MUST carry a major and a minor
version.

**VTI-OPS-041** — A change that alters what a conforming peer accepts or
produces MUST increment the major version. This applies at every version number:
there is no exemption for versions below 1.0.

**VTI-OPS-042** — A change to the permitted values of a member is a change of
the kind described in VTI-OPS-041, whether or not the shape of the payload
changes.

**VTI-OPS-043** — A node MUST publish the versions of each operation it serves.

**VTI-OPS-044** — A caller MUST select a version served by both peers, and where
several are available SHOULD select the highest.

**VTI-OPS-045** — A node that receives an operation at a version it does not
serve MUST refuse it explicitly, naming the versions it does serve. It MUST NOT
allow the request to fail by timeout.

**VTI-OPS-046** — A caller MUST NOT select a version on the basis of an
unauthenticated discovery response.

**VTI-OPS-047** — A node MUST maintain a minimum acceptable version for each
operation, MUST refuse a version below it, and MUST NOT negotiate below it on
the request of a peer.

*Rationale for VTI-OPS-046 and VTI-OPS-047.* Negotiation selects the version
both parties can speak, which means an attacker who can influence what a peer
appears to speak selects it instead. Authenticating the discovery response
removes the first half; a floor that a peer cannot argue a node below removes
the second. Without the floor, retiring a version is advisory: every peer that
claims not to have upgraded is served anyway, which is precisely the claim an
attacker makes.

*Rationale for VTI-OPS-041 and VTI-OPS-042.* Negotiation is tractable only
where a version number carries a promise. Semantic versioning below 1.0 makes
no promise — correctly, by its own definition — so a catalogue following it
faithfully can ship a breaking wire change as a minor increment, and a peer
that trusted the increment has no way to discover this except by failing.

VTI-OPS-042 exists because the compatibility question is about accepted values,
not about shapes. Renaming the permitted values of a member leaves the payload
structurally identical: no schema comparison flags it, and every peer built
against the previous set fails on it. A contract that covers only structure
covers the easy half.

*Rationale for VTI-OPS-045.* Where the unsupported version is not refused
explicitly, an asynchronous transport turns it into a timeout with no
explanation — an outcome the caller cannot distinguish from an unreachable
peer, and the most expensive way to learn that two versions did not match.

### The policy gate

**VTI-OPS-050** — A node MUST evaluate the approval and step-up rules that apply
to an operation before the operation's handler runs, and MUST reach the same
decision on every transport. See VTI-APV-002.

**VTI-OPS-051** — A node MUST NOT rely on a handler to enforce a requirement
that the policy gate is specified to enforce.

*Rationale.* A requirement enforced in the handler is enforced once per handler,
which means it is enforced everywhere it was remembered. Moving it in front of
the dispatch makes the enforcement structural, so a new operation is gated
because of where it sits rather than because its author recalled the rule.

### Retry, idempotency and the cost of a lost reply

A request that times out has usually not arrived, and retrying is correct.
Sometimes it arrived, was performed, and only the reply was lost — and there a
retry produces a second durable effect that nobody is watching for.

**VTI-OPS-060** — Every operation MUST be classified by what a repeat of it
costs, using at least the following distinctions: no durable effect; a durable
effect whose repeat is harmless; and a durable effect whose repeat leaves a
second artefact that persists and matters.

**VTI-OPS-061** — An operation in the third class MUST accept an idempotency
key, and a node MUST perform such an operation at most once per key.

**VTI-OPS-062** — An operation that is not classified MUST be treated as
belonging to the third class.

**VTI-OPS-063** — Exactly one layer MUST own retry for a given failure domain.
Application code above a retrying client MUST NOT add a retry loop of its own.

**VTI-OPS-064** — Every attempt at one logical operation MUST carry the same
idempotency key.

**VTI-OPS-065** — A node MUST be able to return the result of a completed keyed
operation to a caller that repeats it with the same key, rather than refusing
the repeat.

*Rationale for VTI-OPS-062.* Classification is a judgement made when an
operation is defined, and an unclassified operation is one whose author did not
make it. Treating it as the most costly class is the direction whose error is
cheap: over-classifying costs one deduplication record, and under-classifying
loses the protection in exactly the rare case the classification exists for.

*Rationale for VTI-OPS-063 and VTI-OPS-064.* Uncoordinated retry layers
multiply — three application attempts over three transport attempts is nine
executions — and a hand-written loop at the application layer is worse than
redundant, because it cannot hold a key stable. It re-invokes a client method,
which builds a fresh document with a fresh key, which is a different operation
as far as the node is concerned. It converts one operation retried into several
operations performed, which is the precise failure the key exists to prevent.

### Composing an operation

The requirements above compose into one rule worth stating on its own: a new
operation is defined by naming it in the catalogue, assembling its payload from
shared components, stating the authority it requires and its retry class, and
publishing the versions a node serves. Nothing about it is decided at the
transport, and nothing about it is decided in an implementation.

**VTI-OPS-110** — A node MUST NOT expose behaviour that is not attributable to
a defined operation. An input that changes what a node does MUST be part of a
task's payload, and MUST be documented, versioned and authorized as such.

*Rationale.* Undocumented inputs — a header a proxy adds, a query parameter one
binding accepts, an environment variable that changes an authorization outcome
— are the surface no review covers, because they belong to no definition. This
requirement puts them inside the model or removes them, and the configuration
form of the same hazard is covered by VTI-CMP-090.
