## Composition Requirements

This section is normative.

### In plain terms

Every part of a car can pass its own test and the car still be unsafe. The
brakes work, the steering works, the tyres are within spec — and the
combination understeers into a hedge. Nobody's component is faulty; the
assembly was never assessed.

That is this chapter. DTG components are specified separately so they can
evolve separately, and the price is that some things a relying party depends on
are true of the arrangement rather than of any part.

Three examples, each of which has a requirement below:

- A proof that reveals nothing about who presented it, carried under a name
  that never changes, is not anonymous. The proof is fine. The interaction is
  not.
- A message exchange that completes exactly as specified has not necessarily
  achieved what it was for. "The protocol succeeded" and "the thing you wanted
  is now true" are different claims, and only one of them is testable by the
  protocol.
- Several signatures from several parties may look like independent
  corroboration and be four copies of one opinion. Counting them does not make
  them independent; establishing that they are is separate work.

The last one is the hardest, because the fix is not "check more". It is
**knowing what you have not established** — and being willing to answer "we
cannot tell" rather than rounding it to yes or no.

### What this chapter defines

DTG component specifications are modular by design, and that modularity is what
lets each of them evolve without waiting for the others. It also means that
some properties a relying party depends on cannot be determined by inspecting
any one of them. This chapter owns those properties.

Every requirement here is a property of the **composed** interaction. None can
be established by a single component, and none of them implies a defect in the
component specifications it spans: a component can be entirely correct and the
composition still fail to hold the property, because the property was never
anyone's to hold.

### How a composition requirement binds

**VTI-CMP-001** — A requirement in this chapter binds the party making a
relying decision and the deployment within which that decision is made. A
component's conformance to its own specification MUST NOT be treated as
satisfying any requirement in this chapter.

**VTI-CMP-002** — A conformance claim against the Composition profile MUST
state, for each requirement in this chapter, the evidence relied on. Appendix E
records the ownership classification and evidence for each.

*Rationale.* The failure this chapter exists to prevent is not a component
behaving incorrectly. It is every component behaving correctly and the system
built from them holding a property none of them held.

### Composition integrity

**VTI-CMP-010** — A property established by one component MUST NOT be assumed to
survive composition with another.

**VTI-CMP-011** — A deployment MUST be able to identify, for each property a
relying decision depends on, whether that property is established by a
component or by the composition.

### Semantic completion

**VTI-CMP-020** — Successful execution of a protocol MUST NOT be treated as
establishing the trust outcome the protocol was invoked to achieve.

**VTI-CMP-021** — An implementation MUST distinguish, in what it reports to a
relying party, the completion of an exchange from the establishment of its
intended outcome.

*Rationale.* A task can complete exactly as specified — every message
well-formed, every signature valid, every state transition correct — while the
authority, policy, relationship or evidence conditions it presupposed remain
unresolved. An interface that reports only "completed" gives a relying party no
way to tell the two apart, and the relying party will reasonably assume the
stronger reading.

### Authority is not verification

**VTI-CMP-030** — Verification of a credential or a proof MUST be treated as
establishing provenance and integrity, and MUST NOT be treated as establishing
authority.

**VTI-CMP-031** — A composed flow MUST keep the following distinguishable:
identity; authentication; credential verification; authority; authorisation;
and the trust decision itself. An implementation MUST NOT collapse one into
another.

**VTI-CMP-032** — Where a relying decision depends on the authority of an
issuer or an actor, that authority MUST be established at the time of the
decision from a source entitled to answer for it, and MUST NOT be inferred from
the validity of a signature.

*Rationale.* A cryptographically impeccable credential can be issued by a party
that never held, or no longer holds, the authority it asserts. Signature
validity answers a question about the artefact; authority is a question about
the world at the moment of relying, and only something entitled to answer for
the world can answer it.

### Currency of state

**VTI-CMP-040** — Historical evidence of a relationship, an authority or a
status MUST NOT be interpreted as evidence of its current state.

**VTI-CMP-041** — Evidence of currency MUST carry the time at which currency was
determined, and a relying party MUST apply a stated freshness bound to it.

**VTI-CMP-042** — Where currency cannot be determined within the freshness
bound, the outcome MUST be indeterminate as specified below, and MUST NOT
default to the last known state.

*Rationale.* Every component in a composed flow may be reading a different
vintage of the same fact: one has just resolved it, one is serving a cached
answer, and one recorded it at issuance. Requiring the determination time to
travel with the evidence is what lets a relying party notice that.

### Delegation lineage

**VTI-CMP-050** — An implementation MUST NOT derive broader authority from
protocol capability than the applicable delegation granted.

**VTI-CMP-051** — For any action taken by a delegate, the following MUST be
determinable: the scope of the delegation, its lineage back to an authority
that held what was delegated, its expiry, its revocation status, and any
re-delegation in the chain.

**VTI-CMP-052** — A delegate's authority MUST be evaluated against the
delegator's authority as it stands at the time of the action.

**VTI-CMP-053** — The identity of the acting delegate MUST reach the point of
decision. A composed flow MUST NOT present a delegate's action as the
principal's own without the delegation being determinable.

*Rationale for VTI-CMP-053.* An intermediate component that substitutes the
principal for the delegate produces a flow in which every artefact is valid and
the question "who actually did this" has no answer at the far end. The
delegation may be entirely proper; the point is that the relying party cannot
tell, and cannot apply any policy that depends on telling.

### Privacy across composition

**VTI-CMP-060** — Privacy properties MUST be evaluated over the complete
interaction, and MUST NOT be evaluated at the level of an individual proof
mechanism alone.

**VTI-CMP-061** — An interaction MUST NOT be described as unlinkable where any
layer of it carries a stable identifier or observable metadata sufficient to
re-link it.

**VTI-CMP-062** — A deployment MUST be able to identify the durable correlators
it introduces, including those arising from naming, routing, lookup and session
management rather than from credentials.

**VTI-CMP-063** — A composed interaction MUST NOT require disclosure beyond what
the relying decision needs.

*Rationale.* An unlinkable proof carried beside a stable agent name, over a
route observable to a mediator, preceded by a registry lookup that reveals who
is asking about whom, is a linkable interaction containing an unlinkable proof.
The proof mechanism is not defective. The composition is where the property was
lost, and the composition is where it has to be assessed.

### False independence

Several distinct threat classes share one shape: something is observed
multiple times, or at depth, or from apparently distinct sources, and that
observation is upgraded into a conclusion about independence or completeness
that it does not support.

| Threat class | The unsafe inference |
|---|---|
| Sybil | multiplicity of identifiers implies multiplicity of actors |
| False diversity | nominal diversity implies governance independence |
| Trust laundering | provenance depth implies assurance depth |
| Sock puppet | persona multiplicity implies social independence |
| Quorum capture | threshold arithmetic implies independent approval |
| Collusion | distinct actors implies independent actors |
| Selective evidence | valid evidence implies complete evidence |

**VTI-CMP-070** — Apparent multiplicity, provenance depth, threshold
satisfaction, actor distinctness or artefact validity MUST NOT be upgraded into
evidence independence or evidence completeness.

**VTI-CMP-071** — Where a relying policy depends on the independence of its
inputs, that independence MUST be established by evidence, and MUST NOT be
inferred from the inputs being separately identified.

**VTI-CMP-072** — Where a relying decision depends on the completeness of the
evidence presented, an implementation MUST consider whether the presentation is
complete for that decision, and MUST NOT treat the validity of what was
presented as an answer to that question.

**VTI-CMP-073** — Where independence or completeness cannot be established, the
outcome MUST be indeterminate. Uncertainty MUST NOT be converted into
corroboration.

**VTI-CMP-074** — Legitimate plurality, pseudonymity, shared infrastructure,
transformation of evidence, coalitions, independent agreement, selective
disclosure and privacy-preserving minimisation MUST NOT be treated as
adversarial merely because they resemble one of the patterns above.

*Rationale for VTI-CMP-074.* This requirement is not a caveat on the others; it
is half of the property. A system that reads every pseudonym as a sock puppet
and every coalition as collusion has not become safer, it has become unable to
serve legitimate participants, and the pressure to relax it will come from the
cases where it is wrong rather than the cases where it is right. Both boundaries
have to be specified or only one of them will be implemented.

### Indeterminacy

**VTI-CMP-080** — An implementation MUST support an indeterminate outcome as a
first-class result of a trust evaluation, distinct from both success and
failure.

**VTI-CMP-081** — An indeterminate outcome MUST NOT be silently converted into
corroboration, and MUST NOT be silently converted into failure.

**VTI-CMP-082** — A relying policy MUST state what is to be done on an
indeterminate outcome for each class of decision it governs. Where the policy
is silent, the decision MUST be refused.

*Rationale.* Insufficient evidence is a different condition from evidence of
absence, and collapsing the two loses information in whichever direction the
implementation happened to choose. Requiring the policy to say what happens on
indeterminate is what forces the choice to be made deliberately, once, by
someone who can weigh it — rather than incidentally, per call site, by whoever
wrote the error handling.

### Configuration materiality

**VTI-CMP-090** — A configuration change that alters trust, privacy, authority
or security semantics MUST be treated as materially equivalent to an
implementation change affecting those semantics, and MUST attract the same
review and audit.

**VTI-CMP-091** — A deployment MUST be able to reconstruct the effective
configuration that governed a past decision.

**VTI-CMP-092** — Configuration that alters the semantics named in VTI-CMP-090
MUST be identifiable as such, and MUST NOT be indistinguishable from
configuration that does not.

*Rationale.* The nominal protocol is unchanged, every component remains
conformant, and the trust behaviour of the deployment is different. Where the
change is invisible to the review process that governs code, the composition's
properties can be altered by an edit that nobody assessed.

### Component substitution

**VTI-CMP-100** — Where one conformant implementation is substituted for
another, the deployment MUST re-establish the composition properties this
chapter requires. Conformance of the substituted component MUST NOT be treated
as carrying them.

**VTI-CMP-101** — A deployment MUST be able to state which trust, privacy,
governance and lifecycle properties it requires to remain invariant across
substitution.

### Human control

**VTI-CMP-110** — An action requiring the authorisation of a human principal
MUST NOT complete without that authorisation having been given.

**VTI-CMP-111** — The authorisation MUST be bound to the action it authorises.
An authorisation MUST NOT admit actions other than the one presented.

**VTI-CMP-112** — The information presented to the authorising human MUST be
sufficient for them to understand what they are authorising.

**VTI-CMP-113** — A delegate acting for a principal MUST NOT be able to satisfy
that principal's authorisation requirement.

*Rationale.* These are the composed-flow form of requirements that appear
locally in the Trust Contexts and Client chapters. They are restated here
because a flow can satisfy each node's local rule and still complete without a
human having meaningfully decided anything — the authorisation collected at one
hop being reused, widened, or attributed to a principal by a component acting
on their behalf.

### Open propositions

The propositions in this chapter differ in how settled they are. Some are
established requirements on the composition; others are supported by evidence
but not yet resolved to an owner, and could belong to a component
specification instead.

**VTI-CMP-120** — A published version of this specification MUST record, for
each requirement in this chapter, its ownership classification, the evidence
supporting it, and the counter-cases considered. Appendix E carries that record.

*Rationale.* Publishing the unresolved propositions with their status visible is
deliberate. A reader is entitled to know which requirements the working group
considers settled and which it does not, and a backlog stated in public is
worth more than a chapter withheld until it is complete.
