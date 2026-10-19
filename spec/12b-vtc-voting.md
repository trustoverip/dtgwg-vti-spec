## Community Voting

This section is normative.

### In plain terms

A club holds a vote. The secretary keeps the list of members and hands each one
a ballot paper; a separate box, kept by someone else, collects the papers; and
anyone the club allows may watch the count.

What makes the vote private is that the secretary and the box never compare
notes. The secretary knows who took a paper but never sees one filled in. The
box sees every filled-in paper but never learns whose it was. Each paper carries
a stamp that proves the club issued it, and a mark that is the same every time
the same member votes in the same election, so a second paper from one member
replaces the first instead of counting twice — but the mark says nothing about
who the member is, and a member's marks in two elections look unrelated.

What makes the vote trustworthy is that the box keeps every paper on a board
that can only grow, signs what the board held at each moment, and lets anyone
allowed to read it count again. A member holds a receipt for their own paper and
can check it is on the board. Nobody has to take the box's word for the result.

Some votes need more than this: the club may not want anyone to read the papers
before the count, or ever. Then the papers are sealed so that only several
trustees acting together can open them — and for the strictest votes, only the
totals are ever opened.

### What this chapter defines

This chapter specifies how a community runs polls, surveys and elections among
its members: who may vote, how a member proves it without saying who they are,
where ballots go, how they are counted, and how anyone permitted can check the
count.

A VTC is not required to offer voting. A VTC that offers it MUST satisfy the
requirements of this chapter; a VTC that does not is not bound by them.

The chapter adds no authority model. A vote decides what its manifest says it
decides and nothing more: a result never grants, widens or removes authority by
itself, and any change the community makes because of a result is made through
the model of the Access Control and Authority and the Approvals, Consent and
Step-Up chapters (VTI-VTC-020), like any other change.

Five parties take part, and the chapter keeps what each learns apart, as
[BELENIOS] separates its credential authority, voting server and trustees:

| Party | Played by | Learns | Never learns |
|---|---|---|---|
| **Registrar** | the community's VTC | who asked for a ticket, and when | any ballot, any tag, the address or time of any cast |
| **Ballot box** | a voting service, which is a host service (Architecture chapter) | ballots, tags, the address and time of each cast | which member any tag belongs to |
| **Voter's agent** | the voter's VTA | the voter's tag and ballot | other voters' tags and ballots |
| **Trustees** | parties named for one election, each through its own VTA | a share of the election key; at close, what decryption reveals | who cast which ballot |
| **Auditors** | parties the community names, or anyone where the board is public | the board, the registration and the issuance record | who cast which ballot |

The capability this chapter refers to is registered in Appendix C.3:
`vtc.elections.manage`, the capability to create, close, cancel and certify
the community's elections.

Throughout this chapter, an **election** is one poll, survey or election, with
one manifest, one roll and one board. A **ticket** is a credential that admits
its holder to vote in one election and identifies nobody. A **cast** is a ballot
together with the proof that admits it. A **tag** is the value a cast carries
that is the same for every cast made with one ticket and unrelated to any other.
The **board** is the election's append-only record of what the ballot box
admitted.

### Separation of the registrar and the ballot box

**VTI-VOT-001** — A VTC MUST NOT receive, store or relay a ballot or a cast, and
MUST NOT act as the ballot box of its own elections.

**VTI-VOT-002** — A ballot box MUST NOT be given, and MUST NOT ask for, the
identity of any voter: no membership identifier, roster or issuance record of an
election it serves.

**VTI-VOT-003** — A cast MUST NOT be sent from, signed by or otherwise carry the
voter's membership identifier or any other durable identifier of the voter
(VTI-KEY-007). It MUST be signed by a key generated for that cast alone.

**VTI-VOT-004** — A ballot box MUST NOT refuse a cast because of its transport.
It MUST accept a cast over a binding whose sender holds no persistent identifier
and no session, and MAY accept one over any other binding it serves.

*Rationale for VTI-VOT-001 – 003.* Linking a voter to a ballot needs two facts:
who held a ticket, and which ballot came from where and when. The registrar
holds the first and the ballot box the second. Keeping them in different hands
means that linking a voter needs those two parties to collude, and that either
one alone learns nothing about how anyone voted. A ballot that passes through
the VTC, or a cast sent from the voter's own identifier, joins the two facts in
one place and undoes the separation whatever cryptography follows.

*Rationale for VTI-VOT-004.* A voter casting from a key made for one cast has no
mediator session to receive on and no relationship with the ballot box. The
preference for end-to-end messaging (Transports chapter) assumes both parties
hold identifiers; here only one does, by design, and the receipt is the only
answer the voter needs.

### Elections and their manifests

An election's **manifest** states what is asked and how it is decided. Its
**digest** is the SHA-256 of its canonical form.

**VTI-VOT-010** — A manifest MUST state: the election's identifier, unique
within the community; its questions, each with a type (a choice between options
with a minimum and a maximum number of selections, a ranking, a score within
stated bounds, or text of bounded length) and whether it may be left unanswered;
its secrecy level (VTI-VOT-060); its roll rule (VTI-VOT-021); the times
registration opens, voting opens and voting closes; whether a later ballot under
one ticket replaces an earlier one or is refused; the board's visibility
(VTI-VOT-052); the ballot box; and, where the secrecy level needs them, the
trustees and the threshold of them that decryption requires.

**VTI-VOT-011** — Every cast MUST be bound to the manifest's digest, so that a
cast made against any other manifest does not verify in the election. A VTC MUST
show a member the manifest whose digest it registered.

**VTI-VOT-012** — An election MUST be registered with its ballot box by a
document signed by the community, carrying the manifest's digest, the
community's voting verification key and the election's ticket class. A ballot
box MUST refuse a registration not signed by the community it names.

**VTI-VOT-013** — Once registered, an election's manifest MUST NOT change. The
community MAY close voting early where the manifest allows it, or cancel the
election before it closes; a ballot box MUST record either on the board and MUST
refuse every other change to a registered election.

**VTI-VOT-014** — A manifest MAY hold its question text outside the ballot box,
registering only option counts and question types with it. A board that is not
public SHOULD do so.

*Rationale for VTI-VOT-011.* A ticket proves that its holder may vote; it does
not prove what they were asked. Binding the cast to the digest means a community
that shows different questions to different members produces casts that do not
count, rather than results nobody can interpret.

### Tickets

A community's **voting key** is the key it signs tickets with. A ticket is a
class credential of a predicate credential system [PCS] issued under the class
of one election.

**VTI-VOT-020** — A VTC MUST issue tickets under a voting key used for no other
purpose, and under a class that names one election and no other. A ticket MUST
be issued blind: the VTC MUST NOT learn the ticket it issues, or anything that
would let it recognise a cast made with it.

**VTI-VOT-021** — A VTC MUST decide eligibility for a ticket by the election's
roll rule — its active members, its active members and parties the manifest
lists, or only parties the manifest lists — read from its own records at the
time it issues. It MUST NOT issue a ticket to a member who is not active at that
time.

**VTI-VOT-022** — A VTC MUST issue at most one ticket for an election to each
eligible party, and MUST record each issuance durably, before answering, against
the requesting party, under a request that party signed with its membership
identifier (or, for a listed non-member, the identifier the manifest lists).

**VTI-VOT-023** — A voter's agent SHOULD generate the key a ticket is requested
under for that election alone, and MUST derive it as it derives any other key of
the voter's context (VTI-KEY-030).

**VTI-VOT-024** — A VTC MUST NOT issue a ticket for an election after its voting
closes, or for an election that has been cancelled.

*Rationale for VTI-VOT-020.* A ticket under a class that names one election is
what makes the roll real: a member who left before the roll closed has no ticket,
because no class credential issued for an earlier period stands for this
election. A key used for nothing else means a credential issued for any other
purpose, such as hidden vetting, can never be presented as a ticket.

*Rationale for VTI-VOT-023.* A tag is derived from the voter's key and the
election. A key used across elections leaves every tag of the voter recoverable
together should that key be exposed, including by a future adversary able to
compute discrete logarithms; a key per election limits that to one election.

### Casting

**VTI-VOT-030** — A cast MUST carry the ballot and a proof, made with the
voter's ticket, that the holder holds a ticket of the election's class. The
proof MUST bind the election's identifier, the community, the manifest digest
and the ballot's exact bytes, so that it verifies for no other ballot and no
other election.

**VTI-VOT-031** — The tag of a cast MUST be a function of the ticket's key and
the election alone, so that every cast made with one ticket in one election
carries the same tag, and casts made with different tickets, or in different
elections, carry tags that cannot be linked to each other or to the voter.

**VTI-VOT-032** — A voter's agent MUST validate the ballot against the manifest
before casting, and MUST NOT cast a ballot the manifest does not admit.

**VTI-VOT-033** — A voter's agent SHOULD NOT cast in the same exchange in which
it obtained the ticket, and SHOULD let a random interval pass between the two.

*Rationale for VTI-VOT-033.* The registrar sees when a ticket was requested and
the ballot box sees when a cast arrived. A cast that follows its ticket within
seconds can be paired by time alone if the two operators collude.

### The ballot box

**VTI-VOT-040** — A ballot box MUST admit a cast only if its proof verifies
under the community's registered voting verification key, for the registered
election and its ticket class, with the cast's ballot bound into it, and only
while voting is open.

**VTI-VOT-041** — A ballot box MUST count at most one ballot per tag. Where the
manifest allows a later ballot to replace an earlier one, the latest admitted
ballot under a tag MUST be the one counted; otherwise a later, different ballot
under a tag MUST be refused.

**VTI-VOT-042** — A ballot box MUST answer a cast it has already admitted, sent
again unchanged, with the receipt it first issued, and MUST NOT count it again.

**VTI-VOT-043** — A ballot box MUST answer every admitted cast with a receipt it
signs, naming the election, the cast's position on the board and a board head
that includes it.

**VTI-VOT-044** — A ballot box MUST be able to admit a cast using only the
registration and its own record of tags. It MUST NOT consult the community, or
any record of who holds tickets, to admit one.

**VTI-VOT-045** — A ballot box SHOULD reject a cast at the cheapest check that
fails — the election, the ballot's form, the ticket class, the tag — before it
verifies the proof.

### The board

**VTI-VOT-050** — A ballot box MUST keep, for each election, a board: an
append-only log of every admitted cast, every replacement, every trustee share
and the result, in the form of a Merkle tree log [RFC 9162]. It MUST sign each
head it publishes, and MUST be able to prove the inclusion of any entry in a
head, and the consistency of any two heads.

**VTI-VOT-051** — A VTC MUST obtain and keep a signed head of each of its
elections' boards at least when voting opens, when it closes and when the result
is published, and MUST verify each against the one before. A VTC MAY have its
witnesses sign the same heads.

**VTI-VOT-052** — A board MUST be either public, readable by anyone, or private,
readable by the community, by the auditors the manifest names and, for the
inclusion of one cast, by the holder of that cast's receipt. A ballot box MUST
NOT give any other party a private board or any part of it.

**VTI-VOT-053** — A voter's agent SHOULD verify that the receipt's entry is
included in the head the VTC kept at close, and MUST tell the voter if it is not.

*Rationale for VTI-VOT-051.* A board is only append-only if someone holds an
earlier head to compare against. The community, which already holds signed
checkpoints of its own audit log, is the party with the most interest in the
board staying intact and the least in learning what is on it.

### Secrecy levels and trustees

**VTI-VOT-060** — An election MUST have one of three secrecy levels: **open**,
whose ballots are recorded on the board as cast; **sealed**, whose ballots are
encrypted to an election key and decrypted after voting closes; or **tallied
encrypted**, whose ballots are encrypted and of which only the totals are ever
decrypted.

**VTI-VOT-061** — The ballot box of an open election MUST NOT disclose any
result before voting closes unless the manifest allows it.

**VTI-VOT-062** — The election key of a sealed or tallied-encrypted election
MUST be generated jointly by its trustees so that no fewer than the stated
threshold of them can decrypt. The VTC and the ballot box MUST NOT be trustees,
and a trustee MUST NOT hold enough shares to decrypt alone.

**VTI-VOT-063** — In a sealed or tallied-encrypted election, every decryption
MUST be published on the board with a proof that it is correct, and a
decryption MUST NOT be made before voting closes.

**VTI-VOT-064** — In a tallied-encrypted election, every ballot MUST carry
proofs that it encrypts a ballot the manifest admits, and an individual ballot
MUST NOT be decrypted. A manifest whose questions cannot be totalled — rankings and
text — MUST NOT be given this level.

**VTI-VOT-065** — Answers to text questions MUST NOT be disclosed until the
number of counted ballots reaches the threshold the manifest states, which MUST
be at least 10.

*Rationale for VTI-VOT-060.* The ticket keeps a ballot anonymous whatever the
level, so an open or sealed ballot can be published whole, and any question
type can be asked. Encryption at the sealed level hides results until close.
The tallied-encrypted level adds a protection that does not depend on
anonymity: if a ballot were ever linked to its voter — by network address and
time, say — its content was still never decrypted.

### Results and audit

**VTI-VOT-070** — The result of an election MUST be computable by anyone who can
read its board, from the board and the registration alone.

**VTI-VOT-071** — A VTC MUST NOT certify a result it has not itself verified
from the board: every cast's proof, every tag counted once, every decryption's
proof, and the result recomputed. It MUST NOT certify a result whose board head
is inconsistent with a head it kept.

**VTI-VOT-072** — A VTC MUST NOT certify a result whose number of counted
ballots exceeds the number of tickets it recorded as issued for the election,
and MUST give the issuance record of an election to the auditors its manifest
names.

**VTI-VOT-073** — An auditor's finding SHOULD be a verifiable credential whose
subject is the election and the board head audited, stating each check made and
its outcome. A VTC MUST publish with a certified result every such credential
it received.

### Governance

**VTI-VOT-080** — Creating, closing early, cancelling and certifying an election
MUST require `vtc.elections.manage`.

**VTI-VOT-081** — Creating an election whose manifest marks it binding on the
community SHOULD require consent from a party other than the requester.
VTI-APV-022 governs a node that does not apply it.

**VTI-VOT-082** — The list of ballot boxes a community may register elections
with is configuration that alters trust semantics (VTI-CMP-090), and a change to
it SHOULD require consent from an administrator other than the requester.

### Disclosure

**VTI-VOT-090** — A VTC offering voting MUST publish, and show a member before
they request a ticket for an election, a statement of: who operates the ballot
box; whether the same party operates the VTC; the trustees and their threshold;
the auditors and the board's visibility; whether a later ballot replaces an
earlier one; and that a member can prove how they voted, so that the community
cannot protect a member against being made to vote a certain way.

### Audit

**VTI-VOT-100** — A VTC MUST record an audit event for the creation, early
closure, cancellation and certification of an election and for each ticket it
issues. An audit record of a VTC or a ballot box MUST NOT pair a voter's
identifier with a tag, a cast or a ballot.

*Open issue.* This chapter does not provide resistance to coercion or vote
buying: a voter can prove how they voted by revealing their key's tag, and a
later ballot replacing an earlier one mitigates this only partly. Ranked
questions in a tallied-encrypted election would need a verifiable shuffle before
decryption, which this chapter does not yet specify.
