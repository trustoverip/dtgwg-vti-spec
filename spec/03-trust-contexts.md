## Trust Contexts and the Authority Model

This section is normative.

### In plain terms

A trust context is a room. Everything a node holds — keys, credentials, data,
the authority it has delegated — sits in one of them, and a grant is always a
grant over particular rooms rather than over the building.

The payoff is what happens when something goes wrong. A credential that leaks
opens the rooms it was granted for and no others, so the question "how bad is
this?" has an answer you can work out from the grant itself rather than by
auditing everything the holder ever touched.

Rooms nest, and authority over a room reaches everything inside it. That is why
the way a room's name is compared matters so much: `acme` contains `acme/eng`,
and must not be read as containing `acme-evil`, which merely starts with the
same letters. A single careless string comparison hands one organisation's
authority to anyone who can register a similar-looking name — which is the
first requirement in this chapter, written as a prohibition for that reason.

Two other ideas run through what follows. A **role** is a ceiling, not a grant:
it says the most an entry could be allowed, and the entry can hold less.
And **acting** is separate from **approving** — the person who writes the
cheque and the person who countersigns it are doing different jobs, and an
approver who can also act is not a control.

### What this chapter defines

This chapter defines the boundary a VTI node organises everything it holds
into, and the model by which authority over those boundaries is granted,
narrowed, exercised, approved and withdrawn. The Sessions, Client, Verifiable
Trust Agent and Verifiable Trust Community chapters express their requirements
in the terms defined here.

### Trust contexts

A **trust context** is the isolation boundary within which a VTA holds keys,
credentials, stored data, agent memory and delegations, and it is the unit in
which authority is granted. A single VTA holds many contexts. Compromise of a
credential authorized in one context reaches what that context holds, and
nothing else.

**VTI-CTX-001** — Every key, credential, stored datum and delegation held by a
VTA MUST be associated with exactly one trust context.

**VTI-CTX-002** — A VTA MUST NOT perform an operation on a resource except
under authority whose scope covers the trust context that resource belongs to.

*Rationale.* Making the context the unit of grant, rather than the node or the
individual key, is what makes least privilege expressible: a purpose gets a
context, a client gets authority over that context, and the blast radius of
that client is legible without enumerating the resources it can reach.

### Context identifiers

A context identifier **is** the materialized path of the context within its
tree — `acme`, `acme/eng`, `acme/eng/team-a`. There is no separate parent
pointer, and no identifier that has to be resolved to discover where a context
sits.

**VTI-CTX-010** — A context identifier MUST be a non-empty sequence of
segments separated by U+002F SOLIDUS (`/`). SOLIDUS is the only separator.

**VTI-CTX-011** — Each segment MUST be non-empty, MUST NOT exceed 64 bytes, and
MUST consist only of the characters `A`–`Z`, `a`–`z`, `0`–`9`, FULL STOP (`.`),
LOW LINE (`_`) and HYPHEN-MINUS (`-`).

**VTI-CTX-012** — A context identifier MUST NOT contain a leading separator, a
trailing separator, or two consecutive separators.

**VTI-CTX-013** — A context identifier MUST NOT exceed 8 segments.

*Note.* The depth and segment-length limits are fixed by this specification
rather than left to implementations, so that an identifier valid at one node is
valid at every node. A limit chosen per deployment would make a context
creatable in one place and unaddressable in another, which is a failure that
appears only once the two are federated.

**VTI-CTX-014** — An implementation MUST NOT assign relative-path semantics to
any segment. A segment consisting of `.` or `..` is an ordinary segment naming
an ordinary context and MUST NOT be interpreted as a reference to the context
itself or to its parent.

**VTI-CTX-015** — Segments MUST be compared for equality octet by octet.
An implementation MUST NOT apply case folding, Unicode normalization or any
other transformation before comparison.

#### Ancestry

Ancestry is the predicate the authorization gate is built on. For identifiers
*a* and *d*, *a* is an **ancestor-or-self** of *d* when the segment sequence of
*a* is a prefix of the segment sequence of *d*.

**VTI-CTX-016** — Ancestry MUST be evaluated over whole segments. An
implementation MUST NOT decide ancestry with a byte-prefix or string-prefix
comparison over the complete identifier.

**VTI-CTX-017** — Authority over a context MUST extend to every descendant of
that context, and MUST NOT extend to any context of which it is not an
ancestor-or-self.

*Rationale.* A byte-prefix comparison reports `acme` as an ancestor of
`acme-evil`, which hands authority over one organisation's subtree to anyone
who can name a context whose first segment merely begins with it. This is the
single most consequential rule in the chapter, and it is stated as a
prohibition rather than as a definition because the incorrect implementation is
the shorter one.

#### Purity of the authorization gate

**VTI-CTX-018** — The decision whether a caller's authority covers a target
context MUST be computable from the caller's verified authority and the target
identifier alone. An implementation MUST NOT require the resolution of stored
context records to reach that decision.

*Rationale.* Path-encoded identifiers are chosen over parent pointers precisely
to make this possible. A gate that has to traverse a store to resolve ancestry
acquires, inside the security check, a question about what to do when the store
errors — which is a fail-open waiting to be written — along with a
denial-of-service surface, the possibility of a cycle in the parent graph, and
a gap between the moment ancestry is resolved and the moment it is used. A pure
gate has none of these. The price is that a context cannot be moved
(VTI-CTX-024), and it is worth paying.

The grammar for a context identifier is given in Appendix D.

### Context lifecycle

**VTI-CTX-020** — Creating a context MUST require that the creator holds act
authority covering the parent under which it is created. Creating a root
context MUST require unrestricted act authority.

**VTI-CTX-021** — A context MUST be bound at creation to a key-derivation base,
and that base MUST NOT change for the lifetime of the context.

**VTI-CTX-022** — The derivation base of a child context MUST nest under the
derivation base of its parent.

**VTI-CTX-023** — Deleting a context MUST either refuse when the context has
descendants, or delete the entire subtree. An implementation MUST document
which behaviour it implements, and MUST NOT delete a context while leaving any
descendant reachable.

**VTI-CTX-024** — A context identifier MUST be stable for the lifetime of the
context. A node MUST NOT support re-parenting a context, and MUST NOT rewrite
an existing context's identifier.

*Rationale.* Identifier stability is what allows a grant, an audit record and a
derived key to refer to the same context years apart without a translation
step. Re-parenting is not deferred for want of effort: moving a subtree
rewrites every descendant identifier and every grant that names one, which
means an authority statement written yesterday no longer resolves, and the
window during which it resolves to the wrong thing is exactly the window an
attacker would choose. A deployment that needs a different shape creates the
new context and migrates into it, where each step is an audited grant rather
than a silent rewrite.

**VTI-CTX-025** — A context identifier MUST NOT be reused for a new context
while any access control entry names it. Reuse of an identifier after deletion
is NOT RECOMMENDED in any case.

*Rationale for VTI-CTX-025.* An identifier that comes back into use inherits
every grant that named it and was not cleaned up, which converts an
administrative convenience into a silent restoration of authority.

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
caller's act scope overlaps the entry's act scope.

**VTI-ACL-051** — A caller MUST be able to read or list an entry where
VTI-ACL-050 permits modification, and additionally where the entry's approve
scope reaches the caller.

**VTI-ACL-052** — A subject MUST NOT modify its own entry, except by the
self-service rotation defined in the Client Onboarding chapter, which moves an
entry to a new subject identifier while preserving its authority exactly.

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

### Approvals, consent and step-up

Some operations are not safely authorized by a single credential. A node
expresses that with **approval rules**, evaluated before the operation runs.

There are two kinds of requirement, and they are not interchangeable.

| | Re-authentication | Consent |
|---|---|---|
| Who decides | the caller, re-proving with an additional factor | one or more parties other than the caller |
| Bound to | the caller's session, for a bounded window | the exact payload, by digest |
| Establishes | that the caller is still present | that a different party agreed to this change |
| Threshold | not applicable | N-of-M, optionally excluding the requester |

**VTI-APV-001** — An approval rule MUST name the operation it applies to, and
MAY additionally name the contexts it applies in. Where both a
context-scoped and an unscoped rule name the same operation, the
context-scoped rule MUST apply in the contexts it names.

**VTI-APV-002** — A node MUST evaluate applicable approval rules before the
operation's handler runs, and MUST reach the same decision on every transport.

**VTI-APV-003** — A re-authentication requirement MUST be satisfied only by the
caller, MUST raise the assurance level of the caller's session for a bounded
window, and MUST NOT be treated as evidence that any party other than the
caller agreed to anything.

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

### Audit

**VTI-AUD-001** — A node MUST record an audit event for: the creation,
modification, removal and rotation of an access control entry; the creation and
deletion of a context; the creation, modification and removal of an approval
rule; and every approval decision.

**VTI-AUD-002** — An audit event MUST identify the subject that acted, the
authority that permitted the action, the resource acted on, the time, and the
outcome.

**VTI-AUD-003** — A node MUST audit refusals as well as successful actions.

**VTI-AUD-004** — The audit trail MUST be tamper-evident: an observer MUST be
able to detect the modification or removal of a previously recorded event.

**VTI-AUD-005** — An audit record MUST NOT embed personal data in a form that
cannot later be erased. Where a record must refer to personal data, it MUST do
so by reference or by a salted commitment, such that erasing the data leaves
the record's integrity verifiable.

**VTI-AUD-006** — Access to the audit trail MUST itself be authorized, and MUST
itself be audited.

*Rationale for VTI-AUD-005.* Tamper-evidence and erasure are in genuine
tension: a chain that cannot be altered cannot have a name removed from it. The
tension is resolved at write time or not at all. A record that commits to a
reference rather than embedding the data can honour an erasure without breaking
the chain; a record that embedded the data leaves an operator choosing between
two obligations, and whichever they choose they will be in breach of the other.

*Rationale for VTI-AUD-006.* The audit trail is the most complete description
of a principal's activity that a deployment holds. Reading it is a privileged
act, and a control that records everything except who read it has a gap exactly
where an insider would stand.

*Rationale for VTI-AUD-003.* The refused attempts are the half of the record
that answers whether a control is working, and a trail containing only
successes cannot distinguish a system nobody attacked from one whose gate is
open.

### Where authority decisions live

Two node types could each plausibly own several of the decisions in this
chapter. This section states which one does, so that an implementer is never
left to infer it.

**VTI-ACL-090** — The VTA MUST be the authority for the context tree and for
key derivation within it. A VTC MUST NOT create contexts and MUST NOT derive
keys.

**VTI-ACL-091** — Where a VTC references context identifiers, it MUST evaluate
them using the ancestry predicate defined in this chapter.

**VTI-ACL-092** — A node MUST NOT infer ownership of an authority decision that
this specification does not assign. Where this specification is silent, an
implementation MUST refuse rather than assume.

*Rationale.* A decision with two plausible owners and no assigned one is a
decision that gets made twice, differently, and the disagreement surfaces as an
authorization gap rather than as an error. The purpose of VTI-ACL-092 is that
the gap is closed by refusal rather than by whichever implementation happens to
be more permissive.
