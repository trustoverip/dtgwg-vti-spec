## The Verifiable Trust Network

This section is normative, and specifies a node that has no implementation at
the time of publication. The requirements are stated so that a first
implementation has something to build against. One structural question — whether
a VTN is a distinct node type or a VTC operating in a curation role — is open,
and is recorded at the end of the chapter rather than settled in it.

### In plain terms

A [[ref: VTC]] is a club. A VTN is the association the clubs are listed in.

Two things separate the association from the club. It works at a coarser
grain: a club decides about people one at a time, an association points at
whole clubs. And it can point without being pointed back at — putting a club on
the list is something the association does by itself, where joining a club is
something two parties agree to.

The Internet has the same shape one layer down. An operator decides which
machines are on its network. An autonomous system decides which networks it
will carry and announce to everybody else, and the announcement is the
operator's to make. A VTN occupies the second position: it is the unit at
which whole communities are aggregated, announced and found.

That is the whole of it. A VTN curates. Everything below is about what a
curated list may and may not be taken to mean.

### What this chapter defines

This chapter specifies the network node: what it aggregates, what its published
set establishes and what it does not, how networks nest and overlap, and where
the authority boundary runs between a network and the communities in it. An
entry in a network's published set is a [[ref: listing]], and most of what
follows is about what one does and does not assert.

It defines no new authority model, publication mechanism or credential family.
A VTN uses the model in the Trust Contexts chapter, publishes under the Trust
Registries chapter, and issues and presents under the Credentials chapter.

### Position in the architecture

| Node | Decides | Unit of decision | Sides required |
|---|---|---|---|
| **VTA** | keys, contexts, delegated authority | one principal's own affairs | one — its own |
| **VTC** | admission, membership state, recognition | one subject at a time | two — both parties agree |
| **VTN** | which communities and networks it lists | a whole community at a time | one — the network alone |

A VTN sits above the VTC in **aggregation**, not in **authority**. It is not a
parent of the communities it lists. It grants them nothing, and removing a
community from its list takes nothing away from that community's members. The
two axes are independent, and everything a network is good for lives on the
first one.

What it is good for is discovery at scale. Without a network layer, every
relying party that wants to know about many communities keeps its own list, and
a community that wants to be found is found only by parties that already know
it. Cross-community recognition solves this pairwise, and pairwise does not
scale: *n* communities is *n²* relationships, and VTI-REG-024 deliberately
refuses the shortcut of making them transitive. A VTN is the other way to make
it scale — one curated set, with an owner accountable for it — and it makes
discovery cheap without making recognition transitive.

### Ownership and governance

**VTI-VTN-001** — A VTN MUST be provisioned on an existing VTA and MUST derive
its identity from it.

**VTI-VTN-002** — A VTN MUST have an identified owner, and MUST publish a
governance framework stating the criteria for listing, the criteria for
removal, and the means by which a listed party may contest an entry concerning
it.

**VTI-VTN-003** — A VTN's identifier MUST be resolvable by a party with no prior
relationship to the network, using a method satisfying VTI-KEY-001.

**VTI-VTN-004** — A VTN MUST express its own roles, capabilities, approve scope
and approval rules using the model defined in the Trust Contexts chapter, and
MUST NOT define a parallel model.

**VTI-VTN-005** — An administrative action on the network MUST be subject to the
approval requirements of that chapter.

*Rationale for VTI-VTN-002.* A curated set is a published assertion about other
parties, made without their agreement. The owner is what makes such an
assertion contestable: a list nobody owns is a list nobody can be asked to
correct, and an uncontestable assertion about a third party is one that party
has no defence against. The criteria matter for the same reason — a removal
that cannot be checked against a stated rule is indistinguishable from a
removal made for any other reason.

### Curation is one-sided

**VTI-VTN-010** — A VTN MAY list a VTC or another VTN without the agreement of
the listed party.

**VTI-VTN-011** — Every published entry MUST state whether the listed party has
acknowledged it. An entry that has not been acknowledged MUST be published as
unacknowledged.

**VTI-VTN-012** — A VTN MUST provide a means by which a listed party can
acknowledge or repudiate an entry concerning it, and MUST publish a repudiation
it has received alongside the entry.

**VTI-VTN-013** — A VTN MUST NOT describe an unacknowledged entry as membership,
and MUST NOT issue a membership credential to a party that has not acknowledged
its entry.

**VTI-VTN-014** — Where membership of a VTN is two-sided, it MUST follow the
Membership Lifecycle chapter: admission is a decision, evidenced by a
credential, with states, audit and departure dispositions as required there.

*Rationale for VTI-VTN-011.* One-sidedness is what makes a network useful — a
curator who has to negotiate every entry curates nothing — and it is also what
makes a network's list easy to misread. *They list us* and *we joined* are
different facts about different numbers of parties, and a consumer who cannot
tell them apart will read the weaker as the stronger every time, because the
stronger is what a list normally means. Publishing the distinction costs one
field. Recovering it afterwards is impossible.

*Rationale for VTI-VTN-012.* A party listed against its wishes has two
remedies: argue with the owner, or say publicly that it does not accept the
entry. Requiring the network to publish repudiations keeps the second remedy
inside the system, where a consumer resolving the entry will see it, rather
than leaving it to be conducted in announcements that nobody resolving an entry
ever reads.

### What a listing establishes

**VTI-VTN-020** — Presence in a VTN's published set MUST NOT be treated as
evidence of current authority. VTI-REG-010 binds a network's answers as it
binds any other registry answer.

**VTI-VTN-021** — A consumer MUST determine whether an entry is acknowledged
before relying on it, and MUST NOT treat an unacknowledged entry as an
acknowledged one.

**VTI-VTN-022** — Listing MUST NOT be transitive with recognition. A party that
recognises a VTN MUST NOT thereby recognise the parties that VTN lists, and a
party listed by a VTN MUST NOT thereby recognise the others it lists. See
VTI-REG-024.

**VTI-VTN-023** — A party MAY adopt a policy of extending recognition to the
parties a VTN lists. Such a policy MUST be explicit, MUST name the network and
the version of its governance framework relied on, and MUST NOT be inferred
from the existence of the listing.

**VTI-VTN-024** — A VTN's answers MUST satisfy VTI-REG-002, VTI-REG-011 and
VTI-REG-012: the framework identified, the determination time carried, and
unavailability treated as indeterminate rather than as absence.

*Rationale for VTI-VTN-022 and VTI-VTN-023.* A network creates exactly the
temptation that VTI-REG-024 refuses — one relationship standing in for a
hundred. These two requirements keep the convenience and refuse the inference.
A relying party may still decide, in a single act, to accept everything a
network lists; the difference is that it is a decision, it names what it rests
on, and it can be withdrawn when the network's framework changes. An inference
cannot be withdrawn, because nobody recorded making it.

### Networks of networks

**VTI-VTN-030** — A VTN MAY list other VTNs. This specification imposes no limit
on the depth of nesting.

**VTI-VTN-031** — Every entry MUST identify the kind of the listed party —
community or network — so that a consumer can tell whether the entry points at
members or at further entries.

**VTI-VTN-032** — Nesting MUST NOT alter what an entry establishes. A consumer
resolving through a chain of networks MUST evaluate the acknowledgement state
of each entry in the chain, and MUST NOT infer the state of one entry from
another.

**VTI-VTN-033** — A VTN that lists another network MUST NOT represent that
network's entries as its own.

*Rationale.* Three levels — state networks aggregated nationally, national
networks aggregated globally — is the obvious structure and probably the common
one, and it is also where flattening happens. By the time an entry has been
through three aggregations it reads as a single global assertion with one name
on it, and the three owners, three frameworks and three acknowledgement states
behind it have disappeared. Keeping each step's own owner and own
acknowledgement state visible is what keeps a global network a list of lists
rather than a claim.

### Overlapping networks

**VTI-VTN-040** — A VTC or a VTN MAY be listed by any number of networks. A VTN
MUST NOT require, as a condition of listing or of membership, that a party not
be listed elsewhere.

**VTI-VTN-041** — A consumer MUST NOT treat listing by more than one network as
a conflict, and MUST NOT treat listing by several networks as stronger evidence
than listing by one unless it has established that those networks are
independent, as required by VTI-CMP-071.

*Rationale.* Overlap is the expected condition rather than a defect: nothing
stops three parties each curating a set for the same subject, and the ordinary
outcome is three networks with different criteria, different owners and
substantially the same members. Counting them is where it goes wrong. Networks
that list each other's members, or that share a secretariat and a governance
framework, are one opinion published three times, and a relying party that adds
them up has manufactured corroboration out of duplication — the failure
VTI-CMP-070 describes.

### Credentials a network issues and receives

**VTI-VTN-050** — A VTN MAY issue and receive DTG credentials, including
membership and endorsement credentials, and MUST issue, hold and present them
under the Credentials chapter.

**VTI-VTN-051** — An endorsement credential MUST state what is endorsed, under
which governance framework and version, and for what period.

**VTI-VTN-052** — An endorsement MUST NOT confer authority. A consumer MUST NOT
infer from an endorsement any capability, role or scope in the endorsing
network or in any party it lists.

**VTI-VTN-053** — Removal of a party from a network MUST be reflected in the
status of any credential the network issued to evidence the entry, and MUST
take effect for verifiers that check status.

*Rationale for VTI-VTN-052.* Endorsement is the credential most likely to be
read as more than it says, because in ordinary language endorsing somebody is
close to vouching for them. A conformance endorsement — *this community passed
our interoperability tests, on this date, against this version* — is a statement
about a test result. It says nothing about what the community is entitled to do
today, and it is issued by a party that was never asked.

### What a network is not

**VTI-VTN-060** — A VTN MUST NOT create trust contexts and MUST NOT derive keys.
See VTI-ACL-090.

**VTI-VTN-061** — A VTN MUST NOT make key-custody decisions for a party it
lists, or for the members of such a party.

**VTI-VTN-062** — A VTN MUST NOT alter the membership state of a member of a
VTC. Admission, suspension and removal of members belong to the community, as
required by VTI-MEM-010.

**VTI-VTN-063** — Removal of a community from a network MUST NOT be represented
as a change to that community's membership, and removal of a member from a
community MUST NOT be represented as a change to any network entry.

*Rationale.* The pressure to collapse these comes from the thing that makes
networks attractive to operate: a network that can remove a community has
leverage, and leverage is easily mistaken for enforcement. It is not
enforcement. It is a second membership decision, made about somebody else's
members, by a party that never admitted them and answers to nobody for it. A
network's remedy against a community it disapproves of is to stop listing it,
publicly and under its stated criteria, and that remedy is sufficient.

### The open question: one node type or two

This subsection is informative.

A VTN and a VTC share most of their surface. Both have an owner and a
governance framework, both are provisioned on a VTA, both publish and answer
lookups, both issue and receive credentials, and both have something that looks
like joining. The differences are narrow: a VTN's relationships may be
one-sided, and it aggregates at a coarser grain.

Whether that is enough to make the VTN a distinct node type, or whether a VTN
is better understood as a VTC operating in a curation role — one-sided listing
enabled, per-subject admission unused — is not settled.

| If the resolution is | Then |
|---|---|
| **A distinct node type** | Its own conformance target and its own tests; the two node specifications may diverge freely where the roles differ. The cost is two implementations of a largely shared surface, and the drift between them. |
| **A role of the VTC** | One implementation and one test suite, with curation as a configured mode. The cost is that every requirement holding for one role and not the other becomes conditional, and a reader must establish which role is in force before any requirement binds. |

The requirements above are written so that either resolution is reachable: they
state what a network must do, not what it must be built out of. The `VTN`
requirement area is allocated, and the conformance target is marked provisional
until the question is closed. Should the working group settle on the second
answer, the requirements survive with their identifiers and bind a VTC
operating in the curation role.

### Illustrations

This subsection is informative. The organisations named are used to make the
shapes concrete. Naming one here is neither recognition of it nor an
endorsement by this specification or the Working Group.

- **A software foundation's communities.** The communities working under one
  open-source foundation, curated into a single network. The foundation is in a
  position to list a community that has never asked to be listed, and often
  will be — which is the one-sided case of VTI-VTN-010, and the reason
  VTI-VTN-011 exists.
- **Health services, three levels deep.** State networks of healthcare
  communities, aggregated into a national network, aggregated into a global
  one. This is the nesting case, and the flattening it invites is what
  VTI-VTN-032 and VTI-VTN-033 are written against.
- **Interoperability and conformance bodies.** An organisation running
  interoperability and conformance testing — Ayra is working in this space —
  is a natural network owner: the tests produce exactly the endorsement
  credential of VTI-VTN-051, and the set of parties that passed them is the
  curated list. It is also the clearest case for VTI-VTN-052, since passing a
  test says nothing about authority to act.
- **A network organised around people rather than institutions.** The First
  Person Collective's First Person Network illustrates that the curation
  criterion need not be a sector or a jurisdiction. A network's criterion is
  whatever its governance framework says it is, which is why VTI-VTN-002
  requires that the framework say so.
