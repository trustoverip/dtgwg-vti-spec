## Notifications

This section is normative.

### In plain terms

A club decides to hold a vote that its partner clubs' members may take part
in. It does not know who those members are, and it has no right to their
names. So it writes to each partner club: *there is a vote here, number 12,
and your members may take part.* Each partner passes the note on to its own
members however it likes — a newsletter, a noticeboard, a message to each.
The note a member finally reads is still the first club's note, with the first
club's signature on it, and a line from the partner saying *I passed this on.*

The note is not a ballot paper and not a key. It says where something is and
who may come; whether a particular member may actually take part is decided
when they turn up, by the club holding the vote. And it says nothing more than
that: not the questions, not who else was told.

A member's agent keeps the notes it is given and shows them to the member when
asked, so that a member can see, in one place, what they have been told they
may take part in. It does not act on a note by itself. Opening the thing a note
points at is the member's choice, because opening it can tell its holder
something about the member.

Notes addressed to one of the member's own names — the name they joined a club
under — wait at a post office box that only one key-holder may empty at a
time. If the member's own device empties it, the device hands every note to
the agent. If nothing does, the agent empties it itself, so that a member
whose only device is a browser, which cannot hold the box, is not left with a
box that nobody empties. The device and the agent agree, in writing and for a
stated time, which of them holds the box, so that they never both reach for it.
The agent empties every box nobody has claimed unless the member tells it not
to — a choice the member makes separately for each of the spaces the agent
keeps apart for them, and can change at any time. The agent's operator can stop it
emptying any box for a while, but cannot make it empty one the member told it
to leave.

And because anyone can write a note, an agent takes notes only from parties it
has a relationship with: a community it belongs to, a group or network it is a
member of, a party it has chosen to hear from. A note relayed by a partner
club is taken on the partner's standing, not the first club's.

### What this chapter defines

This chapter specifies the [[ref: notification]]: a signed statement by an
**originator**, addressed to one identifier, that a thing exists which the
addressee, or the parties it stands for, may act on or take part in. It defines
what a notification carries and must not carry, how it is delivered to the
identifier it is addressed to, how a party that stands for others — a group, a
community, a network — may relay it to them while preserving the originator's
signature, how a recipient decides whether to accept one, how a member's agent
holds the notifications it accepts, and the notification kind for an election.

A notification may be addressed to an individual, a group, a community or a
network, each by its identifier. What an addressee that stands for others does
with a notification — whether it relays it, to whom, when, and by which means
— is the addressee's decision, and this chapter states only the properties a
relay must have.

The operations are defined by the catalogue specification: one to deliver a
notification, one to relay it; at a VTA, one each to list, read and dismiss the
notifications held for a principal, one by which the principal's client
hands the VTA a notification it received for an identifier whose keys the VTA
holds, one each by which a client claims, releases and lists the
collection of messages for the principal's identifiers, and one each by which
a party reads and changes whether the VTA collects for them in a trust context; and at a VTC, one each by which its administrators list and read the
notifications addressed to the community and decide a relay of one to its
members.

Throughout this chapter, the **thing** is what a notification points at — an
election, an invitation, a document awaiting review — held by a party named in
the notification, its **holder**. The **audience** is the identifier whose
holders or members the thing concerns. A **relayer** is an addressee that
passes a notification on to a party it stands for, and a **relay** is one such
passing-on.

### The notification

**VTI-NTF-001** — A notification MUST be an operation document of the
catalogue's notification operation, issued by its originator and addressed to
exactly one identifier. It MUST carry a proof by the originator
(VTI-OPS-022), whatever transport carries it.

**VTI-NTF-002** — A notification MUST state: an identifier for it, unique among
the notifications its originator issues; its kind (VTI-NTF-010); the thing's
holder, by identifier, the thing's identifier at its holder, and the operation
by which it is opened; the audience, by identifier; its revision; and whether
it may be relayed. It MAY state the period in which the thing can be acted on.

**VTI-NTF-003** — A notification MUST NOT carry more than its kind's definition
permits (VTI-NTF-011), and MUST NOT carry the thing's content — for an
election, its title, questions or options — or any statement of who else was
notified or how many.

**VTI-NTF-004** — A notification confers no authority and is no evidence of
eligibility. A recipient MUST NOT treat one as evidence that it, or anyone, may
take part in the thing, and the thing's holder MUST decide that when the thing
is opened, as if no notification had been sent.

**VTI-NTF-005** — An originator MUST address a notification only to a party the
thing concerns, or to a party that stands for those it concerns, as the
originator's records show at the time it sends. The originator MUST apply this
when it issues each document of the notification — every revision, and every
new document VTI-TRN-046 issues for one — and MUST NOT send a revision that
still offers the thing to a party its records no longer show as concerned. A
withdrawal is not subject to this check: it MUST go to every party an earlier
revision was sent to (VTI-NTF-024). A retransmission of the same document by
the delivery layer is not a new issue.

**VTI-NTF-006** — The proof of a notification, and of every relay, MUST carry
the proof purpose `authentication` and be made with an `operational` key of its
signer (VTI-KEY-081, VTI-KEY-106). A notification is not an attestation
artefact (VTI-KEY-082), and a recipient MUST NOT read its proof, whatever
purpose it names, as evidence of anything but who produced the document and
that it is unaltered.

*Rationale for VTI-NTF-001.* A notification that is relayed reaches parties
that never had a session with its originator, and an authenticated transport
tells each hop only who the previous hop was. The originator's proof is the one
thing that survives every relay, so a final recipient can tell who said there
is an election from who passed the message on.

*Rationale for VTI-NTF-005.* The originator's records change while a
notification is outstanding: a party may leave the community, or stop being
named by the thing. Checking once, when the first revision is sent, would let
every later revision and every re-issued copy go on offering the thing to a
party that is no longer concerned. A withdrawal is the exception because it
offers nothing: it tells a party that what it was already told no longer
holds, and a party left holding an unwithdrawn notification is worse off than
one told it was withdrawn.

*Rationale for VTI-NTF-006.* A notification is a Trust Task request document,
which VTI-KEY-081 puts on the `operational` key, and it asserts no decision of
its originator about anyone's membership, standing or authority, which is the
test VTI-KEY-082 applies before an artefact must be an attestation. What a
recipient, or a relay's recipient that never had a session with the
originator, relies on the proof for is authorship and integrity: who said the
thing exists, and that nobody changed what they said. That is what the
`authentication` relationship authorizes a key to show. Signing with the
`attestation` key instead would put every notification behind the VTA's
non-exportable key (VTI-KEY-110) for no property a recipient checks, and would
invite reading a notification as the attestation VTI-NTF-004 says it is not.

*Rationale for VTI-NTF-003 and VTI-NTF-004.* A notification travels further
than the thing it points at: it may be relayed to every member of a network
before any of them has shown that they may see the thing. What the thing
contains is therefore shown only by its holder, to a reader it admits, when it
is opened. A notification says where to look, and the look is where the
decision is made. Treating a notification as eligibility would let whoever can
send one enfranchise whoever receives it.

### Kinds

**VTI-NTF-010** — Every notification MUST name a kind. A kind is either
registered in the notification kind registry of Appendix G, or named under a
domain its definer controls. Registering a kind MUST NOT require a new version
of the notification or relay operation: the operation fixes only the form of a
kind's name.

**VTI-NTF-011** — A kind's definition MUST state: what the thing is; the
operation by which it is opened; what the audience means for it; what a
notification of the kind may carry beyond the members of VTI-NTF-002, which is
nothing unless the definition says otherwise; whether it may be relayed, and,
where it may, what a relay of it discloses to the parties it is relayed to; and
when it lapses.

**VTI-NTF-012** — A recipient MUST NOT act on a notification of a kind it does
not recognise beyond holding, listing and discarding it, and MUST NOT present
one as anything but what VTI-NTF-002 states of it.

*Rationale.* A closed set of kinds would make every new use a new version of
the notification operation and every recipient a party to every use. A
registry kept in this specification, rather than in the operation's schema,
keeps the operation fixed and the uses open, as the capability registry does
(Appendix C), and the rule of VTI-NTF-012 is what makes an
unknown kind safe: a recipient that does not understand a notification can
still show that it exists, and does nothing else with it.

### Delivery

**VTI-NTF-020** — An originator MUST deliver a notification to the identifier
it is addressed to over a transport that identifier's document advertises,
selected as the Transports, Messaging and Delivery chapter requires, and MUST
treat it as delivered only on the evidence VTI-TRN-040 requires.

**VTI-NTF-021** — The notification and relay operations are of the second
retry class of VTI-OPS-060. The originator's identifier for a notification,
with its revision, is the operation's idempotency key: every attempt at one
delivery, and every new document VTI-TRN-046 issues for it, MUST carry the same
identifier and revision. An originator MUST issue every revision of a
notification as the same identity, and a node MUST issue its notifications as
its durable identity (VTI-KEY-001), whose identifier a key rotation does not
change.

**VTI-NTF-022** — A recipient MUST hold at most one record of a notification
for each pair of originator identifier and notification identifier, comparing
originator identifiers as VTI-KEY-160 – VTI-KEY-169 require for the form they
take. A higher revision
MUST replace the record; a revision equal to or lower than the one held MUST
NOT change it.

**VTI-NTF-023** — A recipient MUST answer a notification or a relay it has
verified with the same reply whether it held it, declined it under its
acceptance policy, or relayed it on, and the reply MUST NOT state which. A
recipient MAY decline to answer a sender with which it holds no relationship
at all (VTI-NTF-030).

**VTI-NTF-024** — An originator that changes what a notification states, or
withdraws it because the thing has been cancelled or is no longer open to the
audience, MUST do so by issuing a higher revision under the same identifier to
the same addressee. It SHOULD withdraw a notification whose thing is cancelled
before it lapses, and MUST where the thing's kind requires it.

**VTI-NTF-025** — A withdrawal MUST state the same period as the revision it
replaces, and none where that revision stated none.

*Rationale for VTI-NTF-021 and VTI-NTF-022.* A notification is delivered by
store-and-forward, retried by its sender, re-issued under VTI-TRN-046 when a
copy outlives its acceptance window, and may reach one recipient by two
relayers. Each of those is the same notification. Keying the recipient's record
on the originator's own identifier, rather than on the identifier of the
document that carried it, is what makes all of them one record, and the
revision is what lets the originator change or withdraw it without the change
being taken for a repeat. The key holds only while the originator's identifier
does, which is why a node issues notifications as its durable identity: a
`did:webvh` identifier, in either of its forms, survives the rotation of every
key behind it.

*Rationale for VTI-NTF-025.* The period is what bounds how long a recipient
keeps a notification (VTI-NTF-052) and how long a relayer keeps its record of
whom it relayed to (VTI-NTF-044). A withdrawal that dropped the period would be
held for each party's default retention, longer than the notification it
withdraws, and a relayer would keep the record of whom it told for longer than
VTI-NTF-044 allows; one that shortened the period could end the relayer's
record before the withdrawal had reached everyone it must.

*Rationale for VTI-NTF-023.* A reply that said "accepted" or "declined" would
tell any sender whether the recipient has a relationship with it, and a reply
from a relayer that said "relayed" would tell the originator that the relayer
has members it passed the notification to. Neither is the sender's business.
The reply confirms receipt, which is what VTI-NTF-020 needs, and nothing else.

### Acceptance

**VTI-NTF-030** — A recipient MUST decide whether to hold a notification by an
acceptance policy that it states. A VTA's policy MUST, unless the principal has
chosen otherwise, accept a notification only from a party with which the
principal holds a relationship: a community, group or network whose membership
credential the principal holds; a party with which the principal holds a
verifiable relationship credential; a party holding an access control entry at
the VTA; or a party the principal has chosen to accept notifications from.

**VTI-NTF-031** — For a relayed notification, the relationship VTI-NTF-030
counts MUST be the recipient's relationship with the relayer that delivered it,
never with the originator.

**VTI-NTF-032** — A recipient MUST discard, and MUST NOT hold, a notification
whose originator's proof does not verify, that is not addressed to it, whose
relay chain fails VTI-NTF-042, or whose period has ended.

**VTI-NTF-033** — A recipient MUST bound the notifications it holds from any
one originator and from any one relayer, and MUST bound the work it spends on
senders with which it holds no relationship separately from the work it spends
on those with which it does (VTI-TRN-029).

**VTI-NTF-034** — A VTC's acceptance policy MUST, unless its administrators
have chosen otherwise, accept a notification only from: a party holding an
access control entry at the community; a party with which the community holds
a verifiable relationship credential in force; or a community or network of
which the community is itself a member, as the community's own record of that
membership shows. A listing the community has not acknowledged (VTI-VTN-011)
MUST NOT count as membership.

*Rationale for VTI-NTF-034.* VTI-NTF-030 counts a membership by the credential
the principal holds, and a VTC holds no vault from which to show one. What it
has instead is its own record of the memberships it entered, which is the
authoritative fact for the community even where the credential that evidences
it to others is held elsewhere. A listing the community never acknowledged is
a third party's statement about the community, not a relationship the
community chose, and counting it would let any network that lists the
community reach its inbox.

*Rationale for VTI-NTF-030 and VTI-NTF-031.* A notification costs its sender
one signature, and an inbox open to every sender is a channel for anyone with
an identifier. Accepting only from parties the principal already stands in a
relationship with bounds that to parties the principal chose, each of which is
accountable for what it sends. A relayed notification is accepted on the
relayer's standing because that is the relationship the member has: the point
of relaying is to reach members who have never heard of the originator. The
relayer is accountable for what it passes on, which is why VTI-NTF-044 asks it
to apply its own policy first.

### Relaying

**VTI-NTF-040** — An addressee that stands for others MAY relay a notification
it has accepted to any of them, by any means and at any time it chooses. This
specification does not require it to relay any notification, and does not
define how it chooses whom to relay to.

**VTI-NTF-041** — A relay MUST be an operation document of the catalogue's
relay operation, issued and signed by the relayer, addressed to one party, and
carrying the document the relayer received exactly as received, proof
included. A relayer MUST NOT alter or re-sign the document it relays, MUST NOT
relay a document that is not addressed to it, and MUST NOT relay a notification
that states it may not be relayed or whose kind may not be relayed. A relayer
MUST relay only where its relay makes at most four relays in all, counting its
own and every relay in the chain it received: it MAY relay a notification it
received directly or through up to three relays, and MUST NOT relay one that
reached it through four.

**VTI-NTF-042** — A recipient of a relay MUST verify every layer of it: that
each relay's proof verifies against its relayer; that the document each relay
carries is addressed to that relay's issuer; and that the innermost document is
a notification whose originator's proof verifies. It MUST refuse a relay that
carries more than four relays in all, counting its own. It MUST apply its acceptance window
(VTI-OPS-024) to the relay it received, and MUST NOT apply it to the documents
that relay carries, which are evidence of what was said rather than requests.
VTI-KEY-107 applies to the relay it received; the proof of a document that
relay carries is verified for its signer and integrity only, and a repeat of
that document is absorbed by VTI-NTF-022.

**VTI-NTF-043** — A relayer MUST NOT tell the originator whom it relayed a
notification to, how many, or whether it relayed it at all, and an originator
MUST NOT ask it to. Delivery evidence for a relay goes to the relayer only.

**VTI-NTF-044** — A relayer SHOULD apply its own acceptance policy to a
notification before relaying it. It MUST relay every later revision of a
notification it relayed, including a withdrawal, to every party it relayed an
earlier revision to, and MUST therefore keep a record of those parties. It MUST
keep that record until the notification's period ends, or, for a notification
that states no period, for a period the relayer states and applies to every
such notification, and MUST NOT use the record for any other purpose. A
relayer MAY relay a revision that arrives after it has discarded the record to
the parties it would relay a new notification to.

**VTI-NTF-046** — Before a relayer relays a notification of a kind whose
definition says that a relay discloses something about the relayer, whoever
decides the relay — the relayer's administrator, or for an individual, the
principal — MUST have been shown what it discloses. For the kind `election`,
that is that relaying shows each party it is relayed to that the relayer is a
member of the community holding the election, which the relayer's own listing
in that community's directory may otherwise keep from them (VTI-DIR-040).

**VTI-NTF-047** — A relayer MUST NOT relay a notification to its originator,
to any relayer the chain it received names, or to itself.

**VTI-NTF-045** — A relay MUST NOT be read as a statement by the relayer about
the thing. It states only that the relayer passed the originator's notification
to the relay's addressee.

*Rationale for VTI-NTF-041 and VTI-NTF-042.* Carrying the received document
whole, rather than restating it, is what lets the final recipient check the
originator's own signature, and checking that each layer was addressed to the
party that relayed it is what stops a party from relaying a notification
someone else was given. A party that could re-sign would be an originator, and
is one: a party that wants to say something of its own sends a notification of
its own. The bound on depth covers the deepest chain VTI composes — a network,
a community it lists, a group of that community, the group's member — with one
to spare, and stops a loop from growing a relay without end. The producer's bound
and the recipient's are one bound stated from each side, so that a relay its
relayer was permitted to make is never one its recipient must refuse. A
forwarded layer's proof is not applied as VTI-KEY-107 applies an
`authentication` proof to a request because it is not a request to the
recipient: it is addressed to the relayer, its acceptance window closed long
ago, and its identifier was recorded by the party it was sent to. The replay
VTI-KEY-107 guards against is that of a request; a notification replayed as
evidence changes nothing, because the recipient holds one record for it
whichever document carried it.

*Rationale for VTI-NTF-043.* A community that puts an election to the members
of another party does not hold that party's roster and has no right to it
(VTI-VOT-028). A relay that reported back would hand it the roster one receipt
at a time.

*Rationale for VTI-NTF-047.* A relay is itself a report that the relayer
relayed. Relaying to the originator, or to a party that relayed the
notification on the way here, would tell it exactly what VTI-NTF-043 forbids
the relayer to tell it, however the relayer came to select it — a network
that is also a member of the community it relayed to would otherwise learn,
from the relay it receives back, that the community passed the notification
on.

*Rationale for VTI-NTF-044.* A member who was told of an election and is not
told it was cancelled holds a notification that points at nothing, and may
present a credential to open it. The relayer is the only party that knows whom
it told, so it is the only party that can tell them again. Keeping that record
until the notification lapses is the least that does this; keeping it longer,
or for anything else, would turn the relayer's courtesy into a log of whom it
reached.

*Rationale for VTI-NTF-046.* Relaying is the relayer's own disclosure, not the
originator's. A community that notifies a member party tells that party only
what it already knows, that it is a member; a party that relays the
notification tells its own members, some of whom it may have chosen not to tell.
That choice is the relayer's to make, and it is made well only by whoever has
been shown what it reveals.

### The recipient's agent

**VTI-NTF-050** — A VTA MUST hold each notification it accepts for an
identifier it holds keys of in the trust context that identifier belongs to,
and MUST let a party with authority in that context list the notifications
held there, read one, and dismiss one, through the catalogue's operations. A
read MUST return the document as it was received, so that the reader can
verify the originator's proof and every relay itself.

**VTI-NTF-051** — A VTA MUST NOT disclose a notification held in one context
to a party without authority in that context.

**VTI-NTF-052** — Dismissal MUST be the principal's alone: a VTA MUST NOT
dismiss a notification on any party's behalf except the principal's or a party
the principal authorized, and a dismissed notification MUST remain readable,
and listable when asked for, until it lapses. A VTA SHOULD discard a
notification that has lapsed or been withdrawn after a period it states.

**VTI-NTF-053** — A VTA MUST NOT, in consequence of receiving a notification,
send anything to its originator, to any relayer, or to the thing's holder, and
MUST NOT open the thing except at the choice of the principal or of a party
with authority in the context.

**VTI-NTF-054** — A client acting for a principal that receives a notification
or a relay addressed to an identifier whose keys the principal's VTA holds, in
a context the client has authority in, MUST hand the document to that VTA
exactly as received, proof included, through the catalogue's operation for
doing so, and MUST NOT apply an acceptance policy of its own in place of the
VTA's. The client MUST answer the document's sender as VTI-NTF-023 requires,
with the same reply whatever the VTA does with the document, and is bound by
VTI-NTF-053 as the VTA is.

**VTI-NTF-055** — A VTA MUST verify and hold a document handed to it under
VTI-NTF-054 exactly as it would one delivered to it directly: VTI-NTF-022,
VTI-NTF-030 – VTI-NTF-033 and VTI-NTF-042 apply unchanged, and it MUST answer
the client as VTI-NTF-023 requires. The relationship VTI-NTF-030 counts MUST be
the VTA's principal's relationship with the party that delivered the
document — its originator, or the last relayer — and MUST NOT be the client's
authority at the VTA. The document's own issuer, established by its proof,
is the party the document is attributed to. VTI-OPS-024, VTI-OPS-026,
VTI-OPS-027 and VTI-KEY-107 apply to the document as received by the VTA, at
the time it is handed over.

**VTI-NTF-056** — A VTA MUST accept a document under VTI-NTF-054 only from a
party with authority in the context named, and only where the document is
addressed to an identifier the VTA holds keys of in that context.

*Rationale for VTI-NTF-053.* Opening the thing is where the holder learns who
is reading it — for an election under a credential roll naming a member party,
the read itself carries the member's credential (VTI-VOT-015). An agent that
opened every notified thing, or confirmed receipt to the originator, would turn
each notification into a probe of which members it reached. Holding a
notification tells nobody anything; acting on it is the principal's decision.

*Rationale for VTI-NTF-054 – VTI-NTF-056.* A notification is delivered to the
identifier it is addressed to, over the transport that identifier advertises
(VTI-NTF-020), and the party holding that transport is not always the VTA: the
mediator permits one connection per identifier, and for a persona identifier
the principal's client may hold it under a collection claim (VTI-NTF-081).
Without a hand-over, a notification
addressed to the persona never reaches the principal's held notifications,
and the principal's stated acceptance policy never sees it. A client that
filtered what it handed over would be applying a policy the principal did not
state; one that handed it over altered would destroy the proof every later
reader verifies. Counting the client's own authority as the relationship would
let any client make the VTA hold anything addressed to the persona; counting
the delivering party, as a direct delivery does, means a document handed over
is accepted exactly when it would have been accepted had the VTA held the
socket. The acceptance window is applied at the hand-over because the
hand-over is part of the delivery: a client that holds a document past its
window is a delivery that arrived late, and is answered by VTI-TRN-046 as any
other is.

### The election kind

The notification kind registry (Appendix G) registers the kind `election`,
which this section defines.

**VTI-NTF-060** — A notification of the kind `election` MUST name the community
as the thing's holder, the election's identifier as the thing's identifier, and
the catalogue's operation for reading one election as the operation that opens
it. Its audience MUST be the identifier the election's roll names under a
credential roll (VTI-VOT-026), and the community's own identifier otherwise. It
MAY state the election's voting period as its period, and lapses when voting
closes. It MUST carry nothing else of the election. It MAY be relayed, and a
relay of it discloses that the relayer is a member of the community
(VTI-NTF-046).

**VTI-NTF-061** — A VTC that holds an election under a credential roll naming a
party that is itself a member of the community MUST notify that party once the
election is registered with its ballot box, MUST NOT notify any party but that
one, and MUST withdraw the notification if the election is cancelled, or
closed early, before the time the notification states voting closes.

**VTI-NTF-062** — A VTC MAY notify its members, or the members of one of its
groups, of an election their own list already shows them (VTI-VOT-015), and
MUST NOT notify any party of an election VTI-VOT-015 would not show it, except
as VTI-NTF-061 permits.

**VTI-NTF-063** — A member's agent that shows a member the elections they may
take part in MUST show both the elections the community's list shows the
member and those it holds a notification of the kind `election` for. It MUST
show a notified election only as notified — its community, identifier, audience
and period — until the member opens it, and MUST open it only by reading the
election from the community, presenting a credential only as VTI-VOT-016
allows.

*Rationale for the withdrawal in VTI-NTF-061.* An election closed early is no
longer open to the audience, though nothing about it was cancelled, and a
notification that still says voting is open until the original close points
members at an election they can no longer vote in — and may lead them to
present a credential to find that out. The withdrawal keeps the period it
replaces (VTI-NTF-025), so a recipient sees both that the election was
withdrawn and when it had been due to close.

*Rationale for VTI-NTF-061 and VTI-NTF-063.* A community's list never shows an
election put to the members of a party that is itself a member of the
community, not even its identifier (VTI-VOT-015), and the community holds no
record of those members, so it cannot reach them. The party can: the list shows
it the election, and it alone holds the roster. Notifying the party and leaving
the rest to it puts each step with the one party able to take it. The
community learns nothing about the party's members by doing so, and the party
decides, by relaying or not, what its members learn of the community. A member
who receives the relayed notification knows only that the community says an
election exists that their party's members may take part in. What the election
asks, and whether this member may vote in it, they learn only by opening it and
presenting the credential that makes them one, which is the member's choice.

### A community's received notifications

**VTI-NTF-070** — A VTC MUST hold each notification it accepts, and MUST let an
administrator holding the capability for reading received notifications list
the notifications it holds and read one, through the catalogue's operations.
A read MUST return the document as it was received. A VTC MUST NOT disclose a
notification it holds to any other party, its members included, except by
relaying it under VTI-NTF-071.

**VTI-NTF-071** — A VTC MUST relay a notification only on the decision of an
administrator holding the capability for relaying received notifications over
the recipients the decision names, made through the catalogue's operation, and
MUST refuse a decision for a notification whose kind's definition says a relay
discloses something about the relayer unless the decision states that the
administrator was shown what it discloses (VTI-NTF-046).

**VTI-NTF-072** — A relay decision MUST name its recipients as all of the
community's members, the members of one of its groups, or the members holding
one community role. The VTC MUST resolve them from its own records when the
decision is made, to members whose membership is active, and MUST leave out the
parties VTI-NTF-047 excludes and every party it already relays the notification
to. A member who joins the named set later MUST NOT be added without a new
decision. Every later revision MUST be relayed to every party a decision added
(VTI-NTF-044).

**VTI-NTF-073** — A VTC MUST audit each relay decision — the administrator, the
notification, the revision, the recipients as named, how many parties it added,
and the acknowledgement — and MUST NOT record in the audit trail, or return to
the administrator, which parties it reached.

**VTI-NTF-074** — A relay decision is not authority-conferring (VTI-APV-018).
A VTC MAY hold one for a step-up or a further administrator's consent under its
approval rules (VTI-APV-010), and SHOULD do so for a decision that names all of
its members.

*Rationale for VTI-NTF-070 and VTI-NTF-071.* What a community has been told
says what it belongs to — which communities, groups and networks count it a
member — and is the community's to disclose, so its administrators see it and
its members do not until someone decides to tell them. VTI-NTF-046 requires that
whoever decides a relay has been shown what it discloses; making the decision
an operation of its own, with the acknowledgement in it, is what makes that
checkable rather than an instruction to an interface.

*Rationale for VTI-NTF-072.* A relay decision names a set rather than a list of
members because the set is what an administrator decides about — "our board",
"everyone" — and a list assembled by hand is a list an administrator can get
wrong. Resolving it once, at the decision, keeps the decision what the
administrator saw: a member who joins afterwards was not part of it, and
adding them silently would extend a disclosure nobody decided. The record of
whom the decision reached exists only for later revisions, so it is not
returned, and not audited by name, which would make the audit trail the log of
whom the community reached that VTI-NTF-044 forbids.

*Rationale for VTI-NTF-074.* A relay grants its recipients nothing
(VTI-NTF-004), so it is not a grant VTI-APV-018 covers. It is irreversible all
the same: what each member is told about the community cannot be recalled,
and a decision that reaches every member discloses the most. A community that
holds its other irreversible acts for a step-up has the same reason to hold
this one.

### Collection for the principal's identifiers

A notification addressed to an identifier that advertises a transport through a
[[ref: mediator]] is delivered, as far as its sender can tell, when it is queued
at that mediator; it reaches the identifier only when some party collects it.
The mediator permits one connection per identifier, so at most one party can
collect for an identifier at a time, and a second connection evicts the first.
A VTA holds the keys of the identifiers it creates for its principal — a
persona it presents to a community, for instance — but a client of the
principal may be the party that holds the connection. A client that cannot
hold it, such as a browser that does not hold the identifier's keys, leaves
the identifier with no collector at all, and every notification and relay
addressed to it waits at the mediator until it expires. This section makes the
VTA the collector of every such identifier no client has claimed, and fixes how
a client and the VTA agree which of them is the collector.

In this section, to **collect** for an identifier is to retrieve the messages
queued for it at the mediator its identifier document advertises, by whatever
means that mediator provides and for every protocol it carries there. The
identifier's **collector** is the party that does so. A **collection claim** is
the VTA's record that one client of the principal is the collector of one
identifier until a stated time, its **end**; the client is its **claimant**.

**VTI-NTF-080** — Where collection by default is enabled for the trust context
an identifier belongs to (VTI-NTF-094 – VTI-NTF-098), a VTA
MUST collect for every identifier whose `messaging` keys it holds in one of its principal's trust contexts and whose identifier document
advertises a transport through a mediator, from the time the document first
advertises one, except:

1. its own durable identity (VTI-KEY-001), which it collects for as a node and
   for which it MUST NOT grant a collection claim;
2. the durable identity of another node or host service, and an identity it
   provisioned for another party (VTI-VTA-030 – VTI-VTA-033), which their
   holders collect for; and
3. an identifier for which a collection claim is in force.

**VTI-NTF-081** — At most one party MUST collect for an identifier at a time. A
VTA MUST NOT collect for an identifier while a collection claim for it is in
force, and a client MUST NOT collect for an identifier VTI-NTF-080 requires the
VTA to collect for except while it is the claimant of a collection claim for it
that is in force.

**VTI-NTF-082** — A VTA MUST grant a collection claim, through the catalogue's
operation for claiming one, only to a client with authority in the trust
context the identifier belongs to, only for an identifier VTI-NTF-080 requires
it to collect for or one already claimed by the same client, and only where no
claim by another client is in force for it. Before it answers a grant, the VTA
MUST stop collecting for the identifier and close every connection it holds
for it. A claim MUST state its end, no later than a maximum period from the
grant that the VTA states, and the VTA MUST answer the grant with that end. A
claimant MAY renew its claim through the same operation before the claim ends,
and a renewal moves the end and is otherwise a grant. The VTA's stated maximum
SHOULD NOT exceed thirty days.

**VTI-NTF-083** — A claimant MUST NOT open a connection that collects for the
identifier before it holds the VTA's grant, and MUST stop collecting for it,
and close every such connection, before the claim's end less its clock-skew
tolerance as its own clock reads, unless it has renewed the claim by then. A
claimant SHOULD ask for a claim of twenty-four hours and SHOULD renew it once
half of the period granted has passed. A
claimant that releases its claim MUST stop collecting before it asks the VTA to
release it.

**VTI-NTF-084** — A claim ends at its end, or earlier when its claimant
releases it through the catalogue's operation for releasing one. A party with
authority in the identifier's context other than the claimant MAY release a
claim through the same operation, and a claim MUST become unrenewable when it
is so released, when the claimant's access control entry is removed or expires,
or when the entry ceases to give it authority in that context; such a claim
still ends at its end. A rollover of the claimant's entry (VTI-ACL-055 –
VTI-ACL-057) and a rotation of its key (VTI-CLT-025) carry the claim to the
successor. A VTA MUST NOT resume collecting for an identifier before the claim
for it has ended and, unless the claimant released it, before the end plus the
VTA's clock-skew tolerance.

**VTI-NTF-085** — A collector whose connection for an identifier is closed by
another connection for the same identifier MUST NOT re-open it repeatedly at
once. It MUST back off between attempts, and MUST stop after a bound it states
until the conflict is resolved. A VTA MUST record each such conflict on a
connection it holds in the audit trail, and MUST show the identifier as in
conflict in the listing of VTI-NTF-091 until it collects for it again.

**VTI-NTF-086** — A VTA MUST treat a notification or relay it collects for an
identifier exactly as one handed to it under VTI-NTF-054: VTI-NTF-055 applies,
with the VTA in the place of the client and the time of collection in the
place of the hand-over, and the relationship VTI-NTF-030 counts is the
principal's with the party that delivered it. The VTA MUST answer the sender as
the identifier collected for, with the reply VTI-NTF-023 requires, and that
reply MUST be the same, from the same identifier, whichever party collected the
document and whatever the VTA did with it.

**VTI-NTF-087** — A VTA MUST NOT perform, hold or relay any other operation
document it collects for an identifier, and MUST NOT treat one as a request to
the VTA itself, whatever authority its sender holds at the VTA. It MUST answer
each as an operation it does not serve (VTI-OPS-081), as the identifier
collected for, and MUST NOT name
its own identity in that refusal or in any other reply it makes as that
identifier. A message of the transport's own, which carries no operation, it
handles as that transport's specification requires of a recipient.

**VTI-NTF-088** — The work a VTA spends on the messages it collects MUST be
bounded by the same bounds that apply to the messages delivered to it directly
(VTI-TRN-029): a sender with no standing at the VTA counts against the bound
for senders without standing, and the bounds of VTI-NTF-033 apply to what one
originator or one relayer delivers to every identifier the VTA collects for in
a context, together with what is handed to it there under VTI-NTF-054. A VTA MUST
NOT state or apply either bound per identifier collected for. A VTA MUST bound
the number of identifiers it collects for, and MUST state that bound; an
identifier it does not collect for because of the bound it MUST show as
uncollected in the listing of VTI-NTF-091.

**VTI-NTF-089** — A VTA MUST collect within the [[ref: protection boundary]]
that holds the identifier's `messaging` keys, and MUST NOT give those keys, or
a message it collected in the clear, to a process outside that boundary in
order to collect. A VTA MUST NOT collect for identifiers of different trust
contexts over one connection, and MUST document, as VTI-TRN-027 requires, what
its connections for identifiers of different contexts reveal in common to the
mediator.

**VTI-NTF-090** — A rotation of an identifier's keys MUST NOT change its
collector or end a claim for it. Where an identifier's document comes to name a
different mediator, its collector MUST collect at the one now named, and the
VTA, where it is the collector, SHOULD continue to collect at the one previously
named for a period it states. When a VTA ceases to hold an identifier's keys,
the identifier is deactivated, or its trust context is deleted, the VTA MUST
stop collecting for it and MUST end every claim for it, and a claim so ended is
not in force for the purposes of VTI-NTF-081.

**VTI-NTF-091** — A VTA MUST let a party with authority in a trust context list,
through the catalogue's operation for doing so, the identifiers there that
VTI-NTF-080 names, and for each whether the VTA collects for it, a client holds
a claim for it — with the claimant, the claim's end and whether it is
renewable — or nobody collects for it, and why. It MUST NOT disclose any of
this to a party without authority in that context (VTI-NTF-051).

**VTI-NTF-092** — A claimant is bound by VTI-NTF-054 for every notification and
relay it collects. A VTA MUST accept a document handed to it under VTI-NTF-056
whether or not the client handing it over holds a claim, and whether or not the
VTA collects for the identifier; VTI-NTF-022 makes one record of a document that
reaches it both ways.

**VTI-NTF-093** — *Withdrawn.*

*Note.* VTI-NTF-093 made collection by default a setting of the VTA's operator,
disabled unless the operator enabled it, and is withdrawn in favour of
VTI-NTF-094 through VTI-NTF-098, which make it a setting of each trust context,
enabled unless it is disabled there.

**VTI-NTF-094** — Collection by default MUST be a setting of each trust context
of the principal's, and a VTA MUST hold it enabled for a context unless it has
been disabled for that context under VTI-NTF-095. A context's setting MUST
apply to the identifiers of that context alone, and not to those of its
ancestors or descendants. The VTA MUST keep the setting where it survives the
VTA's restart, including the restart of a trusted execution
environment that holds it, and MUST NOT require a restart, or a change to its
configuration, in order to apply a change of it.

**VTI-NTF-095** — A VTA MUST let a party with administrative authority over a
trust context (VTI-ACL-022) — an entry whose role is administrative and whose
act scope covers the context — change the context's setting, through the
catalogue's operation for doing so, and MUST refuse every other party. A change
MUST take effect when the VTA answers it. When a change disables collection by
default, the VTA MUST, before it answers, stop collecting under VTI-NTF-080 for
the context's identifiers and close every connection it held to do so. When a
change enables it, the VTA MUST begin to collect for each identifier there that
VTI-NTF-080 then names, including one it had stopped collecting for under
VTI-NTF-085. A change to the value already held MUST succeed and change nothing
but the record of VTI-NTF-096.

**VTI-NTF-096** — A VTA MUST record each change of a context's setting in the
audit trail, with the party that made it, the context and the value set. It
MUST let a party with authority in the context read, through the catalogue's
operation for doing so, the context's setting, the party that last changed it
and when — or that it has never been changed — and whether a suspension under
VTI-NTF-098 is in force. It MUST NOT disclose any of this to a party without
authority in that context (VTI-NTF-051).

**VTI-NTF-097** — While collection by default is disabled for a context, the
VTA MUST NOT collect for any of its identifiers under VTI-NTF-080, and MUST
still grant, renew, release and list collection claims there (VTI-NTF-082,
VTI-NTF-084, VTI-NTF-091), showing each unclaimed identifier as collected by
nobody because collection by default is disabled. A party with administrative
authority over a context SHOULD disable it there while a client of the
principal collects for an identifier of that context without claiming it
(VTI-NTF-083).

**VTI-NTF-098** — A VTA MAY let its operator suspend collection by default for
every context at once. A suspension MUST only disable: while it is in force the
VTA MUST treat every context as one for which collection by default is disabled
(VTI-NTF-097), whatever the context's setting, and MUST NOT change any
context's setting; once it is lifted, each context's setting applies again. A
VTA MUST NOT be suspended unless its operator has suspended it, and MUST NOT
offer its operator a means of enabling collection by default for a context
whose setting disables it. Suspending and lifting a suspension MUST be recorded
in the audit trail. Suspending MUST stop collection as a change that disables
it does under VTI-NTF-095, and lifting a suspension MUST resume it, in each
context whose setting enables it, as a change that enables it does.

*Rationale for VTI-NTF-080.* The identifiers a VTA creates for its principal are
the ones whose keys it holds and no node of their own collects for, so they are
the ones a missing collector strands. Collecting for them unless a client has
claimed them, rather than only when a client asks, puts the failure where it
can be seen. A VTA that waited to be asked would leave every identifier of a
principal whose only client is a browser exactly as uncollected as it is
without this section, and nothing would report it: the sender holds collection
evidence for nothing, and the principal is never told a notification was sent.
The cost of collecting by default is the opposite case, a client that collects
without claiming, and that case announces itself as a conflict (VTI-NTF-085)
that the principal can see and resolve. The exceptions are the identities
another holder collects for: a node's own and those it was given, which
collecting for would take from their holders.

*Rationale for VTI-NTF-081 – VTI-NTF-084.* Two collectors of one identifier do
not share it: each connection evicts the other, each reconnects, and messages
are lost to whichever was evicted mid-delivery. Exclusion therefore has to be
agreed before either side connects, and the VTA, which holds the keys and the
context, is the party that records it. The VTA stops before it grants and the
client connects only after it holds the grant, so that the hand-over never has
both connected. A claim is a lease rather than a standing assignment because
the client that holds it can disappear without releasing it — uninstalled, a
device lost, an entry revoked. A standing assignment would then leave the
identifier with no collector for good and nobody told; a lease returns it to
the VTA at its end, and the mediator holds what arrived in between. A release
by another party ends a claim only at its end, and a revoked entry cannot
renew one, because the claimant is still connected and only its own clock
tells it to stop. Ending the claim at once would make the VTA the second
connection. The VTA waits out its skew tolerance as well, because the
claimant's clock is not the VTA's.

*Rationale for VTI-NTF-085.* A party that collects without a claim — a client
that predates this section, or one that holds the identifier's keys without
standing — is a second connection the VTA cannot prevent. Two collectors that
each reconnect at once lose messages to each other indefinitely while each
reports itself connected. Backing off and stopping turns that into a conflict
that the principal is shown, which is the only party able to settle it.

*Rationale for VTI-NTF-086 and VTI-NTF-087.* What the VTA collects is what a
client would have handed it, and accepting it on the same terms is what keeps
the principal's acceptance policy the single policy, whoever collected. A reply
that differed with the collector would tell every sender which of the
principal's devices hold their personas, which is none of its business, and a
reply from the VTA's own identity would link the persona to the principal's
agent, which the persona exists to prevent. Other operations addressed to the
identifier are not requests to the VTA: they are addressed to the persona, and
the VTA performing them would be acting as the persona on authority nobody
gave it — and would let any party with an entry at the VTA reach it through an
identifier the entry does not name. Refusing rather than dropping is what
keeps the sender's delivery honest: a message the VTA collected and then
discarded is one the sender holds collection evidence for (VTI-TRN-040), so it
records a delivery that performed nothing, whereas a refusal is a protocol
reply it acts on.

*Rationale for VTI-NTF-088 and VTI-NTF-089.* Every identifier a VTA collects
for is another inbox a stranger can write to. Bounds stated per identifier
would multiply what a sender without standing can spend by the number of the
principal's personas, so each bound is the node's, whatever identifier a
message was collected for. Collecting is decryption with the identifier's
`messaging` keys, so it belongs where those keys are held: a VTA in a trusted
execution environment collects inside it, and the host that carries its
connections sees what the mediator sees and nothing more. One connection that
collects for identifiers of several contexts would tell the mediator outright
that they are one principal's, which separate contexts and separate routing
identifiers (VTI-TRN-026) exist to withhold.

*Rationale for VTI-NTF-090.* A collection claim is about an identifier, not
about a key, so a rotation leaves it in place; a mediator change moves where
the collector looks, and messages already queued at the previous mediator are
still the identifier's. An identifier the VTA no longer holds keys of cannot be
collected for by it, and a claim on it would block nothing that remains.

*Rationale for VTI-NTF-094 – VTI-NTF-098.* The default is the state that fails
visibly. A VTA that collects beside a client that also collects without
claiming meets it as a conflict (VTI-NTF-085), which it backs off from, records
and lists, and which the principal can see and settle. A VTA that does not
collect leaves a browser-only member's personas collected by nobody, and
nothing reports that at all: the sender holds collection evidence, and the
member is never told a notification was sent. So collection is on unless it is
turned off. Clients that predate collection claims are the case it was
withheld for, and they are what VTI-NTF-097 lets a principal turn it off for.

The setting belongs to the trust context, not to the node, because the parties
who know whether a client collects without claiming are the principal's, and
the principal's clients and personas are arranged by context: a principal who
runs an older client for one context should not lose collection in every
other. It belongs to a context administrator rather than to every party with
standing there, because it decides for every client and every identifier in
the context at once — turning it off strands the personas of every
browser-only client there — which is administering the context
(VTI-ACL-022), whereas a claim decides for one identifier and one client and
needs only standing. It is not reserved to a super-administrator, which would
return the decision to the operator, who does not know which clients a
principal runs. It does not pass to descendants, so that each context's
setting is read from that context alone.

A setting held in configuration is read when the node starts, so changing it
means a restart, and a change made at run time without being stored is lost
when a trusted execution environment restarts, which is the opposite of what
whoever changed it decided. Storing it, and applying it when the change is
answered, makes the setting what the reading of VTI-NTF-096 says it is.
Claims are served while it is off so that a client can adopt them before it
is turned on again; a claim made while the VTA collects nothing changes
nothing but the record.

An operator still needs to stop collection without asking every principal — a
mediator that fails, a fault that makes the VTA's connections evict its
clients'. An off-only suspension gives that and nothing more: there is one
place collection is turned on, the context, and an operator who could also
turn it on would override a principal's decision to turn it off, which is the
one the principal makes knowing its clients.

*Rationale for VTI-NTF-092.* The hand-over and collection by the VTA are two
paths to the same record. A client that holds a claim hands over what it
collects exactly as before, and a client that received a notification some
other way may still hand it over, because refusing it would lose a
notification for a reason that has nothing to do with whether it should be
held.

*Open issue.* A VTA refuses every operation other than a notification or a relay
that it collects for an identifier (VTI-NTF-087), so a member whose only client
is a browser does not receive the other operations a community addresses to
its persona, such as a step of a credential exchange. A general inbox — the VTA
holding such operations, unperformed, for the principal's client to fetch and
perform — is not yet specified: what it may hold, for how long, under which
acceptance policy, and how a client performs an operation addressed to an
identifier whose keys only the VTA holds. Until it is, VTI-NTF-087 applies. A
sender can also tell, from a refusal where a client would have performed the
operation, that the persona has no claiming client.

*Open issue.* A claim is ended by removing its claimant's standing, but a
client that was given an identifier's `messaging` keys keeps them, and a
revoked device can go on collecting for the identifier without a claim. The
VTA then meets it as a conflict (VTI-NTF-085) and stops, which leaves the
identifier collected by the revoked device. The remedy is to rotate the
identifier's `messaging` keys, so that what is sent to it after the rotation is
encrypted to keys the device does not hold, and to move it to a connection the
device cannot open. How a VTA rotates a persona's keys on revoking a client,
and what it tells the parties the persona holds relationships with, is not yet
specified.

*Open issue.* A notification's originator is compared by its identifier
(VTI-NTF-022). A change of the identifier method an originator issues under —
not a key rotation, which leaves a `did:webvh` identifier unchanged, but a move
to another method, or a presented form a recipient cannot match to the earlier
one — makes a later revision a new notification at the recipient. This chapter
does not yet say how a recipient recognises the same originator across such a
change.

*Open issue.* A principal's client cannot yet send a notification through its
VTA as the principal. A party wanting to notify others sends the notification
operation itself; a VTA operation that signs and sends one for its principal is
deferred.

*Open issue.* A group or network that receives notifications holds them as its
own node decides. This chapter specifies the held notifications of a VTA's
principal (VTI-NTF-050 – VTI-NTF-056) and of a community (VTI-NTF-070 –
VTI-NTF-074), and does not yet specify how the administrators of a community
group with its own identifier, or of a network, see what was addressed to it.
