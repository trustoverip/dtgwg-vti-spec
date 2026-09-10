## Trust Contexts and the Authority Model

{{This section is normative.}}

This chapter defines the isolation boundary a VTI node organises everything
else around, and the authority model built over it. It is a prerequisite for
the Sessions, Client, Verifiable Trust Agent and Verifiable Trust Community
chapters, all of which express their requirements in its terms.

### Trust contexts

{{What a trust context is: the isolation boundary for keys, credentials, data,
agent memory and delegations, and the unit in which authority is granted. One
node, many contexts. Why the context — not the node and not the key — is the
unit of blast-radius containment.}}

### Context identifiers

{{Context identifiers are **path-encoded and nested**: the identifier is the
materialized path of the context within its tree. This section specifies the
segment character set and length limits, the single permitted separator, the
maximum depth, and the ancestry predicate.

The ancestry test is **segment-aware**: a context identifier is an ancestor of
another only when its segments are a prefix of the other's segments. A raw
string prefix test is not sufficient and MUST NOT be used — under it, an
identifier would falsely appear to be an ancestor of an unrelated identifier
that merely shares a leading substring.

Rationale to be preserved in the text: path encoding keeps the authorization
gate pure — a comparison over data already present in the verified credential,
with no store traversal inside the security check. That removes an entire class
of failure (fail-open on store error, resolution cycles, and time-of-check /
time-of-use gaps) and makes the reach of a grant legible on its face.

Grammar in Appendix D.}}

### Context lifecycle

{{Creation under a parent; nesting; deletion (cascade versus refusal on a
non-empty subtree); enumeration of a subtree. Re-parenting is out of scope for
this version, and the cost of that choice is stated rather than hidden. The
binding between a context and its key-derivation base, and the requirement that
the base is immutable and nests as the context does.}}

### The access control entry

{{The entry is the only authority artefact: subject identifier, role, contexts,
capabilities, approve scope, expiry, provenance. Nothing is authorized by
configuration alone. Fail-closed defaults for every field.}}

### Roles

{{The role set, and the rule that a role is a **ceiling** rather than a grant.}}

### Super-administrators and context administrators

{{How each is spelled in an entry: an unrestricted act scope is what a
super-administrator is; a context-scoped administrator administers the entire
subtree beneath its grant.

Normative rule: an empty context list is meaningful only in combination with
the role, and an implementation MUST NOT decide anything from the emptiness of
the list alone. Both known failures of this rule were privilege defects — a
least-privilege approver displayed as unrestricted on the screen operators use
to audit grants, and the same entry shape reaching a credential store in every
context.}}

### Capabilities

{{Capabilities narrow what a role permits. A capability outside the role's
ceiling MUST be refused rather than silently dropped. This section also
specifies the treatment of additive capabilities — those no role implies.

The capability set is an **extensible registry**, not a closed list in this
specification: the set of separately gateable powers grows as the
infrastructure does, and a closed list would make every new gate a breaking
change to the specification. The registry is accompanied by a versioned annex
naming the capabilities defined at the time of publication.

Extension is made safe by one rule: an implementation MUST NOT treat a
capability it does not recognise as granted. A consumer that has never heard of
a capability is precisely the consumer that must not assume it holds it, and a
decision to ignore an unrecognised capability MUST be written down rather than
reached by default.}}

### Two axes: acting and approving

{{An entry answers two independent questions: in which contexts may this
subject *make* a change, and in which may it *bless* someone else's change.
Both are three-valued, share the ancestry predicate, and default closed. The
least-privilege approver — a subject that acts nowhere and approves in named
contexts — is the shape that motivates the split.}}

### Approvals, consent and step-up

{{Two distinct requirements that MUST NOT be conflated: re-authentication,
which raises the assurance level of the caller already present, and
second-person approval, which requires a different human. The requester MUST
NOT satisfy their own two-person requirement.

Approval rules bind to operation identifiers and MUST be evaluated before the
operation's handler and identically on every transport. This section also
specifies the consent ceremony: what an approver is shown, the timers, who may
approve, and what a refusal means to the caller.}}

### Reading is not managing

{{Visibility (may update or delete an entry) and auditability (may list or read
it) are separate predicates over a single entry, and this section defines both.}}

### The direction of a context query

{{A filter over a context is two questions, not one: *who may act in this
context*, which reads up the tree, and *what is granted beneath it*, which
reads down. A subtree revocation asked with the first returns precisely the
entries that are not being revoked and omits every leaf-scoped grant the sweep
exists to cut — an answer that is short rather than empty, and therefore looks
complete.

The direction of a query MUST therefore be explicit. An unrecognised direction
MUST be refused with the set of valid values and MUST NOT be defaulted.}}

### Delegation and revocation

{{Delegating authority to a software agent; the scope, lineage, expiry and
re-delegation rules; immediate revocation; and what a subtree sweep MUST cover.
A grant below a swept context that continues to act while the revocation
reports success is the specific failure this section exists to prevent.}}

### Audit

{{Every change of authority produces an audit event of defined shape.
Tamper-evidence requirements for the audit trail.}}

### Where authority decisions live

{{Which target owns which gate. The VTA is the context and key authority; a VTC
references context identifiers without being a key authority, and any filter it
applies over them MUST use the ancestry predicate defined here, so that two
nodes cannot disagree about the reach of a scope.}}
