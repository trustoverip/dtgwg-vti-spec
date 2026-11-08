## Trust Contexts

This section is normative.

### In plain terms

A trust context is a **partition**. Everything a node holds — keys,
credentials, data, the authority it has delegated — belongs to one of them, and
a grant is always a grant over particular partitions rather than over the node.

Think of the bulkheads in a ship. They are not there to keep things tidy; they
are there so that a breach floods one compartment instead of the hull. The
payoff comes on the bad day: a credential that leaks reaches what its
partitions hold and nothing else, so "how bad is this?" is a question you
answer from the grant itself rather than by auditing everything its holder ever
touched.

Partitions nest, and authority over one reaches everything beneath it. That is
why the way a partition's name is compared matters so much: `acme` contains
`acme/eng`, and must not be read as containing `acme-evil`, which merely starts
with the same letters. One careless string comparison hands an organisation's
authority to anyone who can register a similar-looking name — which is why the
central requirement in this chapter is written as a prohibition.

### What this chapter defines

This chapter defines the boundary a VTI node organises everything it holds
into: what a trust context is, how it is named, how one context is decided to
contain another, and how contexts are created and retired. The three chapters
that follow build the authority model over it — Access Control and Authority
states how authority over contexts is granted, narrowed, delegated and
withdrawn; Approvals, Consent and Step-Up states when a single credential is
not enough; Audit states what is recorded. The Sessions, Client, Verifiable
Trust Agent and Verifiable Trust Community chapters express their requirements
in the terms these four chapters define.

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

### Creating a context without administrative authority

Some callers create contexts as a matter of course and ought not to be
administrators. A process that mints a context for each software agent it
manages, on a host that faces the network, is the case this section exists for:
holding the administrative role would let a compromise of that process rewrite
the access control entries of everything it created, and creating a context is
the one power it needs.

**VTI-CTX-026** — A node MAY let an entry that holds the additive capability
`context-create` (Appendix C) create a context beneath a context that the
entry's act scope covers, whatever the entry's role. Such a creation MUST meet
every requirement on creating a context in this section, VTI-CTX-020 through
VTI-CTX-022 included.

**VTI-CTX-027** — The `context-create` capability MUST NOT permit creating a
root context, and MUST NOT permit creating a context beneath one that the
entry's act scope does not cover; both remain as VTI-CTX-020 states them. It
MUST confer no other authority. In particular, creating a context MUST NOT
write an access control entry for the creator and MUST NOT otherwise enlarge
what the creator may do, so that the authority a creator holds over what it
created is exactly what its entry already gave it over that part of the tree
(VTI-CTX-017).

**VTI-CTX-028** — An entry that holds `context-create` MAY set the primary DID
of a context that its act scope covers, and only while that context has none.
The identifier it names MUST be one that only keys the context holds can sign
as, and, once set, the entry MUST NOT change or clear it; changing or clearing
it remains an act of administrative authority.

*Rationale for VTI-CTX-026 and VTI-CTX-027.* A context administrator can write
access control entries scoped to the context, so making a creator an
administrator of what it creates lets its authority accumulate, one context at a
time, for as long as it runs — and an automated creator is the process that
should hold it least. Making the capability additive (VTI-ACL-033) means no
role implies it, so no change to a role hands creation to every holder of that
role, and granting it needs unrestricted act authority: a single decision by a
person, not something a process acquires. What it confers is bounded by rules
that already hold. A root context still needs unrestricted act authority
(VTI-CTX-020), and ancestry is still evaluated over whole segments
(VTI-CTX-016), so the capability reads as "branch beneath what I already
cover", never "create anywhere". It is consistent with VTI-VTA-032: creating a
context confers no authority the creator was not already given.

The alternative considered was a separate, always-available service that holds
unrestricted act authority and creates contexts on callers' behalf. It was
rejected because it is a new component whose only purpose is to hold the most
powerful grant in the system.

*Rationale for VTI-CTX-028.* A context with no primary DID exists but can be
issued as nothing, so a creator that cannot name one has made something it
cannot make usable and would need an administrator to finish every context it
creates, which defeats the grant. Naming is permitted once and only once
because changing a context's identity is how an automated process would quietly
become something else, and that is rare enough to be an administrator's
decision. The identifier has to be one the context's own keys control because
otherwise a creator could name an identifier the context cannot sign as, and
anything then issued as that context would claim an issuer nothing in it can
produce.

The requirement bounds naming by what the context holds and by the creator's act
scope, and not by whether the entry created that particular context. A node need
not record who created a context to enforce it: reaching the context and naming
only an identifier its own keys control leave nothing for a creator to gain by
filling the blank on a context it did not create.
