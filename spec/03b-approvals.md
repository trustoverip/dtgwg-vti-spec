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
entry to unrestricted act scope, SHOULD require consent from a party other than
the requester. A node that does not require it MUST do so only as VTI-APV-022
describes.

**VTI-APV-015** — A re-authentication bound to one operation MUST be bound to
the digest of that operation's exact type and payload, computed as VTI-APV-004
computes a consent digest. It MUST be redeemable only by the caller who
performed it, only for that digest, and only once; it MUST expire; and it MUST
NOT raise the assurance level of any session. The gesture MUST be the caller's
own additional factor, verified against the challenge the node issued for that
operation — a proof the caller could produce without the factor, such as a
signature by a key the caller already holds, does not satisfy it.
A signature satisfies it only where the key is bound to the caller as a
re-authentication factor under VTI-APV-016, is distinct from every key that can
sign the caller's operations, and is held where its use requires user
verification.

**VTI-APV-016** — A node MUST bind a re-authentication factor to a subject only
on evidence independent of the subject's signing keys: a single-use token
delivered out of band, a factor the subject already holds, the authority of
another administrator exercised under that administrator's own
re-authentication, or administrative access to the host. The binding MUST
prove possession of the factor, and MUST be audited, naming the evidence it
rested on.

**VTI-APV-017** — Where a node holds an operation pending a consent requirement
and executes it when the consent completes, it MUST re-evaluate, at execution
and against the state at execution, every authorization check that applies to
the operation; MUST execute it at most once; and MUST NOT execute it after the
pending request has expired. A re-authentication the caller performed when
submitting the operation MAY stand for that execution only under these
conditions.

**VTI-APV-018** — Granting a capability that permits its holder to create or
widen authority — an **authority-conferring capability** — or widening an
entry to hold one, SHOULD require consent from a party other than the
requester. VTI-APV-014 is the instance of this recommendation for unrestricted
act scope, and VTI-APV-022 governs a node that does not apply it.

**VTI-APV-019** — Removing or narrowing another subject's unrestricted act
scope or authority-conferring capability SHOULD require consent from a party
other than both the requester and the subject, wherever such a party exists.
Wherever no such consent is obtained — none exists, or the node does not
require it under VTI-APV-022 — the node MUST require the requester's
re-authentication, MUST notify the subject, and MUST audit the removal at its
highest severity.

**VTI-APV-020** — A change that lowers the number of approvals a consent
requirement needs, or removes the requirement, SHOULD itself require consent
meeting the requirement as it stood before the change.

**VTI-APV-021** — A node SHOULD bound the number of pending consent requests a
single requester may hold, and SHOULD alert the other eligible approvers to a
burst of requests from one requester.

**VTI-APV-022** — Second-party consent (VTI-APV-014, VTI-APV-018 – 020) is
recommended practice, not every deployment's requirement: a community run by one
person has no second party to ask. A node MAY operate in **single-administrator
mode**, in which an operation those recommendations cover is authorized by the
requester's re-authentication bound to that operation under VTI-APV-015 in
place of another party's consent, wherever no eligible party other than the
requester exists. A node that does not apply those recommendations MUST do so
only through this mode, and the mode:

1. MUST be enabled only by a party with administrative access to the host, and
   MUST NOT be settable, or clearable, through the node's operation surface;
2. MUST NOT waive any consent requirement for which an eligible party other
   than the requester exists;
3. MUST be reported to every administrator, in every session, for as long as
   it is in effect; and
4. MUST be audited at the node's highest severity when it takes effect, each
   time the node starts with it in effect, and for every operation whose
   consent it waived.

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

*Rationale for VTI-APV-016.* A second factor bound on the strength of the
first is the first factor twice. The binding is where a re-authentication
factor gets its independence, so it is the binding that has to rest on
something the signing key cannot produce; every later use of the factor
inherits exactly the assurance its binding had.

*Rationale for VTI-APV-017.* Holding an operation until its approvers are
available is what lets a team that is not online together use consent at all.
The cost is that the community may move between the request and its execution
— an approver removed, the subject's entry changed — and an operation executed
on the state its approvers saw, rather than the state it acts on, is an
operation nobody approved. Re-evaluating at execution makes a long wait fail
closed instead.

*Rationale for VTI-APV-019 and VTI-APV-020.* Second-party consent for granting
authority can be defeated without granting anything: remove the other
administrators one at a time, or lower the threshold to one, and every later
grant needs nobody. Both are the same attack on the same control, and both are
closed the same way. Where no third party exists — two administrators, one
removing the other — refusing would make a compromised administrator
irremovable, so the requirement settles for making the removal loud.

*Rationale for VTI-APV-022.* Second-party consent presupposes a second party.
A community run by one person — a household, a test deployment — has none, and
without this mode its administrator can neither add a colleague nor change a
rule except through the offline break-glass, which is less visible, not more.
The mode keeps what consent was for wherever it still can: it waives consent
only where there is nobody else to ask, it still requires the requester's own
second factor bound to the one operation, it cannot be switched on by anyone
the consent rules are meant to restrain, and it is never quiet. The moment a
second eligible administrator exists, ordinary consent applies again without
any change to the mode.
