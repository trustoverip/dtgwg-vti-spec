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
| **Registrar** | the community's VTC | who asked for a ticket, and when; under a credential roll, that they hold the membership credential it names | any ballot, any tag, the address or time of any cast |
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

**VTI-VOT-005** — A ballot box MUST advertise in its DID document every binding it
serves, and MUST serve at least one end-to-end messaging binding (Transports
chapter) besides the binding VTI-VOT-004 requires. A community MUST send its
registration, close and cancellation, and SHOULD send its reads, over the
highest-preference messaging binding both parties advertise. Where a messaging
binding proves the sender, a ballot box MUST refuse a document whose `issuer` is
not that sender.

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
answer the voter needs. A messaging binding could carry a cast only if the
one-time key opened a mediator session of its own, which would give the
mediator an account for it, a relationship record, and the voter's network
address and timing.

*Rationale for VTI-VOT-005.* Everything other than a cast is sent by a party
that holds an identifier — the community, an auditor — and so takes the
preference for end-to-end messaging like any other exchange. A registration is
authority over an election, so it travels on a binding that proves its sender as
well as carrying its own proof.

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
roll rule — its active members; its active members and parties the manifest
lists; only parties the manifest lists; or the holders of a membership
credential for the one identifier the manifest names (a **credential roll**,
VTI-VOT-025) — at the time it issues: from its own records under the first
three rules, and from the presentation the requester makes, checked as
VTI-VOT-025 requires, under the fourth. It MUST NOT issue a ticket to a member
of the community who is not active at that time.

**VTI-VOT-022** — A VTC MUST issue at most one ticket for an election to each
eligible party, and MUST record each issuance durably, before answering, against
the requesting party, under a request that party signed with its membership
identifier (or, for a listed non-member, the identifier the manifest lists; or,
under a credential roll, the identifier VTI-VOT-027 holds the issuance against).
Where a member's identifier is rotated (VTI-MEM-041), the VTC MUST carry their
issuance records to the new identifier, so that rotation never yields a second
ticket for an election.

**VTI-VOT-023** — A voter's agent SHOULD generate the key a ticket is requested
under for that election alone, and MUST derive it as it derives any other key of
the voter's context (VTI-KEY-030).

**VTI-VOT-024** — A VTC MUST NOT issue a ticket for an election after its voting
closes, or for an election that has been cancelled.

**VTI-VOT-025** — Under a credential roll, a VTC MUST establish a party's
eligibility when the party requests its ticket, and MUST NOT establish it, or
fix the roll, when the election is created. The request MUST carry a
presentation of the requester's membership credential for the identifier the
roll names, made by the credential's subject and satisfying VTI-CRD-020 to
VTI-CRD-022 with the community as its audience. The VTC MUST issue a ticket only
where, at the time it issues:

1. the credential's proof verifies and its issuer is the identifier the roll
   names, and, where that identifier is one of the community's groups, the
   community's authorization credential to the group also verifies
   (VTI-DIR-083, VTI-DIR-086);
2. the credential is within its validity period;
3. the party that signed the request is the credential's subject;
4. neither the credential, nor any authorization credential its issuer's
   authority rests on, is revoked or suspended, as its status — its status
   list, or the trust registry its issuer publishes status through — shows at
   that time (VTI-CRD-011, VTI-MEM-030); a credential that carries no status
   mechanism is checked by its validity period alone (VTI-DIR-085);
5. where the roll names the community or one of its groups, the subject is an
   active member of the community, and of the group, in the community's own
   records; and
6. where the roll names any other identifier, that identifier's own membership
   of the community (VTI-VOT-026) is active — neither expired, suspended,
   revoked nor removed — in the community's own records and as the status of
   the membership credential the community issued it shows at that time.

Where any of these cannot be determined — a status list or a trust registry
that cannot be reached — the outcome MUST be indeterminate (VTI-CRD-012,
VTI-REG-012), and the VTC MUST NOT issue the ticket. The VTC MUST NOT retain
the presentation once it has decided, and MUST record with the issuance only
what VTI-VOT-022 and VTI-VOT-027 need (VTI-CRD-042).

**VTI-VOT-026** — A VTC MUST NOT create an election under a credential roll
unless the identifier the roll names is the community's own, the identifier of
one of its groups, or the identifier of a party that is itself an active member
of the community — holding a membership credential the community issued it,
neither expired, suspended nor revoked, as the community's records show at
creation — such as another community, a group of another community, or a
network. It MUST refuse to create one naming any other identifier, and MUST NOT
treat recognition of a party through a trust registry (VTI-REG-020) as
membership for this purpose. The roll MUST name exactly one identifier. A roll
that names a party admits the holders of that party's own membership
credentials, never the members of the parties it lists or recognises in turn
(VTI-REG-024, VTI-VTN-022).

**VTI-VOT-027** — Under a credential roll, the party VTI-VOT-022 holds to one
ticket is the subject of the credential presented, never the identifier the
roll names. Where the roll names the community or one of its groups, the VTC
MUST hold the issuance against the member the subject identifies in its
records, so that it follows the member through rotation (VTI-VOT-022). Where
the roll names any other party, the VTC MUST hold it against the subject's
identifier.

**VTI-VOT-028** — Under a credential roll, the number of parties the roll
admits is not known to the community, and a VTC MUST NOT state one. Where it
reports turnout, it MUST report it as the number of counted ballots against the
number of tickets it issued (VTI-VOT-072).

*Rationale for VTI-VOT-020.* A ticket under a class that names one election is
what makes the roll real: a member who left before the roll closed has no ticket,
because no class credential issued for an earlier period stands for this
election. A key used for nothing else means a credential issued for any other
purpose, such as hidden vetting, can never be presented as a ticket.

*Rationale for VTI-VOT-023.* A tag is derived from the voter's key and the
election. A key used across elections leaves every tag of the voter recoverable
together should that key be exposed, including by a future adversary able to
compute discrete logarithms; a key per election limits that to one election.

*Rationale for VTI-VOT-025.* A credential roll lets a community put a question
to a set of people whose list it does not keep — the members of one of its
groups, or of a community, group or network that is itself its member — by
asking each of them, when they
come for a ticket, to show the credential that makes them one. Checking it then,
and not when the election is called, is what keeps the roll honest in both
directions: a person who joins after the election is called holds a credential
when they ask and is admitted, and a person removed in the meantime has a
credential that is revoked or expired and is not. A roll fixed at creation
would have to be a copy of another party's roster, which the community has no
right to and which would be stale by the first ballot. The check is made in the
one exchange in which the community already learns who is asking, so it
learns nothing else about the voter, and the ticket stays blind
(VTI-VOT-020). Where the roll names the community or one of its groups, the
community's own records are the fresher evidence, and a credential that has
not yet expired does not outvote them.

*Rationale for VTI-VOT-026.* A credential roll hands the decision of who may
vote to whoever issues the credentials. Naming any identifier would let
whoever controls it enfranchise anyone they like, and the administrator who
called the election would not see it happen. A party from outside the community
is therefore trusted for this only by being a member of it: admitted by the
community's own decision (VTI-MEM-011), evidenced by a credential the community
issued, and removable by the community's own lifecycle. Recognition through a
registry is a weaker, one-sided relationship and is not enough. The community
itself and its own groups need no such membership, because their
membership credentials already come from the community. Checking that
membership again at each issuance means a party whose membership lapses or is
revoked during an election stops yielding tickets, as a revoked credential
does.

*Rationale for VTI-VOT-027.* The identifier the roll names is shared by
everyone the roll admits; one ticket per roll identifier would be one ticket
for the whole roll. What one ticket per party must count is the person
holding the credential, and the subject is the only identifier of them the
presentation proves.

*Rationale for VTI-VOT-028.* A community cannot count the members of a party
whose roster it does not hold, and an estimate presented as a roll size would
make turnout look like a measurement. Tickets issued is a number the community
does hold, is the bound certification already holds the count to, and says
what turnout can honestly say: how many of those who came for a ticket voted.

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
be at least 1 and SHOULD be at least 10. Where the threshold is below 10, the
statement a member is shown before they request a ticket (VTI-VOT-090) MUST say
so, and MUST say that a written answer may identify its author.

*Rationale for VTI-VOT-065.* A written answer can identify its author by what it
says or how it is phrased, and the fewer the ballots released together, the
easier that is. Small communities and surveys still need written answers, so a
low threshold is disclosed to the member rather than forbidden.

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
earlier one; where the threshold for disclosing text answers is below 10, that
it is and that a written answer may identify its author (VTI-VOT-065); under a
credential roll, the identifier the roll names, whether it is the community, one
of its groups, or another party that is a member of the community, and that
claiming a ticket shows the community the member's membership of it
(VTI-VOT-025); and that a member can prove how they voted, so that the community
cannot protect a member against being made to vote a certain way.

### Audit

**VTI-VOT-100** — A VTC MUST record an audit event for the creation, early
closure, cancellation and certification of an election and for each ticket it
issues. An audit record of a VTC or a ballot box MUST NOT pair a voter's
identifier with a tag, a cast or a ballot.

*Open issue.* Under a credential roll that names a party other than the
community, the registrar learns, from the presentation, that the
requester is a member of that party — a fact it may not otherwise hold, about
a person who may not be its member at all. A presentation of a predicate
credential system proof that the requester holds such a credential, under a
pseudonym scoped to the election, would let the registrar enforce the roll and
one ticket per party without learning which member asked. This chapter does not
yet specify it.

*Open issue.* Where a credential roll names a party other than the community or
one of its groups, the registrar cannot follow a rotation of the subject's identifier at that party
(VTI-VOT-027): a member who rotates there during an election, and presents the
credential issued to their new identifier, is a new subject to the registrar.
The count is still bounded by the tickets issued (VTI-VOT-072), but one ticket
per party then rests on the issuing party's credentials carrying an identifier
that survives rotation, which this specification does not yet require.

*Open issue.* This chapter does not provide resistance to coercion or vote
buying: a voter can prove how they voted by revealing their key's tag, and a
later ballot replacing an earlier one mitigates this only partly. Ranked
questions in a tallied-encrypted election would need a verifiable shuffle before
decryption, which this chapter does not yet specify.
