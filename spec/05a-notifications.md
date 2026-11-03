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
notifications held for a principal, and one by which the principal's client
hands the VTA a notification it received for an identifier whose keys the VTA
holds; and at a VTC, one each by which its administrators list and read the
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
the principal's client usually holds it. Without a hand-over, a notification
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
