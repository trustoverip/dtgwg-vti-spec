## Operation Surface

This section is normative.

This chapter specifies how a VTI node exposes operations: the relationship
between the canonical Trust Task catalogue and any interface a node defines for
itself, what an operation document carries, how versions are selected, and what
a caller may conclude when a reply does not arrive.

### Precedence

**VTI-OPS-001** — Where a canonical Trust Task exists for an operation, a
conforming node MUST expose that operation as that task.

**VTI-OPS-002** — A node MAY define a private interface for an operation only
where no canonical task covers it.

**VTI-OPS-003** — Where a node exposes both a private interface and the
canonical task for the same operation, the canonical task MUST be
authoritative, and the node MUST publish a retirement path for the private
interface.

**VTI-OPS-004** — A private interface MUST be identified within a namespace
under the control of the party defining it, such that it cannot collide with a
canonical task defined later.

**VTI-OPS-005** — A node MUST make the operations it exposes discoverable, so
that a peer can determine what is available without attempting each one.

**VTI-OPS-006** — Where a privately defined operation is of general
applicability, its definer SHOULD propose it to the canonical catalogue.

*Rationale.* The canonical catalogue is what makes two implementations
interoperable without a bilateral agreement, and every private interface is a
bilateral agreement wearing the same clothes. The rule is not that private
interfaces are forbidden — an implementation always runs ahead of a
specification somewhere — but that they are temporary by construction and
identifiable as such. A private interface is a waiting room, not a destination.

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

*Rationale for VTI-OPS-031.* A conformance check that validates only what an
implementation sends measures its own fixtures rather than the service under
test: the requests were built by the same codebase the check is meant to
assess, so agreement between them establishes nothing. A response is the half a
peer depends on, and it is the half no producer-side test exercises.

*Rationale for VTI-OPS-033.* "Not found" is the recurring example: a framework
that defines no code for it leaves an implementation reporting it as a generic
failure, which a caller cannot distinguish from a genuine error, so callers
either treat a normal condition as an outage or paper over real ones.

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

### Minimum operation set

{{The operations a conforming VTA and a conforming VTC each expose, by profile.
To be completed once the canonical catalogue references are settled — see the
References chapter.}}
