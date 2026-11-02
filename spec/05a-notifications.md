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
notification, one to relay it, and, at a VTA, one each to list, read and
dismiss the notifications held for a principal.

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
originator's records show at the time it sends.

*Rationale for VTI-NTF-001.* A notification that is relayed reaches parties
that never had a session with its originator, and an authenticated transport
tells each hop only who the previous hop was. The originator's proof is the one
thing that survives every relay, so a final recipient can tell who said there
is an election from who passed the message on.

*Rationale for VTI-NTF-003 and VTI-NTF-004.* A notification travels further
than the thing it points at: it may be relayed to every member of a network
before any of them has shown that they may see the thing. What the thing
contains is therefore shown only by its holder, to a reader it admits, when it
is opened. A notification says where to look, and the look is where the
decision is made. Treating a notification as eligibility would let whoever can
send one enfranchise whoever receives it.

### Kinds

**VTI-NTF-010** — Every notification MUST name a kind. A kind is either
registered in the catalogue's notification kind registry, or named under a
domain its definer controls.

**VTI-NTF-011** — A kind's definition MUST state: what the thing is; the
operation by which it is opened; what the audience means for it; what a
notification of the kind may carry beyond the members of VTI-NTF-002, which is
nothing unless the definition says otherwise; whether it may be relayed; and
when it lapses.

**VTI-NTF-012** — A recipient MUST NOT act on a notification of a kind it does
not recognise beyond holding, listing and discarding it, and MUST NOT present
one as anything but what VTI-NTF-002 states of it.

*Rationale.* A closed set of kinds would make every new use a new version of
the notification operation and every recipient a party to every use. A
registry keeps the operation fixed and the uses open, as the capability
registry does (Appendix C), and the rule of VTI-NTF-012 is what makes an
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
identifier and revision.

**VTI-NTF-022** — A recipient MUST hold at most one record of a notification
for each pair of originator and notification identifier. A higher revision
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
before it lapses.

*Rationale for VTI-NTF-021 and VTI-NTF-022.* A notification is delivered by
store-and-forward, retried by its sender, re-issued under VTI-TRN-046 when a
copy outlives its acceptance window, and may reach one recipient by two
relayers. Each of those is the same notification. Keying the recipient's record
on the originator's own identifier, rather than on the identifier of the
document that carried it, is what makes all of them one record, and the
revision is what lets the originator change or withdraw it without the change
being taken for a repeat.

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
credential the principal holds; a party holding an access control entry at the
VTA; or a party the principal has chosen to accept notifications from.

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
that states it may not be relayed or whose kind may not be relayed.

**VTI-NTF-042** — A recipient of a relay MUST verify every layer of it: that
each relay's proof verifies against its relayer; that the document each relay
carries is addressed to that relay's issuer; and that the innermost document is
a notification whose originator's proof verifies. It MUST refuse a relay that
carries more than four relays in all, counting its own. It MUST apply its acceptance window
(VTI-OPS-024) to the relay it received, and MUST NOT apply it to the documents
that relay carries, which are evidence of what was said rather than requests.

**VTI-NTF-043** — A relayer MUST NOT tell the originator whom it relayed a
notification to, how many, or whether it relayed it at all, and an originator
MUST NOT ask it to. Delivery evidence for a relay goes to the relayer only.

**VTI-NTF-044** — A relayer SHOULD apply its own acceptance policy to a
notification before relaying it, and SHOULD relay a later revision it accepts,
including a withdrawal, to every party it relayed an earlier revision to.

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
to spare, and stops a loop from growing a relay without end.

*Rationale for VTI-NTF-043.* A community that puts an election to the members
of another party does not hold that party's roster and has no right to it
(VTI-VOT-028). A relay that reported back would hand it the roster one receipt
at a time.

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

*Rationale for VTI-NTF-053.* Opening the thing is where the holder learns who
is reading it — for an election under a credential roll naming a member party,
the read itself carries the member's credential (VTI-VOT-015). An agent that
opened every notified thing, or confirmed receipt to the originator, would turn
each notification into a probe of which members it reached. Holding a
notification tells nobody anything; acting on it is the principal's decision.

### The election kind

The catalogue's notification kind registry registers the kind `election`, which
this section defines.

**VTI-NTF-060** — A notification of the kind `election` MUST name the community
as the thing's holder, the election's identifier as the thing's identifier, and
the catalogue's operation for reading one election as the operation that opens
it. Its audience MUST be the identifier the election's roll names under a
credential roll (VTI-VOT-026), and the community's own identifier otherwise. It
MAY state the election's voting period as its period, and lapses when voting
closes. It MUST carry nothing else of the election, and it MAY be relayed.

**VTI-NTF-061** — A VTC that holds an election under a credential roll naming a
party that is itself a member of the community SHOULD notify that party once
the election is registered with its ballot box, MUST NOT notify any party but
that one, and MUST withdraw the notification if the election is cancelled
before voting closes.

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

*Open issue.* Whether a VTC MUST, rather than SHOULD, notify the party a
credential roll names is not settled. A community that does not leaves the
party's members to learn of the election from a link or from outside VTI, and
an election that its voters cannot find is one whose turnout says little.

*Open issue.* A relayed notification tells each member the relayer reaches that
the originator holds something for the audience. For an election, that is
that the community holds an election and that the party is its member, which
the party's own listing in the community's directory may otherwise keep from
those members (VTI-DIR-040). The relayer decides, by relaying, but this
chapter does not yet require a relayer to be told what a relay discloses.
