## Membership Lifecycle and Community Credentials

This section is normative.

This chapter specifies how a subject becomes, remains and ceases to be a member
of a community, and the credentials that evidence each state.

### Lifecycle states

**VTI-MEM-001** — A VTC MUST represent at least the following member states:
applying; member; suspended; and departed. A transition between them MUST be
the result of a decision by an authority entitled to make it.

**VTI-MEM-002** — Every state transition MUST be audited, recording the subject,
the acting authority, the time and the reason.

### Admission

**VTI-MEM-010** — An admission decision MUST be made under authority scoped to
the community.

**VTI-MEM-011** — Completion of a join exchange MUST NOT be treated as
admission. Admission is the decision; the exchange is how it is requested and
communicated. See VTI-CMP-020.

**VTI-MEM-012** — A VTC MUST record the evidence relied on for an admission,
sufficient for the decision to be reviewed afterwards.

**VTI-MEM-013** — A VTC MUST NOT admit a subject on the basis of a presentation
that does not satisfy VTI-CRD-021 and VTI-CRD-022.

### Membership credentials

**VTI-MEM-020** — Membership MUST be evidenced by a credential issued by the
community to the member.

**VTI-MEM-021** — Where membership is evidenced by a pair of credentials that
commit to one another, each commitment MUST be computed over the exact
transmitted form of the other credential.

**VTI-MEM-022** — A verifier of such a pair MUST verify both commitments, and
MUST refuse the pair where either fails.

*Rationale for VTI-MEM-021.* A commitment computed over a parsed and
re-serialized form is a commitment to whatever survived the round trip. Members
that a parser drops — a status member it does not recognise, an ordering it
normalises — are outside the commitment, so an artefact can be altered in
exactly those members without disturbing the proof. The requirement is stated in
terms of the transmitted form because that is the only form both parties can
agree on without agreeing on a parser.

### Suspension and removal

**VTI-MEM-030** — Suspension MUST be reflected in the status of the member's
credentials, and MUST take effect for verifiers that check status.

**VTI-MEM-031** — A VTC MUST support a departure disposition that erases the
member's personal data, and MUST support at least one that retains the record
for audit.

**VTI-MEM-032** — A subject-initiated erasure MUST NOT be downgraded by
community policy to a disposition that retains their personal data.

**VTI-MEM-033** — A departure MUST propagate to whatever the community has
published about the member.

**VTI-MEM-034** — Where erasures are propagated in batches to reduce timing
correlation, the batching interval MUST NOT delay the effect of the departure on
the member's credential status.

*Rationale for VTI-MEM-034.* Batching publication is a legitimate privacy
measure: an erasure published the instant it is requested tells observers when a
particular person left. It becomes a defect if the same delay is applied to
status, because then the member's credentials remain accepted after they have
left. The two are separable, and this requirement says so.

### Renewal and rotation

**VTI-MEM-040** — Membership MUST expire unless renewed, and renewal MUST NOT
extend membership beyond the maximum the community's policy permits.

**VTI-MEM-041** — Rotation of a member's identifier MUST preserve their
membership state, and MUST require proof of control of the new identifier.

**VTI-MEM-042** — Rotation MUST NOT be usable to transfer membership to a
different subject.

**VTI-MEM-043** — A VTC MUST audit renewal and rotation, recording both the
previous and the new identifier in the case of rotation.

*Rationale for VTI-MEM-042.* Rotation and transfer look identical on the wire —
the same operation, a new identifier — and differ in whether the same person is
on the other end of it. Requiring proof of control from the new identifier does
not by itself distinguish them, so the prohibition is stated separately: a
community that permits transfer by rotation has a membership that can be sold.
