## Community Boards

This section is normative.

### In plain terms

A community holds an unconference. Someone pins a wall of cards: anyone in the
community may write an idea for a session on a card and pin it up, and anyone may
stick a note beside a card or on the wall itself. A facilitator walks the wall,
merges two cards that say the same thing, takes down one that does not belong,
and puts each card that stays to a show of hands. When the hands are counted the
facilitator picks the sessions and writes them into the timetable.

A board in a community is that wall. Proposing an idea and leaving a note are
things a member does in their own name; everyone who can see the board can see
who wrote what. Voting on an idea is different: each idea the facilitator
accepts becomes an election of its own, run as the Community Voting chapter
runs every election, so the board learns how many ballots an idea drew and,
after the count, its result, and never who voted or how.

### What this chapter defines

This chapter specifies how a community keeps **boards**: shared spaces where its
members propose **ideas**, leave **notes**, and vote on each idea through an
election, and where the people running the board select among them.

A VTC is not required to offer boards. A VTC that offers them MUST satisfy the
requirements of this chapter; a VTC that does not is not bound by them. A VTC
offering boards MUST offer voting (Community Voting chapter).

The chapter adds no authority model. A board's result decides nothing by itself:
which ideas are selected is a decision of the people running the board, made
with the results in view, and nothing on a board grants, widens or removes any
authority (VTI-VTC-020).

The capability this chapter refers to is registered in Appendix C.3:
`vtc.boards.manage`, the capability to create and run the community's boards,
qualifiable by board.

Throughout this chapter:

- a **board** is one such space, with a title, the parties who may see and use it
  (its **audience**), its schedule, and its **election template**;
- an **idea** is one proposal on a board, made by one member;
- a **note** is a short text a member attaches to a board or to one idea on it;
- a **facilitator** of a board is a party holding `vtc.boards.manage` for it,
  unqualified or qualified by that board (VTI-ACL-035).

### Boards

**VTI-BRD-001** — A board MUST state: its identifier, unique within the community
and never reused; its title; its audience (VTI-BRD-003); the times proposals open
and close, and the times voting opens and closes, with proposals opening before
voting opens and voting closing after proposals close; and its election template
(VTI-BRD-030). A board MAY state a description, the topics an idea may be filed
under, the most ideas one member may propose to it, and a timetable of slots that
selected ideas are placed in.

**VTI-BRD-002** — Creating a board MUST require `vtc.boards.manage` unqualified and
`vtc.elections.manage` (VTI-VOT-080), because its election template decides the
elections its facilitators will open. Changing a board's election template MUST
require the same. Changing any other part of a board, and closing or archiving
it, MUST require `vtc.boards.manage` for that board.

**VTI-BRD-003** — A board's audience MUST be either every active member of the
community or the members of one of its groups. A VTC MUST show a board — any part
of it, its ideas and its notes — and MUST confirm that it exists, only to a
facilitator or to a party its audience admits as the VTC's records stand at the
time of the read; to any other party it MUST answer as it answers for a board that
does not exist. A list of boards given to a party MUST NOT include a board that
party may not see, not even its identifier.

**VTI-BRD-004** — Once voting has opened on a board, its audience and the times
voting opens and closes MUST NOT change. Its title, description, topics and
timetable MAY change at any time before it is archived.

**VTI-BRD-005** — A closed board MUST refuse every new idea and note. An archived
board MUST refuse every change and MAY be withdrawn from members' lists; a VTC
MUST keep an archived board readable to its facilitators for as long as it keeps
the elections it opened.

### Ideas

**VTI-BRD-010** — A party the board's audience admits MAY propose an idea while
proposals are open. An idea MUST record who proposed it, when, its title, its
description, its format and its topic where the board states topics. A VTC MUST
refuse a proposal from a party the audience does not admit, a proposal outside
the board's proposal window, and a proposal that would take its proposer beyond
the board's limit on ideas per member.

**VTI-BRD-011** — An idea MUST be in exactly one of these states:

1. **proposed**, awaiting a facilitator;
2. **open**, accepted, with an election opened for it (VTI-BRD-031);
3. **selected** or **not selected**, decided after its election closed
   (VTI-BRD-040);
4. **declined**, taken down by a facilitator, with a stated reason;
5. **merged**, folded by a facilitator into another idea on the same board,
   which the record MUST name;
6. **withdrawn**, taken back by its proposer.

A VTC MUST NOT move an idea out of a state in item 3, 4, 5 or 6, except that a
facilitator MAY move a **declined** idea back to **proposed** while proposals
are open.

**VTI-BRD-012** — Only an idea's proposer MAY withdraw it, and only while it is
**proposed**. Only an idea's proposer MAY change its title, description, format
or topic, and only while it is **proposed**; once an election has been opened
for it, the idea's title and description MUST be those the election's manifest
carries (VTI-VOT-013).

**VTI-BRD-013** — Accepting, declining and merging an idea, and moving it back to
**proposed**, MUST require `vtc.boards.manage` for its board. A facilitator MUST
NOT accept, decline or merge an idea they proposed, unless no other facilitator
of the board exists; a VTC that permits that MUST record that it did. A
declined or merged idea MUST stay visible to the board's audience in that state,
with the reason or the idea it was merged into.

### Notes

**VTI-BRD-020** — A party the board's audience admits MAY attach a note to the
board or to any idea on it that is not **withdrawn**, while the board is not
closed. A note MUST record who wrote it, when, and what it is attached to. A VTC
MUST bound a note's length and SHOULD bound the rate at which one party adds
notes.

**VTI-BRD-021** — A note's author MAY withdraw it. A facilitator MAY hide a note
on their board, stating a reason. A withdrawn or hidden note MUST NOT be shown to
anyone but its author and the board's facilitators; in its place the VTC MAY show
that a note was withdrawn or hidden, and by whom, but MUST NOT show its text.
Nobody MAY change a note's text after it is added.

**VTI-BRD-022** — A note MUST NOT be carried into any election, manifest, ballot
or ballot box. Nothing a member writes on a board is part of how an idea is
decided except through a facilitator who reads it.

### Elections

**VTI-BRD-030** — A board's election template MUST state the question every idea's
election asks, of a type the Community Voting chapter defines (VTI-VOT-010), and
every member of a manifest except the election's identifier, title, description
and times, which the VTC fills in when it opens an election. The template MUST NOT
mark the election binding (VTI-VOT-081); its roll MUST admit exactly the board's
audience, as the roll rule of the community's active members or a credential roll
naming the group (VTI-VOT-021, VTI-VOT-029); and it MUST name a ballot box the community
may register elections with (VTI-VOT-082).

**VTI-BRD-031** — Accepting an idea MUST open one election for it, from the
board's template, whose manifest carries the idea's title and description, the
board's voting times, and an identifier that names the board and the idea. The
VTC MUST record the election's identifier on the idea, and MUST NOT record the
idea as **open** until the ballot box has registered the election (VTI-VOT-012);
an election the ballot box refuses leaves the idea **proposed**, and the VTC MUST
tell the facilitator why. An idea MUST NOT have more than one election.

**VTI-BRD-032** — An election opened for an idea is an election like any other:
every requirement of the Community Voting chapter applies to it, and a holder of
`vtc.elections.manage` MAY close it early or cancel it as that chapter allows.
A facilitator who does not hold `vtc.elections.manage` MUST NOT close, cancel or
certify it. An idea whose election is cancelled MUST move to **not selected**.

**VTI-BRD-033** — A board MUST NOT show, to anyone, whether a party took a ticket
for, or cast a ballot in, an idea's election, and MUST NOT record either. What a
board shows of an election while voting is open MUST be limited to the
election's identifier, its stage, and, where its board is public (VTI-VOT-052),
the number of ballots on it; a board SHOULD NOT show a running count of
choices while voting is open, even where the ballot box's board would let anyone
compute one.

**VTI-BRD-034** — Where a board offers a party a way to request tickets for
several of its elections at once, each ticket MUST still be requested, issued and
recorded as the Community Voting chapter requires of one ticket in one election
(VTI-VOT-020, VTI-VOT-022), and the VTC MUST NOT learn from the board which of its elections a
party requested tickets for beyond what it learns as each election's registrar.
Taking tickets ahead of voting this way serves VTI-VOT-033, and a board SHOULD
offer it.

### Selection

**VTI-BRD-040** — After an idea's election has been tallied, a facilitator MUST
decide whether the idea is **selected** or **not selected**, and MAY place a
selected idea in a slot of the board's timetable. The decision MUST record the
result it was made with: the election's identifier and the board head the result
was computed from (VTI-VOT-070), and whether that result was certified
(VTI-VOT-071).

**VTI-BRD-041** — A board MUST show the result of a tallied idea's election beside
the facilitator's decision, so that a member can see any decision that departs
from the order of the results. A facilitator's decision MAY depart from it; a
board that states a rule for selection (such as "the twelve ideas with the most
support") SHOULD say so where the decision departs from that rule, and why.

**VTI-BRD-042** — Selecting an idea MUST NOT grant, widen or remove any authority,
and MUST NOT by itself admit anyone to anything. Anything the community does
because an idea was selected is done through the authority model of the Access
Control and Authority and the Approvals, Consent and Step-Up chapters
(VTI-VTC-020), like any other change.

### Members' acts and the portal

**VTI-BRD-050** — A proposal, a change to one's own proposal, its withdrawal, a
note and a note's withdrawal MUST each be a document signed as the member, so
that what a member says on a board stays attributable to them after the
transport has closed. A member portal that holds a key the member authorized to
sign for them for this chapter's tasks (`auth/oob` `signingScope`) MAY sign them
with it, for the session that authorization arms.

**VTI-BRD-051** — A VTC MUST NOT accept a key authorized under VTI-BRD-050 for any
act of this chapter that requires `vtc.boards.manage`, for an election's ticket,
or for a ballot. Ticket requests and casts are made only through the member's
own agent (VTI-VOT-022, VTI-VOT-023, VTI-VOT-030).

### Departure and erasure

**VTI-BRD-060** — When a member departs, a VTC MUST withdraw every idea of theirs
that is still **proposed**, and MAY show their remaining ideas and notes as those
of a former member without naming them. When a member's records are erased, a VTC
MUST remove the text of their notes and of their ideas that are **proposed**,
**declined** or **withdrawn**; an idea for which an election was opened keeps its
title and description, which its manifest carries and which cannot change
(VTI-VOT-013), but MUST NOT keep its proposer's name.

**VTI-BRD-061** — A VTC MUST NOT use a board's ideas or notes, or who wrote them,
for anything but the board, except as a member's own export of what they wrote.

### Disclosure

**VTI-BRD-070** — A VTC offering boards MUST show a member, before they first
propose an idea or add a note on a board: that what they write is shown under
their name to the board's audience; that its facilitators can decline, merge and
hide what they write; that voting on an idea is a separate election in which the
board learns no member's ticket or ballot; and that an idea's title and
description cannot change once an election is opened for it.

### Audit

**VTI-BRD-080** — A VTC MUST record an audit event for the creation, change,
closing and archiving of a board; for accepting, declining, merging and
selecting an idea and moving one back to proposed; and for hiding a note. An
audit record MUST name an idea or note by reference and MUST NOT carry its text.
An audit record MUST NOT pair a party with any election's ticket or ballot
(VTI-VOT-100).

*Open issue.* A board's audience sees who proposed each idea. A community may
want ideas proposed anonymously, as some unconferences take cards without names.
An anonymous proposal that still enforces VTI-BRD-010's limit per member and
VTI-BRD-012's proposer-only withdrawal needs a pseudonym scoped to the board, as
the voting chapter's tag is scoped to an election. This chapter does not yet
specify it.

*Open issue.* VTI-BRD-034 lets a board offer a party all its tickets at once, but
each request is still one election's registrar learning that the party asked. A
registrar that is also the board's community learns, from the timing of those
requests, that one party requested them together. Whether that matters, and how
a batched request should be spread, is not yet specified.
