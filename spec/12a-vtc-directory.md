## The Community Directory and Groups

This section is normative.

### In plain terms

A club keeps a members' book, and it puts some of its members on committees.

The book is where members find each other. What makes it hard is that every
member wants a different book: one wants to be found by everyone, one by the
people they work with, one by nobody at all. So the book is not a list the
secretary writes. Each member writes their own page and says, line by line, who
may read it; the club says how far any line may go; and the secretary hands out
only what both allow. A member who wants to be left alone is not in the book
when you browse it, and a stranger asking after them by name learns nothing —
not even whether they are a member.

The book does not hold what members think of each other. A star next to a
friend's name, a private nickname, a note to oneself: those are kept in each
member's own pocket, where the secretary cannot read them, because a secretary
who knows who watches whom knows more about the club than anyone should.

A committee is a group of members. Being on one can open a door — to a shared
room, to a duty — and the danger is that the committee list quietly becomes a
second register of who may do what. So it never answers that question. When a
committee carries a duty, the secretary writes the duty into each member's own
entry in the register, notes that it came from the committee, and rubs it out
when the member steps down. The register stays the one place authority lives.

### What this chapter defines

This chapter specifies a community's member directory — the cards members keep,
who may read them, how members are found and introduced — and the community's
groups: how a group is identified, what its membership evidences, how it gives
access to the community's rooms, and how authority it carries reaches its
members.

A VTC is not required to offer a member directory or groups. A VTC that offers
either MUST satisfy the requirements of this chapter that concern it; a VTC that
offers neither is not bound by them.

The chapter adds no authority model. Every decision about authority is made by
the model of the Access Control and Authority and the Approvals, Consent and
Step-Up chapters (VTI-VTC-020), and this chapter states how groups feed that
model, never how they stand beside it. The directory is part of what the
community publishes about its members (VTI-VTC-012), within the community and,
where the member chooses, beyond it.

Three parties hold directory data, and each kind of data is held by its owner:

| Owner | Holds | Where |
|---|---|---|
| The community | the field schema, groups, administrators' notes | the VTC |
| The member | their own card and who may read each part of it | the VTC |
| The viewer | what they think of other members: marks, personal lists, personal notes, private names | the viewer's own VTA |

The capabilities this chapter refers to are registered in Appendix C.3:
`vtc.directory.admin`, the capability to administer the directory;
`vtc.directory.notes`, the capability to keep notes about members;
`vtc.groups.manage`, the capability to manage groups, qualifiable by group; and
`vtc.rooms.manage`, the capability to manage the rooms the community owns.

Throughout this chapter, the **subject** of a card is the member it describes,
and a **viewer** is the party a read is answered to. An **entry** is the
directory's record of one member, addressed by its entry identifier.

### What the community does not hold

**VTI-DIR-001** — A VTC MUST NOT store a member's annotations about other
members — a mark such as a star, a personal list, a personal note, or a private
name for another member — in any form, including a form only that member can
decrypt.

**VTI-DIR-002** — A VTC MUST NOT require a member to disclose such an
annotation to use any operation in this chapter.

*Rationale for VTI-DIR-001.* Who annotates whom is a social graph, and a
community that holds it can be compelled to produce it or can lose it. Holding
it encrypted does not help: the record's existence, its size and the time it
was written still show the operator who is watching whom. The member's own
agent already keeps one record per relationship, and is the one place this data
can be kept without anyone else learning it exists. A page that shows both the
community's entries and the member's annotations joins them on the member's own
device.

### Cards and fields

A **card** is the subject's own record in the directory: the values they hold,
a visibility for each value, and their discoverability settings. A **field** is
a named slot a card may fill, defined by the community, with a **ceiling** — the
widest audience any value of it may reach.

**VTI-DIR-010** — A field definition MUST state its key, its type, its ceiling,
the visibility a new value takes when its subject does not choose one, and who
writes its values: the subject (*self-asserted*), an administrator
(*administrator-asserted*), or a credential the subject presents
(*credential-sourced*).

**VTI-DIR-011** — A value that another record of the community already holds —
the member's role, the date of their admission, their membership identifier,
and whether a personhood assertion is on file — MUST be derived from that
record at the time it is read, and MUST NOT be copied onto the card.

**VTI-DIR-012** — A card update MUST be a document signed by the subject's
membership identifier and carrying its time of issue, under VTI-OPS-020. A VTC
MUST refuse an update issued earlier than the update its card last accepted.

**VTI-DIR-013** — An administrator-asserted value MUST be written only by an
operation that requires the capability to administer the directory, and a
credential-sourced value only by the subject presenting the credential. A card
update by the subject that would change either MUST be refused.

**VTI-DIR-014** — A credential-sourced value MUST be accepted only from a
presentation that satisfies VTI-CRD-021 and VTI-CRD-022, and the VTC MUST
retain only the attributes the field holds (VTI-CRD-042). A credential-sourced
value MUST be shown with the issuer of the credential it came from. A
self-asserted or administrator-asserted value MUST NOT be presented as
verified.

**VTI-DIR-015** — A field whose values exist MUST NOT be deleted. A field is
withdrawn by retiring it: a retired field's values MUST be withheld from every
read at once, and MUST be erased after a retention period the community
states. A field key MUST NOT be reused for a field of another meaning.

**VTI-DIR-016** — A VTC MUST bound the number of fields a community may define
and the size of a card. A VTC MUST bound the size of an image held for a card,
and MUST re-encode it before storing it, so that metadata embedded in the
original is removed and the stored image does not reproduce the original's
digest.

**VTI-DIR-017** — A VTC MUST NOT copy an applicant's answers to the community's
join questions onto their card unless the applicant agreed to that use when
answering. A value copied with that agreement MUST be given the narrowest
visibility, for the member to widen.

*Rationale for VTI-DIR-011.* A copy of a role is a second statement of it, and
the two disagree the first time one is changed without the other. A directory
that showed a role the member no longer held would be asserting, to every
member who reads it, authority that the access control model has withdrawn.

*Rationale for VTI-DIR-014.* A badge is a claim that someone checked. Shown on a
value nobody checked, it lends the community's credibility to whatever the
member typed; shown without its issuer, it asks the reader to trust a
verification they cannot evaluate.

*Rationale for VTI-DIR-016.* The image a member uploads to two communities is,
byte for byte, the same file, and its digest is an identifier that joins their
two entries for anyone who can see both. Re-encoding breaks the join; it also
removes the location and device a photograph often carries.

*Rationale for VTI-DIR-017.* Join answers were given for an admission decision,
and the applicant agreed to that purpose. Reusing them for a directory is a
second purpose, and the first agreement does not cover it.

### Audiences

An **audience** is a set of viewers. The audiences, from narrowest to widest:

| Audience | Viewers |
|---|---|
| `admins` | holders of the capability to administer the directory and, for derived values, of the capability to manage members |
| `groups` | members who share, with the subject, a community group the subject has named for the value |
| `connections` | members who hold, with the subject, an in-force verified relationship whose two endpoints are their membership identifiers |
| `members` | every member in good standing |
| `public` | anyone, by the published card of VTI-DIR-046 |

`groups` and `connections` are not nested: a viewer in one need not be in the
other.

**VTI-DIR-020** — A value MUST be disclosed to a viewer only where the subject's
visibility for it admits the viewer, the field's ceiling admits the viewer, and
community policy permits the disclosure. The three MUST be applied together, so
that each can only narrow what the others permit. Where `groups` and
`connections` are both admitted, a viewer in either is admitted.

**VTI-DIR-021** — Community policy MUST NOT disclose a value beyond what the
subject's visibility and the field's ceiling admit (VTI-VTC-022).

**VTI-DIR-022** — The subject of a card MUST be able to read the whole of it,
including every value withheld from others.

**VTI-DIR-023** — A VTC MUST determine `connections` only from verified
relationships whose two endpoints are the subject's and the viewer's membership
identifiers. It MUST NOT attempt to establish a relationship held under any
other identifier, and MUST NOT disclose that two members hold a relationship to
anyone but the two of them, unless both have disclosed it.

**VTI-DIR-024** — A community MUST NOT widen the visibility a subject chose, or
a discoverability setting a subject chose. Narrowing a field's ceiling MUST
take effect at once. Widening a field's ceiling, or the visibility or listing a
new card is given, is a change to the community's privacy semantics
(VTI-VTC-032, VTI-CMP-090): it SHOULD require consent from an administrator
other than the requester, and MUST NOT change any choice a subject has already
made.

*Rationale for VTI-DIR-020.* The subject's choice and the community's ceiling
answer different questions — what this member is willing to share, and what
this community is willing to let anyone share — and policy answers a third. A
union of the three would let any one of them overrule the others, and the one
most likely to do so is the one the subject never sees. Intersection lets each
party refuse, and none of them grant on another's behalf.

*Rationale for VTI-DIR-023.* Relationships are pairwise by default
(VTI-KEY-005), and a pairwise relationship is one the community was meant not
to see. A community that tried to match them to its members would be doing
exactly the correlation the pairwise identifier exists to prevent. The badge a
member sees for such a relationship is computed by their own agent.

*Rationale for VTI-DIR-024.* A member who chose `members` chose it under the
ceiling that stood when they chose. Widening the ceiling and carrying their
choice with it would publish their data under a decision they did not make;
this is the same asymmetry as recognition consent, which an administrator may
withdraw and never give. A widening is consented to by a second administrator
for the same reason as a change to join criteria (VTI-APV-023): it changes what
membership exposes, for everyone, at once.

### Administrators' notes

**VTI-DIR-030** — Notes an administrator keeps about a member MUST be held
apart from the member's card, and MUST NOT be included in any read of the
directory, whatever policy returns.

**VTI-DIR-031** — Notes MUST be read and written only by holders of the
capability to keep them. A note MUST NOT be altered once written: a correction
or withdrawal is a later note that refers to it.

**VTI-DIR-032** — Every read and write of a note MUST be audited, and the audit
record MUST refer to the note without containing its text (VTI-AUD-005).

**VTI-DIR-033** — Whether the community keeps notes about members MUST be part
of its disclosure statement (VTI-DIR-130). Releasing a member's notes to that
member, as in answering their request for the data held about them, MUST be an
administrator's operation requiring re-authentication bound to it, and MUST be
audited at the node's highest severity.

*Rationale for VTI-DIR-030.* A field that "only administrators can see" is one
policy change, one misconfigured ceiling or one projection defect away from
being seen by members. A note that is not on the card at all cannot reach a
reader by any of those paths.

### Discoverability

Discoverability is three separate choices, because members make them
differently: whether the entry appears when members browse (**listing**),
whether a search can find it (**search**), and whether it is published beyond
the community (**external**).

**VTI-DIR-040** — A card MUST carry the subject's listing, search, introduction
and external settings, each the subject's to choose:

1. listing: `listed`, returned when members browse; `findable`, returned only
   by a search that matches it; or `hidden`, returned to no member by browsing
   or searching. Every subject MAY choose any of the three, and a community
   MUST NOT withhold `hidden` from any subject;
2. search: whether a search may match the subject's name or nickname, only an
   exact nickname, or nothing;
3. introductions: from whom the subject accepts introductions (VTI-DIR-060);
   and
4. external: whether the subject's published card is offered beyond the
   community (VTI-DIR-046).

**VTI-DIR-041** — A VTC SHOULD give a new card the listing `listed`, with the
subject's display name and community role visible to `members` and every other
value visible to `admins`. A community MAY give new cards a narrower default.

**VTI-DIR-042** — A VTC MUST NOT give a new card a membership identifier
visible beyond `admins`, any value visible at `public`, or the external setting
on. Each of these MUST be the subject's own choice.

**VTI-DIR-043** — A hidden entry MAY be shown in the member list of a group or
room the viewer shares with the subject, where that group or room shows its
members to the viewer.

**VTI-DIR-044** — A membership identifier MUST NOT be accepted as a search key
from a viewer other than an administrator of the directory.

**VTI-DIR-045** — A VTC SHOULD refuse a search shorter than a minimum length,
and SHOULD bound the number of matches a search returns.

**VTI-DIR-046** — Where the external setting is on, the community MAY publish a
pointer to a card containing only the subject's `public` values, signed by the
subject. The community MUST NOT publish profile data it has signed or composed
itself. External publication is publication for a purpose other than
recognition, and VTI-REG-004 governs it.

**VTI-DIR-047** — The external setting MUST be a consent separate from the
member's consent to recognition, and MUST require it. Withdrawing recognition
consent MUST withdraw external publication.

*Rationale for VTI-DIR-041.* A directory nobody is in is no directory, and an
opt-in default empties it; a default that shows everything shows more than
most members would have chosen. A name and a role is the least that makes an
entry useful, and what a member of a small working group expects others to
know. A large or sensitive community narrows the default and says so.

*Rationale for VTI-DIR-044.* A membership identifier is the one value an
outsider is likely to hold already. Accepting it as a search key turns the
directory into an oracle answering "is this identifier a member here?" for
anyone who can sign in.

*Rationale for VTI-DIR-046.* A card the member signs is the member's statement,
and a verifier can tell it from the community's. A card the community signs is
the community asserting personal data about a person beyond its walls, which
is exactly what the community's role in recognition was bounded to avoid.

### Entry identifiers and correlation

**VTI-DIR-050** — A VTC MUST address each entry by an entry identifier that is
generated at random, scoped to the community, and neither derived from nor
revealing any identifier of the member. The entry identifier MUST NOT change
when the member's identifier is rotated.

**VTI-DIR-051** — A VTC MUST NOT link a member's entry to that member's entry in
another community, and MUST NOT disclose anything in a directory read that does
so, other than a value the subject chose to disclose.

**VTI-DIR-052** — A relationship begun through the directory SHOULD be held
under identifiers distinct to that relationship (VTI-KEY-005).

**VTI-DIR-053** — Among the durable correlators it identifies under
VTI-CMP-062, a VTC MUST count entry identifiers, the published card of
VTI-DIR-046, and the identifier of each community group (VTI-DIR-080).

*Rationale for VTI-DIR-050.* Every store that holds directory data — cards,
group memberships, notes — has to survive the member rotating their identifier
(VTI-MEM-041), and has to be erased when they leave. A store keyed by the
identifier must be rewritten on rotation and searched on departure, and a key
derived from the identifier is the identifier by another name. An identifier
of the entry's own avoids both.

### Introductions

An **introduction** lets one member ask to be put in touch with another without
either learning the other's membership identifier.

**VTI-DIR-060** — An introduction request MUST name the recipient by entry
identifier, and MUST carry the identifier under which the requester proposes
the relationship, a message of bounded length, and only those of the
requester's values whose visibility already admits the recipient. The
requester's identifier SHOULD be one created for that relationship.

**VTI-DIR-061** — A VTC MUST deliver an introduction only where both parties
are members in good standing, the recipient can be found by the requester under
the recipient's discoverability settings, and the recipient accepts
introductions from the requester.

**VTI-DIR-062** — The answer to an introduction request MUST NOT reveal whether
the recipient exists, can be found by the requester, accepts introductions from
the requester, or received the introduction. A refusal that depends only on the
requester's own conduct, such as the number of requests they have made, MAY be
reported to the requester. A limit on requests from one requester to one
recipient MUST be applied silently.

**VTI-DIR-063** — A VTC MUST NOT take part in the recipient's answer to an
introduction, which is made directly between the two parties. A VTC MUST
retain an introduction only for a bounded period stated by the community, and
MUST audit the request by reference (VTI-AUD-005).

*Rationale for VTI-DIR-062.* An introduction answered "refused: not found" or
"refused: does not accept introductions" is a membership lookup by another
name, and a cheap one. Answering every request alike is what lets the
directory offer contact to people who cannot be looked up. A limit counted per
recipient is information about the recipient, and reporting it would reopen
the oracle.

*Rationale for VTI-DIR-063.* The community learns that one member asked to meet
another, which it needs to handle abuse. It does not need to learn whether they
met, and a design in which the answer passes through it would teach it.

### Groups

A **community group** is a set of members the community defines. Its membership
is **static**, changed by joining, leaving and management, or **dynamic**,
computed by a rule. A group may be **bound** to a community room, giving its
members access to the room, or to a set of capabilities, giving its members
that authority.

**VTI-DIR-070** — A group's visibility — who may know it exists — and the
visibility of its member list MUST be separate settings. A member list MUST be
returned only to viewers its setting admits, and MUST be returned by a
paginated listing, never inline in a read of the group.

**VTI-DIR-071** — The authority to manage a group MUST be a capability on the
manager's access control entry, qualified by the group (VTI-ACL-035), and its
grant is a delegation subject to VTI-ACL-071 and VTI-ACL-072.

**VTI-DIR-072** — A dynamic group's membership MUST be computed only from facts
the community has verified or decided — community role, administrator-asserted
and credential-sourced values, personhood and date of admission — and MUST NOT
depend on a self-asserted value. A VTC MUST recompute it when a fact it depends
on changes.

**VTI-DIR-073** — Group membership MUST be held against the entry identifier.

**VTI-DIR-074** — A member MAY opt out of any dynamic group, and a VTC MUST
honour the opt-out for as long as the member keeps it: the member MUST NOT be a
member of the group whatever its rule computes, their group membership
credential MUST NOT be renewed (VTI-DIR-085), and the group's room bindings MUST
treat them as having left it (VTI-DIR-092). A community MUST NOT exempt a
dynamic group from opt-out.

*Rationale for VTI-DIR-070.* Knowing that a group exists and knowing who is in
it are different disclosures, and communities need them apart: a support group
whose existence is common knowledge and whose members are known only to its
moderators is the ordinary case, not a corner case.

*Rationale for VTI-DIR-072.* A rule over self-asserted values is a door any
member opens by editing their own card. The rule is only as trustworthy as the
least-verified fact it reads.

*Rationale for VTI-DIR-074.* A static group is joined, or added to by someone
who decided to; a dynamic group gathers members by a rule nobody applied to
them in particular, and its membership is evidenced to rooms, peers and other
communities by a credential in the member's name. A member who never chose to
be counted in a group must be able to stop being counted. No dynamic group
needs an exemption, because none carries authority (VTI-DIR-103): opting out
can cost a member access to a room, never a duty the community relies on them
to hold.

### Group identity and credentials

A group has an identity of its own, so that its membership can be evidenced to
parties outside the community — a room's host, another member, another
community — without the community attesting each membership in its own name.
The community exercises the group's identity as it exercises a room's it owns:
through its VTA, which holds the keys.

**VTI-DIR-080** — Every community group MUST have an identifier of its own,
created by the VTA the community is provisioned on. The VTC MUST NOT hold the
group's keys, and MUST obtain every artefact signed under the group's
identifier by a signing request to that VTA, as VTI-KEY-111 requires for the
community's own attestation keys. The key that signs the group's credentials
MUST be held with the custody VTI-KEY-110 requires of an `attestation` key.

**VTI-DIR-081** — A VTC MUST NOT record a group before its identifier exists.

**VTI-DIR-082** — A group's identifier document MUST NOT carry the group's name,
its description, or anything else that identifies its purpose or its members,
and MUST NOT advertise a transport at which the community does not serve the
group's identifier (VTI-KEY-050).

**VTI-DIR-083** — The community MUST authorize a group's identifier to issue
membership credentials by an authorization credential, issued by the community
to the group's identifier, which is an attestation artefact of the community
(VTI-KEY-080). The community MUST revoke it when the group is deleted.

**VTI-DIR-084** — The group's identifier MUST issue a membership credential to
each member when they join the group, naming the member's membership
identifier as subject, and MUST renew it, through the community's VTA, for as
long as the member remains in the group and in the community.

**VTI-DIR-085** — A group membership credential MUST be short-lived, with a
validity period the community states, and MUST NOT carry a status mechanism or
be entered in a status set of the group's own. Its expiry is its revocation:
the member leaving the group, the member departing the community, and the
group being deleted MUST each stop its renewal, and deleting the group MUST
also revoke the community's authorization credential to the group's identifier
(VTI-DIR-083). The validity period bounds how long a removal takes to reach a verifier,
and MUST be short enough that the credential needs no status under
VTI-CRD-010.

*Note.* A validity of thirty days, renewed when ten remain, is a reasonable
default. A group whose membership is sensitive, or whose removals must reach
verifiers quickly, suits a shorter one.

**VTI-DIR-086** — A verifier relying on a group membership credential MUST
verify it and its validity period, the community's authorization credential to
the group's identifier, and that credential's status, and MUST refuse where any
of them fails. A verifier
MUST NOT rely on a group membership credential as evidence of membership of the
community without that chain.

**VTI-DIR-087** — A VTC MUST NOT consult a group membership credential, or a
group's record, to decide whether a subject is authorized (VTI-VTC-020,
VTI-ACL-070). A group membership credential is evidence for parties outside the
community's access control: a room's host, a peer, another community. Another
community that relies on one is bound by VTI-REG-023.

*Rationale for VTI-DIR-080.* The keys that make a group's credentials are
attestation keys in all but name: whoever holds them can say who is in the
group to everyone who trusts the group. Holding them where the community's own
attestation keys are held, and nowhere else, keeps a compromised community
process able to ask for a group credential and unable to mint one.

*Rationale for VTI-DIR-081.* A group recorded before its identifier exists is a
group with no way to evidence its membership, and a failure between the two
steps leaves a record that the rest of the system takes for a working group.
Creating the identity first means a failure leaves, at worst, an unused
identifier.

*Rationale for VTI-DIR-082.* An identifier document is public and permanent:
every version of it can be read by anyone, for as long as the identifier
exists. A group's name in that document publishes, to the world, a fact the
community may have shown only to the group's own members — and a name like
"Survivors' support" publishes something about every holder of its credential.

*Rationale for VTI-DIR-085.* A status list is fetched by whoever checks a
credential on it, and fetching the list of a five-member group tells the list's
host that one of five people is being checked: a status set per group is the
set per cohort VTI-CRD-014 forbids, and for a small group the crowd is too
small to hide in. A short-lived credential needs no status query at all, so the
check identifies nobody. The cost is that a removal reaches a verifier when the
last credential expires rather than at once, which is why the validity period
is short and stated, and why the community's own authorization credential —
one per group, not per member — keeps a status a verifier can check when the
whole group goes.

*Rationale for VTI-DIR-087.* A credential in a member's hands is portable and
long-lived; the community's access control entry is neither. Accepting the
first as authority at the community would let it outlive a removal from the
group until it expired, and would make the group record a second
authority model by the back door. The credential is for parties who cannot
read the community's access control, which is precisely who has no other
evidence.

### Groups and rooms

**VTI-DIR-090** — Binding a group to a community room MUST require the
capability to manage the community's rooms over that room and the capability to
manage the group. A binding that gives its members authority over the room's
membership or its keys is authority-conferring, and VTI-DIR-100 through
VTI-DIR-104 apply to it.

**VTI-DIR-091** — A member joining a group bound to a room MUST be offered
admission to the room, and MUST be admitted only on accepting it with key
material of their own. A VTC MUST NOT place a member in an encrypted room by
any other means.

**VTI-DIR-092** — A room membership MUST record each source that admits it —
direct admission, or each group binding. A member leaving a group, or the
community, MUST be removed from a room when, and only when, no source still
admits them.

**VTI-DIR-093** — A VTC MUST NOT record a room admission or removal that a
binding causes until the community's agent, as the room's key holder, has made
it.

*Rationale for VTI-DIR-091.* Adding someone to an encrypted room without their
own key means some other party holds a key for them, and that party can read
the room as them. The offer is what keeps the member's own agent the only
holder of the member's place in the room.

*Rationale for VTI-DIR-092.* A member in a room through two groups who leaves
one is still entitled through the other, and a removal on the first departure
locks out someone the community admitted twice. Counting sources is the same
rule that governs capabilities a group provisions (VTI-DIR-101).

### Groups and authority

**VTI-DIR-100** — A group MUST NOT be consulted to decide authority. Authority a
group carries MUST be written onto each member's access control entry, as
capabilities that record the group as their source, when the member joins, and
removed when they leave, so that every authorization decision reads the entry
alone (VTI-VTC-020).

**VTI-DIR-101** — A capability a group provisions MUST be removed only when no
source still grants it — neither the entry's own grant nor another group's
binding.

**VTI-DIR-102** — A capability a group provisions MUST be bounded by the
ceiling of the member's administrative role and by what the subject who bound
the group's grants holds (VTI-ACL-071). That subject MUST be recorded as the
grant's delegator (VTI-ACL-072). When that subject loses the authority, the
capabilities provisioned from the binding MUST be withdrawn unless another
holder of it re-affirms the binding under their own authority.

**VTI-DIR-103** — A group carrying authority MUST have static membership that
only its managers change; members MUST NOT join it by their own act. A dynamic
group MUST NOT carry authority.

**VTI-DIR-104** — Binding an authority-conferring capability to a group is
granting it (VTI-APV-018). Adding a member to a group carrying an
authority-conferring capability is granting that capability to the member
(VTI-APV-018), and removing a member from it, or the member leaving it, is
removing it (VTI-APV-019).

**VTI-DIR-105** — A group MUST NOT set an entry's community role or its
administrative role. A group confers capabilities within an entry's ceiling,
never the ceiling.

*Rationale for VTI-DIR-100.* Groups that answer authorization questions are a
second authority model, and two models disagree in the cases nobody wrote down:
an entry suspended while its group membership stands, a group deleted while a
grant made through it is in use. Provisioning keeps one model and makes the
group a way of writing it. The source recorded on each grant answers the
question auditors ask of provisioned authority, which is why a person holds it.

*Rationale for VTI-DIR-103.* A dynamic rule changes membership without anyone
deciding that a particular person should hold anything: a rule edited, or a
fact it reads changed, hands authority to whoever now matches. Authority goes
to people someone chose, and a group that confers it has members someone
chose.

*Rationale for VTI-DIR-104.* If adding a member to an authority-carrying group
were not a grant, the group would be a way round every consent requirement on
grants: bind once with consent, then add members freely. The group gives a
community a convenient way to grant, and no way to grant more cheaply.

*Rationale for VTI-DIR-105.* An entry holds one community role. Letting groups
set it would need a rule for which group wins, and a member's role would then
depend on the order of group changes. The administrative role is a ceiling, and
a ceiling a group could raise would be no ceiling.

### Enumeration

**VTI-DIR-110** — A withheld value MUST be absent from a read, not present with
no value. A read of an entry of which nothing is visible to the viewer MUST be
indistinguishable from a read of an entry that does not exist (VTI-OPS-035).

**VTI-DIR-111** — A directory read MUST NOT disclose to members an entry whose
subject is applying, suspended or departed (VTI-MEM-001).

**VTI-DIR-112** — Browsing the directory MUST return only `listed` entries. A
paginated directory listing MUST bind its cursor to the viewer, the query and
the filters it was minted under, and a VTC MUST refuse to resume a cursor under
any other.

**VTI-DIR-113** — A VTC MUST offer a field for filtering, and count matches by
it, only where the field's values are visible to the viewer. A count smaller
than a threshold the community states MUST NOT be disclosed exactly.

**VTI-DIR-114** — A VTC SHOULD limit the rate at which one viewer may browse,
search and request introductions.

**VTI-DIR-115** — Only an administrator of the directory MAY export it. An
export MUST require re-authentication bound to it, and MUST be audited at the
node's highest severity. A member MUST NOT be able to obtain the directory in
bulk.

*Rationale for VTI-DIR-112.* A cursor resumed under a different query lets a
caller read the results of one question while having passed the checks for
another, and a sweep stitched from such resumptions enumerates entries no
single query would return. VTI-ACL-066 binds the direction of an access control
listing for the same reason; a directory listing has more to bind.

*Rationale for VTI-DIR-113.* A filter that answers "one member in this town
works here" has disclosed that member's town and employer to anyone who can
read the count, whatever the member chose to show. Small counts are where
aggregate answers become individual ones.

### Departure and rotation

**VTI-DIR-120** — A member's departure MUST, at once, withdraw any external
publication of their card, remove their entry from every directory read, and
remove them from every group, with the effects on rooms, capabilities and group
credentials that removal has (VTI-DIR-085, VTI-DIR-092, VTI-DIR-101).
Withdrawing external publication MUST precede the rest.

**VTI-DIR-121** — The departure disposition (VTI-MEM-031, VTI-MEM-032) MUST
apply to the member's card, their group memberships and administrators' notes
about them.

**VTI-DIR-122** — Rotation of a member's identifier (VTI-MEM-041) MUST carry
their card, group memberships, notes about them and the capabilities groups
provisioned for them to the new identifier, without changing their entry
identifier. Group membership credentials naming the previous identifier MUST
no longer be renewed, and MUST be issued to the new one.

*Rationale for VTI-DIR-120.* What the community published about a member
outside its walls is the part it cannot recall by deleting a record, and every
moment it stays offered is a moment someone may copy it. Withdrawing it first
bounds that window by the time of the departure, not the time of the
community's cleanup.

### The disclosure statement

**VTI-DIR-130** — A VTC offering a directory MUST publish a statement of what
its directory discloses, and to whom: each field and its ceiling; the default
visibility and listing of a new card; whether membership identifiers can reach
members or the public; whether administrators keep notes about members; that
group membership is evidenced by credentials a group's members hold; and
whether cards can be published beyond the community. The VTC MUST generate the
statement from its configuration, MUST regenerate it at every change to that
configuration, and MUST show it to applicants before they apply.

*Rationale for VTI-DIR-130.* A community that issues membership credentials
states, under [DTG-CRED], whether and to whom it discloses its members'
identifiers, and a directory is the largest part of that answer. A statement
written by hand describes the directory someone intended; one generated from
configuration describes the directory that runs, and cannot fall behind a
change to it.
