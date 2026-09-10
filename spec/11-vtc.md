## The Verifiable Trust Community

This section is normative.

This chapter specifies the community node: what it is, how it is stood up, and
which authority decisions belong to it rather than to the VTA beneath it.

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

### The community authority model

**VTI-VTC-020** — A VTC MUST express its own roles, capabilities, approve scope
and approval rules using the model defined in the Trust Contexts chapter, and
MUST NOT define a parallel model.

**VTI-VTC-021** — An administrative action on the community MUST be subject to
the approval requirements of that chapter.

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
