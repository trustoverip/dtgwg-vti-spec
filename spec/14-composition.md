## Composition Requirements

{{This section is normative.}}

DTG component specifications are modular by design, and that modularity is what
allows them to evolve independently. It also means that some properties a
relying party depends on cannot be determined by inspecting any one of them.
Those properties are owned here.

Each requirement in this chapter is a property of the **composed** interaction.
None can be established by a single component, and none is a defect in the
component specifications it spans.

### Composition integrity

{{A property established by one component MUST NOT be assumed to survive
composition with another.}}

### Semantic completion

{{Successful protocol execution MUST NOT be treated as establishing the
intended trust outcome. A task can complete at the protocol level while the
authority, policy, relationship or evidence conditions it presupposed remain
unresolved.}}

### Authority is not verification

{{Cryptographic verification establishes provenance and integrity. It does not
establish authority. Identity, authentication, credential verification,
authority, authorisation and the trust decision MUST remain distinguishable in
a composed flow, and an implementation MUST NOT collapse one into another.}}

### Currency of state

{{Historical evidence of a relationship or an authority MUST NOT be interpreted
as evidence of its current state.}}

### Delegation lineage

{{An implementation MUST NOT derive broader authority from protocol capability
than the applicable delegation granted. Scope, lineage, expiry, revocation and
re-delegation MUST be determinable for any action taken by a delegate.}}

### Privacy across composition

{{Privacy MUST be evaluated over the complete interaction rather than per proof
mechanism. An unlinkable proof carried alongside a stable identifier or
observable metadata participates in a linkable interaction, and the composed
result is what must be assessed.}}

### False independence

{{Apparent multiplicity, provenance depth, threshold satisfaction, actor
distinctness or artefact validity MUST NOT be upgraded into evidence
*independence* or evidence *completeness*. Several identifiers may represent
one effective actor; several issuers may share one governance root; several
attestations may derive from one source; a threshold may be numerically
satisfied without the independence the relying policy assumed; individually
valid evidence may be materially incomplete.

The converse boundary is equally normative. Legitimate plurality, pseudonymity,
shared infrastructure, coalitions, independent agreement and privacy-preserving
minimisation MUST NOT be treated as adversarial merely for resembling one of
these patterns.}}

### Indeterminacy

{{Where evidence is insufficient to determine whether a trust condition holds,
the disposition MUST remain indeterminate. It MUST NOT be silently converted
into corroboration, and MUST NOT be silently converted into failure. This
section specifies the required behaviour on an indeterminate disposition.}}

### Configuration materiality

{{A configuration change that alters trust, privacy, authority or security
semantics is materially equivalent to an implementation change affecting those
semantics, and MUST attract the same review, scrutiny and audit trail.}}

### Component substitution

{{Which trust, privacy, governance and lifecycle properties MUST remain
invariant when one conformant implementation is replaced by another.}}

### Human control

{{A protocol MUST NOT complete an action requiring a human principal's
authorisation without that authorisation having been meaningfully given.
Cross-references: the approval and consent requirements in the Trust Contexts
chapter, and the step-up handling in the Client Onboarding chapter.}}

### Open propositions

{{Several propositions in this chapter are, at the time of publication,
supported by composition evidence but not yet resolved to an owner. They are
published with that status visible rather than withheld: Appendix E records
each one with its ownership classification, its supporting evidence and its
counter-cases. A reader is entitled to know which requirements the working
group considers settled and which it does not.}}
