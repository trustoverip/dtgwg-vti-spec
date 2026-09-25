## The Verifiable Trust Community

This section is normative.

### In plain terms

A community is a club, and a VTC is its membership secretary.

It decides who joins, records who is currently in good standing, and tells
other clubs what it will vouch for. What it deliberately does not do is hold
the members' keys: those belong to each member's own agent. A compromised
secretary can wrongly admit someone, wrongly expel someone, and wrongly vouch
for a stranger — all serious, and all recoverable. A secretary who held
everyone's keys would be a different kind of disaster.

That division is the reason this chapter spends its time saying which decisions
belong to the community and which to the agent underneath it. A decision with
two plausible owners is a decision that gets made twice, differently.

### What this chapter defines

This chapter specifies the community node: what it is, how it is stood up, and
which authority decisions belong to it rather than to the VTA beneath it.

It is the first of three chapters on the community. What a community does once
it is stood up is specified in the two that follow the Verifiable Trust Network
chapter: Membership Lifecycle and Community Credentials, for how a subject
becomes, remains and ceases to be a member; and Trust Registries and
Cross-Community Recognition, for what a community publishes and how
communities recognise each other. An implementer of the VTC target needs all
three.

### Provisioning and dependency

**VTI-VTC-001** — A VTC MUST be provisioned on an existing VTA and MUST derive
its identity from it.

**VTI-VTC-002** — The dependency MUST be one-way. A VTA MUST NOT require a VTC
in order to operate.

**VTI-VTC-003** — A VTC's identifier MUST be resolvable by a party with no prior
relationship to the community, using a method satisfying VTI-KEY-001.

### Which authority lives where

**VTI-VTC-010** — A VTC MUST NOT create trust contexts and MUST NOT derive keys.
See VTI-ACL-090.

**VTI-VTC-011** — Where a VTC references context identifiers, it MUST evaluate
them using the ancestry predicate defined in the Trust Contexts chapter.

**VTI-VTC-012** — A VTC owns the following decisions and no others in this
specification: admission to the community; the membership state of a member;
recognition of another community; and what the community publishes.

**VTI-VTC-013** — A VTC MUST NOT make key-custody decisions for its members. A
member's keys are the concern of that member's VTA.

*Rationale.* The division is not arbitrary: the VTA holds the material whose
compromise is unrecoverable, and the VTC holds the relationships that can be
re-established. Keeping the two apart means a compromised community can wrongly
admit, wrongly remove and wrongly recognise — all serious, all recoverable —
and cannot take a member's keys.

### Keys and what the community signs

A community's seal is kept by the agent underneath it, not by the secretary.
The VTC answers messages under a key of its own; what the community decides —
who is a member, in which role, endorsed for what, and whether any of that
still holds — is signed by its VTA at the VTC's request, under a key the VTC
never holds.

**VTI-VTC-040** — A VTC's identity MUST bind its keys to key roles as required
by VTI-KEY-070. Its `operational` and `messaging` keys MUST be derived by its
VTA and delivered under VTI-VTA-030, consistently with VTI-VTC-010.

**VTI-VTC-041** — A VTC MUST NOT hold its `attestation` or `update` keys. It MUST
obtain every attestation artefact — membership, role and endorsement credentials,
vetter grants and status lists (VTI-KEY-080) — by a signing request to its VTA,
as required by VTI-KEY-111, and MUST NOT sign an attestation artefact itself.

**VTI-VTC-042** — A service operated alongside a VTC, such as a forge bridge,
MUST have its own identity and keys and MUST sign as itself, as required by
VTI-KEY-076. It MUST be authorized by an access control entry or an attestation
artefact of the VTC, and never by a key in the VTC's identifier document.

**VTI-VTC-043** — A backup of a VTC MUST NOT contain an `attestation` or `update`
key, as required by VTI-KEY-112.

*Rationale.* The paragraph that opens this chapter says a compromised secretary
can wrongly admit, remove and recognise. VTI-VTC-041 narrows that further: a
compromised VTC process can ask its VTA for attestations, one at a time, each
parsed, constrained and recorded, and each stoppable by withdrawing the
process's authority — but it cannot mint the community's credentials or
re-publish its status lists on its own, and it cannot rewrite what the
community's identifier means.

### The community authority model

**VTI-VTC-020** — A VTC MUST express its own roles, capabilities, approve scope
and approval rules using the model defined in the Access Control and Authority
and the Approvals, Consent and Step-Up chapters, and MUST NOT define a parallel
model.

**VTI-VTC-021** — An administrative action on the community MUST be subject to
the approval requirements of the Approvals, Consent and Step-Up chapter.

### Governance binding

**VTI-VTC-030** — A governance framework MAY impose requirements stricter than
this specification, and MUST NOT reduce any requirement of it.

**VTI-VTC-031** — Where community policy is expressed as a credential or other
verifiable artefact, a VTC MUST verify it before applying it, and MUST record
which version of it governed a decision.

**VTI-VTC-032** — A change to community policy that alters trust, privacy,
authority or security semantics is subject to VTI-CMP-090.

*Rationale for VTI-VTC-031.* Policy is the input that decides every other
decision, and a deployment that cannot say which policy governed a past
admission cannot explain that admission afterwards. Recording the version is
what turns "the policy allowed it" from an assertion into a checkable claim.
