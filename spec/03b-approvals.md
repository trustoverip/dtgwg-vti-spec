## Approvals, Consent and Step-Up

This section is normative.

### In plain terms

**Acting** is separate from **approving**. The person who writes the cheque
and the person who countersigns it are doing different jobs, and an approver
who can also act is not a control.

Some operations are too consequential to rest on one credential, and there are
two different ways to ask for more. One asks the caller to prove again that
they are still there — a fresh factor, for a short window. The other asks
somebody else to agree to this exact change. They answer different questions,
and a design that uses one where the other was needed provides the appearance
of a control and none of its effect.

### What this chapter defines

This chapter defines approval rules: how a node states that an operation needs
more than the caller's own authority, the difference between re-authentication
and consent, how a consent is bound to what was approved, and how a node
refuses a rule that could never be satisfied. Who may approve is stated by the
approve scope defined in the Access Control and Authority chapter.

### Approval rules

Some operations are not safely authorized by a single credential. A node
expresses that with **approval rules**, evaluated before the operation runs.

There are two kinds of requirement, and they are not interchangeable.

| | Re-authentication | Consent |
|---|---|---|
| Who decides | the caller, re-proving with an additional factor | one or more parties other than the caller |
| Bound to | the caller's session, for a bounded window — or exactly one operation, by digest (VTI-APV-015) | the exact payload, by digest |
| Establishes | that the caller is still present | that a different party agreed to this change |
| Threshold | not applicable | N-of-M, optionally excluding the requester |

**VTI-APV-001** — An approval rule MUST name the operation it applies to, and
MAY additionally name the contexts it applies in. Where both a
context-scoped and an unscoped rule name the same operation, the
context-scoped rule MUST apply in the contexts it names.

**VTI-APV-002** — A node MUST evaluate applicable approval rules before the
operation's handler runs, and MUST reach the same decision on every transport.

**VTI-APV-003** — A re-authentication requirement MUST be satisfied only by the
caller, and MUST NOT be treated as evidence that any party other than the
caller agreed to anything. It MUST either raise the assurance level of the
caller's session for a bounded window, or be bound to exactly one operation
under VTI-APV-015.

**VTI-APV-004** — A consent requirement MUST be bound to the digest of the
exact payload approved. An approval MUST NOT be redeemable for a payload other
than the one approved, and MUST NOT be redeemable more than once.

**VTI-APV-005** — A node MUST NOT implement a consent requirement by elevating
the requester's session on the strength of another party's approval. Approval
of one action MUST NOT admit any other action.

**VTI-APV-006** — A party satisfying a consent requirement MUST hold approve
authority covering the context the operation acts in.

**VTI-APV-007** — Where a rule excludes the requester, the requester MUST NOT
count toward the threshold. A single subject MUST NOT count more than once
toward a threshold.

**VTI-APV-008** — A consent request MUST expire, and an expired approval MUST
NOT be redeemable.

**VTI-APV-009** — A node MUST refuse, at the time it is written, an approval
rule that cannot be satisfied. At minimum this includes: a rule naming an
approver set that does not exist or is empty; a threshold larger than the set
it draws on; approver configuration on a re-authentication rule; and two rules
for one operation whose context scopes overlap.

**VTI-APV-010** — A node MUST be able to report, for a given operation and
context: which rule applies, what it requires, which subjects can satisfy it,
and whether it can be satisfied at all.

**VTI-APV-011** — The description presented to an approver MUST identify the
operation, the party requesting it, the context it acts in, and the effect of
approving it.

**VTI-APV-012** — A node MUST provide a means of removing an approval rule that
has rendered itself unsatisfiable, and the use of that means MUST be audited.

**VTI-APV-013** — What is presented to an approver MUST be derived from the
same octets that are digested under VTI-APV-004. A node MUST NOT present a
rendering produced from a different source than the one it commits to.

**VTI-APV-014** — Creating an entry with unrestricted act scope, or widening an
entry to unrestricted act scope, MUST require consent from a party other than
the requester.

**VTI-APV-015** — A re-authentication bound to one operation MUST be bound to
the digest of that operation's exact type and payload, computed as VTI-APV-004
computes a consent digest. It MUST be redeemable only by the caller who
performed it, only for that digest, and only once; it MUST expire; and it MUST
NOT raise the assurance level of any session. The gesture MUST be the caller's
own additional factor, verified against the challenge the node issued for that
operation — a proof the caller could produce without the factor, such as a
signature by a key the caller already holds, does not satisfy it.

*Rationale for VTI-APV-013.* An approval is only as good as the correspondence
between what the human saw and what the system committed to. Where the display
is rendered from one structure and the digest computed over another, the
approver's decision is about a document that need not exist — and every part of
the ceremony still functions, which is what makes the gap hard to notice.

*Rationale for VTI-APV-014.* Unrestricted authority is the one grant from which
every other grant can be made, including the removal of the controls that
governed it. Requiring a second party is not a statement about the requester's
trustworthiness; it is the recognition that a single compromised credential
should not be able to end the deployment's ability to constrain anything.

*Rationale for VTI-APV-015.* A request that arrives as a signed document, rather
than over a session, has no session to raise, and binding the gesture to one
of the caller's sessions after the fact cannot say which one — while a window
opened on any of them is one a process holding the caller's signing key can
spend on acts the caller never saw. Binding the gesture to the one operation
it was made for closes that: one gesture admits one act. It is strictly
narrower than the session form, not an alternative to it, and it is still the
caller's own presence that it establishes — never another party's agreement,
which remains the business of consent.

*Rationale for VTI-APV-005.* A design in which a third party ratifies an action
and the requester's session is then elevated for a period is consent with the
binding removed: the approver believes they approved one change, and what they
actually granted was a window in which every gated change passes. The
distinction is invisible in the approval interface and decisive in effect.

*Rationale for VTI-APV-009.* An unsatisfiable rule is a lockout that has not
happened yet. Refusing it when it is written costs the author one error
message; discovering it at the moment it blocks an operator costs an incident,
and the error at that point names a condition the operator cannot act on.

*Rationale for VTI-APV-011.* Several requirements in this specification depend
on a human understanding what they are approving. An approval a person cannot
comprehend is not consent, and a control built on it provides assurance to
everyone except the person exercising it. See also the Accessibility
Considerations.
