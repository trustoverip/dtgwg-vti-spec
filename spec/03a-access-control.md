## Access Control and Authority

This section is normative.

### In plain terms

Authority in a VTI deployment is written down, one grant at a time, in an
**access control entry**: who the grant is for, what they may do, and in which
trust contexts. Nothing else confers authority — not where a request comes
from, not what key it is signed with, not the fact that the caller managed to
reach the node at all.

A **role** is a ceiling, not a grant: it says the most an entry could be
allowed, and the entry can hold less. And every part of a grant is stated
rather than implied. The most dangerous encoding of authority is the one in
which leaving something out means "everything", because leaving things out is
what serializers do by default.

Taking authority away matters as much as giving it. A revocation that removes
the grants it happened to find, and reports success, has not revoked anything
it missed — so this chapter is as exact about finding every grant beneath a
context as it is about making one.

### What this chapter defines

This chapter defines the model by which authority over trust contexts is
granted, narrowed, exercised, delegated and withdrawn: the access control
entry, roles and capabilities, act and approve scope, listing entries by
context, delegation and revocation, and which node owns which authority
decision. It is expressed over the contexts and the ancestry predicate defined
in the Trust Contexts chapter. When a single grant is not sufficient to
authorize an operation is the subject of the Approvals, Consent and Step-Up
chapter.

### The access control entry

Authority is held in **access control entries**. An entry names a subject and
states what that subject may do.

**VTI-ACL-001** — A node MUST derive every authorization decision from an
access control entry. Configuration, deployment state, network position and
possession of a transport credential MUST NOT confer authority.

**VTI-ACL-002** — An entry MUST carry at least: the subject identifier; the
role; the set of contexts the entry is scoped to; and provenance sufficient to
establish who created it and when. An entry MAY additionally carry: a narrowed
capability set; a narrowed set of key identifiers; an approve scope; a step-up
requirement; an expiry; a human-readable label; and ecosystem-defined extension
members.

**VTI-ACL-003** — Every field of an entry MUST default to the interpretation
that grants least authority, except where this chapter states otherwise.

**VTI-ACL-004** — An expired entry MUST confer no authority. Expiry MUST be
evaluated at every authorization decision, and MUST NOT be evaluated only when
a session is established.

**VTI-ACL-005** — A node MUST NOT interpret an ecosystem-defined extension
member as conferring authority.

#### Every grant is stated, never inferred from a shape

**VTI-ACL-006** — Where an entry narrows the keys its subject may reach, the
narrowing MUST be stated explicitly as one of `all`, `none`, or a non-empty
list of key identifiers.

**VTI-ACL-007** — A node MUST NOT infer a key grant from the absence of a
member or from an empty list. An entry carrying neither an explicit key
narrowing nor an explicit statement that it is unnarrowed MUST be refused.

**VTI-ACL-008** — More generally, a node MUST NOT derive any element of
authority from the absence of a member, the emptiness of a collection, or a
default applied by a serializer.

*Rationale.* Omitting an empty collection is a common and usually harmless
serialization convention. Applied to a grant it is not harmless: it converts
the narrowest authority expressible into the widest, silently, on the wire,
with both ends conforming to their own understanding of the shape and neither
in a position to notice.

The general form is VTI-ACL-008, and it is the rule the two specific ones are
instances of. A grant is a statement about authority; a shape is a property of
an encoder. Where the second is allowed to imply the first, the authority a
deployment holds depends on a library's configuration.

#### Storage of subject identifiers

**VTI-ACL-009** — A node SHOULD store the subject identifier of an entry under
a one-way function rather than in the clear, and MUST be able to answer an
authorization question without disclosing the set of subjects it holds entries
for.

### Roles

A **role** names a coarse position — administrator, initiator, application,
reader, monitor — and determines the maximum set of capabilities an entry can
carry.

**VTI-ACL-010** — A role MUST be treated as a ceiling and MUST NOT be treated
as a grant. What an entry may do is the role's set narrowed by the entry's own,
never widened by it.

**VTI-ACL-011** — A node that encounters a role it does not recognise MUST
treat the entry as conferring no authority, and MUST NOT fall back to a default
role.

The role set and the capability ceiling of each role are recorded in the annex
in Appendix C.

### Act scope: super-administrators and context administrators

The **act scope** of an entry answers: in which contexts may this subject make
a change? It takes exactly three values: `none`, `all`, or a named set of
contexts.

**VTI-ACL-020** — An entry MUST state its act scope explicitly, as one of
`none`, `all`, or a non-empty list of context paths.

**VTI-ACL-021** — An implementation MUST NOT infer act scope from the presence,
absence or emptiness of a list. In particular, an empty list MUST NOT be read
as `all`, and MUST NOT be read as `none`: it is not a valid act scope and MUST
be refused.

**VTI-ACL-022** — An entry whose act scope is `all` is a
**super-administrator**, and MUST be able to act in every context of the node.
An entry whose act scope names contexts, and whose role is administrative, is a
**context administrator** of those contexts: it MUST be able to administer them
and every descendant of them, and MUST NOT be able to act anywhere else.

**VTI-ACL-023** — Act scope and role are independent members. A node MUST NOT
compute either from the other, and MUST refuse an entry that omits either.

*Rationale.* Authority is the thing this model exists to state, so it is stated
rather than encoded. An encoding in which the same empty list means "everywhere"
for one role and "nowhere" for another cannot be read correctly without knowing
both members, which means every call site is one omission away from inverting a
grant — and the two readings are not adjacent errors, they are opposites.

Requiring the explicit form removes the class rather than warning about it: a
reader that sees `"act": "all"` cannot mistake it for `"act": "none"`, and a
reader that sees an empty list has encountered a malformed entry rather than a
grant it must interpret. Appendix F records the encoding a current
implementation uses instead, and the migration that follows from this
requirement.

### Capabilities

A **capability** names a single power that can be gated separately from the
role that ordinarily implies it.

**VTI-ACL-030** — The effective capability set of an entry MUST be the
intersection of the role's ceiling with the entry's own set, together with any
additive capability explicitly granted to the entry.

**VTI-ACL-031** — A grant naming a capability outside the role's ceiling MUST
be refused when it is written. An implementation MUST NOT accept such a grant
and silently omit the capability.

**VTI-ACL-032** — A node MUST NOT treat a capability it does not recognise as
granted.

**VTI-ACL-033** — An **additive capability** is one that no role implies.
Granting an additive capability MUST require unrestricted act authority, and an
entry MUST NOT acquire one by virtue of its role.

**VTI-ACL-034** — Where an entry carries a narrowed set of key identifiers,
that set MUST intersect the authority conferred by the entry's context scope,
and MUST NOT widen it.

The capability set is an extensible registry. The capabilities registered at
the time of publication are listed in Appendix C.

*Rationale for VTI-ACL-031.* Silently dropping an unauthorized capability
produces an entry that does not say what its author believed it said. Refusing
it by name sends the author back to the role, which is the decision that
actually needs revisiting.

*Rationale for VTI-ACL-032.* A registry that can grow means a node will
eventually read an entry naming a capability defined after that node was built.
The capability a node has never heard of is precisely the one it has no basis
for granting, and requiring the refusal to be explicit means the decision is
written down rather than arrived at by whichever way the parser happens to
fail.

### Approve scope

The **approve scope** of an entry answers a question independent of the act
scope: in which contexts may this subject bless a change made by someone else?

**VTI-ACL-040** — Approve scope MUST be represented independently of act scope,
MUST take the same three values, and MUST default to none.

**VTI-ACL-041** — An entry with act scope none and a non-empty approve scope —
a **least-privilege approver** — MUST be expressible, and MUST be able to
satisfy an approval requirement in the contexts its approve scope covers while
being unable to initiate any change.

**VTI-ACL-042** — A subject MUST NOT confer approve authority wider than the
approve authority it holds itself.

*Rationale.* Separating the two axes is what makes an approver account safe to
hand out. An approver whose authority to approve implies authority to act is
not a control; it is a second way to do the thing the control exists to gate.

### Visibility and management of entries

**VTI-ACL-050** — A caller MUST be able to modify an entry only where the
caller's act scope covers every context in the entry's act scope and in its
approve scope. Overlap is sufficient for reading (VTI-ACL-051) and is not
sufficient for modification or removal.

**VTI-ACL-051** — A caller MUST be able to read or list an entry where
VTI-ACL-050 permits modification, and additionally where the entry's approve
scope reaches the caller.

**VTI-ACL-052** — A subject MUST NOT modify its own entry, except by the
self-service rotation defined in the Client Onboarding chapter, which moves an
entry to a new subject identifier while preserving its authority exactly.

**VTI-ACL-053** — A caller MUST NOT create or modify an entry, for any
subject, that would hold authority the caller's own entry does not hold. In
particular, the resulting entry's role MUST NOT exceed the caller's, its
effective capability set MUST be contained in the caller's (additive
capabilities are governed by VTI-ACL-033 instead), where the caller's entry
narrows the keys it may use the resulting entry MUST be narrowed to a subset of
them, where the caller's entry carries an expiry the resulting entry MUST
carry one no later, and its approve scope MUST satisfy VTI-ACL-042. A node MUST
evaluate this against the caller's stored entry, not against a credential that
summarises it, and MUST refuse the write of a caller that has no live entry.

**VTI-ACL-054** — A caller creating an entry for another subject MAY mark it
as a **hand-off**: authority for its subject to exercise, exactly once, a
rollover that writes one successor entry for a different subject and removes
the marked entry. The node MUST record with the marker the granter's identifier
and the granter's authority as it stood when the marker was set: role, act
scope, effective capability set, key narrowing, approve scope and expiry. A
node MUST accept the marker only on a create, only on an entry that carries an
expiry, and only from a caller whose own entry carries no marker. A node MUST
refuse a request to set, extend or re-set the marker by any other operation,
including an update and a rotation. A rotation (VTI-CLT-025) MUST NOT carry
the marker to the new subject.

**VTI-ACL-055** — A node MUST refuse a rollover unless the caller is the
subject of the marked entry, the entry is unexpired, and its marker has not
been exercised. The successor MUST NOT exceed the marked entry, nor the
granter's authority recorded with the marker, on role, act scope, effective
capability set, key narrowing or approve scope. The successor's expiry MUST be
no later than the granter's recorded expiry, and the successor MUST NOT be
permanent where the granter's entry was not. The marked entry's own expiry
does not bound the successor; that is the one exception the marker makes to
VTI-ACL-053.

**VTI-ACL-056** — A rollover MUST consume the marker, write the successor and
remove the marked entry as one atomic operation. Of concurrent rollovers of the
same entry, at most one MUST succeed. A failure before the commit MUST leave
the marked entry, with its marker, authoritative.

**VTI-ACL-057** — A node MUST durably record a rollover in its audit log before
committing it, identifying the granter, the marked subject and the successor.
A node that cannot record it MUST NOT commit it.

**VTI-ACL-058** — A node MUST NOT infer a hand-off from any property of an
entry other than the marker. In particular, an entry that carries an expiry
without a marker is bounded by VTI-ACL-053 in full, and a node MUST refuse a
write by its subject of an entry that would outlive it.

*Rationale for VTI-ACL-050.* An entry acting in two contexts carries one
capability set, one key narrowing and one expiry, and they apply in both. An
administrator of one of the contexts who may edit the entry therefore changes
what its subject may do in the other, and one who may delete it revokes the
subject from a context it does not administer. Overlap answers whether the
caller has a legitimate interest in the entry; it does not make the caller the
authority for all of it.

*Rationale for VTI-ACL-052 and VTI-ACL-053.* Each closes the other's bypass.
Forbidding a subject to edit its own entry is empty if it may instead write a
wider entry for a second identifier it controls, scoped to the same context;
bounding what a caller may grant is empty if the caller may simply raise its
own bound first. Every axis on which an entry can be narrowed is an axis on
which it can be widened again, so the bound is stated for all of them rather
than for the role and the context list alone — clearing a capability
narrowing, dropping a key filter or lifting an expiry is a grant, and is
bounded like one. The rotation carved out of VTI-ACL-052 cannot be used for
the same purpose, because VTI-CLT-029 requires it to preserve every one of
those axes exactly.

*Rationale for VTI-ACL-054 – VTI-ACL-058.* Onboarding hands a bootstrap
identifier a short-lived grant, and the identifier then establishes the
long-term one that replaces it (Client Onboarding, Step 2). Where the long-term
entry is written by the bootstrap identifier itself, VTI-ACL-053 refuses it,
because it outlives its writer. Nothing about a time-boxed entry distinguishes
a bootstrap from any other short-lived administrator, so exempting "the
bootstrap" by inference would exempt every time-boxed administrator from the
expiry bound, which is the widening VTI-ACL-053 exists to close. The marker
makes the exemption the granter's explicit, recorded decision instead. It is
one-shot, it lapses with the entry, and it is bounded by what the granter held
when it made the decision, so the successor can be no more than the granter
could have granted directly. The subject cannot create the marker, so it
cannot manufacture an exemption for itself. Atomicity and the audit record
before the commit ensure that the marker cannot be spent twice, and that a
spent marker is always accounted for.

*Rationale for VTI-ACL-051.* Reading an entry and managing it are different
powers. An approver needs to see the grants it is being asked to bless
decisions about; it does not thereby need to be able to change them.

### Listing entries by context

A filter over a context expresses one of two opposite questions, and a caller
that cannot say which one it means will sometimes be given the other.

**VTI-ACL-060** — A listing filtered by context MUST support an explicit
direction with the following values:

| Direction | Question | Predicate |
|---|---|---|
| `acting-in` | Who may act in this context? | the entry's scope is an ancestor-or-self of the queried context |
| `subtree` | What is granted beneath this context? | the queried context is an ancestor-or-self of the entry's scope |
| `any` | Whose authority touches this subtree? | either predicate holds |

**VTI-ACL-061** — A node MUST refuse a listing that filters by context without
stating a direction. A node MUST NOT apply a default direction.

**VTI-ACL-062** — A node MUST refuse a direction value it does not recognise,
and the refusal MUST name the valid values. A node MUST NOT substitute a
default for an unrecognised direction.

**VTI-ACL-063** — A node MUST refuse a direction given without a context.

**VTI-ACL-064** — An entry with unrestricted act scope MUST NOT appear in a
`subtree` answer, and MUST appear in an `acting-in` or `any` answer.

**VTI-ACL-065** — An entry naming contexts both inside and outside the queried
subtree MUST appear in the `subtree` answer.

**VTI-ACL-066** — Where a listing is paginated, the pagination cursor MUST bind
the direction it was minted under, and a node MUST refuse to resume a cursor
under a different direction.

*Rationale.* A caller sweeping a subtree in order to revoke it needs the second
question. Asked with the first, it receives precisely the entries that are not
being revoked — the ancestors keeping their authority — and none of the
leaf-scoped grants the sweep exists to cut. The answer is short rather than
empty, so it looks complete. Once nested contexts exist the natural
least-privilege layout is a leaf context per purpose, which is exactly the
layout that makes the omission total.

There is no default direction because there is no safe one. Either default
answers a question the caller did not ask, in a form indistinguishable from the
question they did ask, and the caller most likely to be harmed is the one
performing a revocation. Requiring the direction costs a parameter; supplying
one costs a containment failure. Appendix F records that a current
implementation defaults to `acting-in`.

The two edges in VTI-ACL-064 and VTI-ACL-065 are deliberate. An unrestricted
entry names no context, so it is not a grant *of* the branch; including it
would hand a caller revoking a compromised branch its own super-administrator
to delete. An entry straddling the boundary does hold a grant inside the
branch, so omitting it would under-report exactly the case the direction exists
to surface.

### Delegation

A **delegate** is a subject acting for another — a client, a device, a software
agent. Delegation in a VTI deployment is expressed as an access control entry
and by no other means.

**VTI-ACL-070** — Delegated authority MUST be expressed as an access control
entry. A node MUST NOT infer authority from the fact that a subject is able to
execute a protocol, hold a key, or reach an endpoint.

**VTI-ACL-071** — A delegate MUST NOT hold authority the delegator does not
hold at the moment the delegation is created, and MUST NOT retain authority the
delegator has since lost.

**VTI-ACL-072** — A node MUST be able to determine, for any action taken by a
delegate, the entry that authorized it and the subject that created that entry.

**VTI-ACL-073** — Re-delegation MUST be refused unless the delegating entry
explicitly permits it, and a re-delegated entry MUST NOT be wider in any axis
than the entry it derives from.

*Rationale.* Protocol capability is not authority. An agent that can construct
a well-formed request, reach the node, and authenticate has demonstrated
exactly that and nothing about whether the action was granted to it. This
requirement is the local form of the composition requirement on delegation
lineage.

### Revocation

**VTI-ACL-080** — Removing or narrowing an entry MUST take effect at the next
authorization decision made under it. A node MUST NOT defer the effect to the
expiry of an existing session, token or cached decision.

**VTI-ACL-081** — A sweep intended to revoke authority over a context MUST
enumerate entries using the `subtree` direction, and MUST therefore include
every entry scoped to a descendant of that context.

**VTI-ACL-082** — A node MUST NOT report a revocation as successful while any
entry granting authority within the revoked scope remains effective. Where a
sweep cannot remove such an entry, the node MUST report the revocation as
incomplete and MUST identify the entries that remain.

*Rationale for VTI-ACL-082.* A containment control that reports success without
having contained anything is worse than no control, because it ends the
response that would otherwise have found what it missed. The failure mode is
specific: a grant scoped one level beneath the swept context is not returned by
a query that reads up the tree, so a sweep built on that query removes the
ancestors and leaves the leaves — and reports the removals it performed. This
requirement makes the report answer the question the operator asked, which is
whether authority within the scope still exists, rather than the question the
implementation happened to answer, which is whether the entries it found were
deleted.

### Where authority decisions live

Two node types could each plausibly own several of the decisions in the
authority model. This section states which one does, so that an implementer is never
left to infer it.

**VTI-ACL-090** — The VTA MUST be the authority for the context tree and for
key derivation within it. A VTC MUST NOT create contexts and MUST NOT derive
keys.

**VTI-ACL-091** — Where a VTC references context identifiers, it MUST evaluate
them using the ancestry predicate defined in the Trust Contexts chapter.

**VTI-ACL-092** — A node MUST NOT infer ownership of an authority decision that
this specification does not assign. Where this specification is silent, an
implementation MUST refuse rather than assume.

*Rationale.* A decision with two plausible owners and no assigned one is a
decision that gets made twice, differently, and the disagreement surfaces as an
authorization gap rather than as an error. The purpose of VTI-ACL-092 is that
the gap is closed by refusal rather than by whichever implementation happens to
be more permissive.
