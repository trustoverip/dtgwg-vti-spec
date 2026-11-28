## The Member's Calendar

This section is normative.

### In plain terms

A member belongs to three communities. One runs a workshop on Monday, another a
working-group call on Tuesday, the third its annual meeting on Saturday. Each
community knows what it runs and who is coming to it. None of them knows the
member's week, and none of them should.

The member's own agent knows it. It asks each community, as the member, for the
events the member is going to, and puts them side by side. It reminds the member
itself, at the times the member chose, so no community learns who wanted to be
reminded. It keeps what each community said when the member registered, signed,
so the member still has it after leaving the community, or after the community is
gone. It can hand the member's calendar app a private address to read, because a
calendar app cannot do anything else; that address is a key, so it is made for
one purpose, can be cancelled, and runs out.

Some events the member has only been told about: a community says there is an
event they may come to, and nothing else. The agent shows that as a slot with a
community and a time. It does not go and look, because looking tells the
community the member was interested.

### What this chapter defines

This chapter specifies the **member's calendar**: what a VTA that keeps one for
its principal reads from the communities the principal belongs to, what it keeps,
what it shows to whom, the reminders it fires, the feeds it serves to calendar
applications, and the busy times it may share.

A VTA is not required to keep a calendar. A VTA that keeps one MUST satisfy the
requirements of this chapter; a VTA that does not is not bound by them. A VTA
keeping a calendar MUST hold notifications (VTI-NTF-050), since the calendar
shows the `event` notifications it holds.

The chapter adds no authority model. A party that reads or changes a calendar
does so with the standing it has in the trust context the calendar entries
belong to (VTI-CTX-*); nothing here grants, widens or removes any authority, and
nothing a community answers about an event is evidence of anything but what the
community said (VTI-EVT-002).

### Scope and standing

**VTI-CAL-001** — A VTA MUST keep calendar entries, settings, reminders, receipts
and feeds per trust context: each belongs to the context the identifier it
concerns belongs to. A VTA MUST NOT disclose an entry, a setting, a receipt or a
feed's content held in one context to a party without authority in that context
(VTI-NTF-051).

**VTI-CAL-002** — A listing across contexts MUST answer only for the contexts the
caller has authority in, and MUST NOT say that other contexts hold anything — not
by a count, an error or an omission marked as such.

*Rationale.* A principal keeps personas apart by keeping them in different trust
contexts. A calendar that joined them for any client would undo that the first
time a client with standing in one context asked what the week holds.

### Reading the member's events

**VTI-CAL-010** — A VTA MUST read a community's events only where its principal
has chosen to, for that trust context, and MUST keep that choice until the
principal changes it. Where the principal has not chosen, the VTA MUST NOT read.

**VTI-CAL-011** — A VTA MUST read a community's events for the principal only
with the community's operation for the caller's own registrations
(`vtc/events/mine/list`), signed as the identifier the principal holds their
membership of that community under, and MUST NOT sign such a read as any other
identifier it holds.

**VTI-CAL-012** — A VTA SHOULD read incrementally, asking only for what changed
since the last read it completed for that community, and MUST keep each
community's answer apart from every other community's.

**VTI-CAL-013** — A VTA MUST replace what it holds of an event with a later
answer about it, and MUST NOT keep an earlier answer as the event's current
state. An answer stating a higher sequence number for the event (VTI-EVT-006)
replaces any reminders computed from the earlier one.

**VTI-CAL-014** — A VTA MUST NOT read a community's events in a way that tells
the community more than the reading member already holds there: it reads only
the member's own registrations, and only as the member.

*Rationale for VTI-CAL-011 and VTI-CAL-014.* A member who joined two communities
under two identifiers did so to keep them apart. A VTA that read one community's
events as the other identifier would hand the first community the link the
member chose not to make. Reading the member's own registrations as the member
tells the community nothing new: it already knows who registered.

### What the calendar shows

**VTI-CAL-020** — A VTA MUST show an event in one of two tiers. An event the
principal holds a registration for, read under VTI-CAL-011, is **mine** and MAY
be shown in full, as the community answered. An event the VTA holds only an
`event` notification for is **notified** and MUST be shown only as the
notification states it — its community, identifier, audience and period — until
the principal opens it (VTI-NTF-067).

**VTI-CAL-021** — A VTA MUST NOT open a notified event of its own accord, and MUST
NOT read a community's event, other than under VTI-CAL-011, except at the choice of
the principal or of a party with authority in the context (VTI-NTF-053).

**VTI-CAL-022** — A VTA MUST show a notification that was withdrawn, or whose
event the community answered as cancelled, as withdrawn or cancelled, and MUST
NOT show it as an event the principal may still come to.

**VTI-CAL-023** — A VTA MAY mark entries whose times overlap. It MUST mark only
overlaps among entries the caller may see under VTI-CAL-002.

*Rationale for VTI-CAL-021.* Opening an event is a read the community sees: from a
member, about an event it invited them to. That is the member telling the
community they were interested. A calendar that opened every invitation to show
its title would tell every inviting community, for every member, that they
looked.

### Receipts

**VTI-CAL-030** — A VTA MUST keep, exactly as received, each signed reply of a
community to a registration of its principal, a change or withdrawal of one, a
host's decision on one, and an answer to an invitation (VTI-EVT-092), that reaches
it for an identifier it holds keys of. It MUST verify that the reply's proof is
the community's, made with an `operational` key of the community, before keeping
it, and MUST refuse one that it cannot verify or that is addressed to an
identifier it does not hold.

**VTI-CAL-031** — A client acting for a principal that receives such a reply for
an identifier whose keys the principal's VTA holds SHOULD hand it to that VTA
exactly as received. VTI-NTF-054's rules on what the client may do with it
apply.

**VTI-CAL-032** — A VTA MUST keep a receipt after the principal leaves the
community it came from, after the event ends, and after the community stops
answering, until the principal deletes it.

*Rationale for VTI-CAL-030 – VTI-CAL-032.* A reply that states what was agreed to is
evidence only while someone holds it. The community has no reason to keep it for
the member, and may not exist when the member needs it. Keeping it as received is
what lets anyone later check it against the community's key, as for a held
notification (VTI-NTF-050).

### Reminders

**VTI-CAL-040** — A VTA that keeps a calendar MUST be able to remind its principal
of a **mine** event the principal is going to, or has said maybe to. It MUST NOT
remind of a notified event, of a registration withdrawn, declined or removed, or
of a cancelled event.

**VTI-CAL-041** — A VTA MUST compute a reminder's time from the event's start as
the community last answered it, at the offsets the principal set for that event;
where the principal set none, at the offsets the event suggests (VTI-EVT-040);
where the event suggests none, at the principal's default for the context. An
offset the principal set to none removes the reminders.

**VTI-CAL-042** — A VTA MUST NOT fire a reminder in a period of quiet the principal
set for the context. A reminder due in one MUST fire when it ends, or not at all
if the event has started by then.

**VTI-CAL-043** — A VTA MUST deliver a reminder to the principal's devices so that
nothing passing through a party other than the VTA and the device — a mediator, a
platform push service — carries the event's title, place, community or any
identifier of the principal's. The device MUST read what the reminder is about
from the VTA.

*Rationale for VTI-CAL-043.* A push service sees every notification it carries and
when. A reminder whose text names the event tells it where the principal will be,
an hour before they are there. A wake with no content tells it only that the
device has something to read.

### Feeds for calendar applications

**VTI-CAL-050** — A VTA MAY serve a principal's calendar to calendar applications
as an iCalendar (RFC 5545) subscription. Each such **feed** MUST be created by a
catalogue task with standing in every context it covers, and MUST be served as a
subscription interface (VTI-OPS-111 – VTI-OPS-113).

**VTI-CAL-051** — A feed's address MUST carry at least 256 bits of randomness, MUST
be returned once, when the feed is created, and MUST NOT be recoverable from what
the VTA keeps. A VTA MUST keep it only as a digest.

**VTI-CAL-052** — A feed MUST state, at creation, the contexts it covers, what it
discloses of each entry, and when it expires; it MUST expire, and a VTA MUST NOT
accept an expiry more than a year after creation. A feed MUST be revocable at any
time by a party with standing in every context it covers, and a revoked or
expired feed MUST be answered as a feed that never existed.

**VTI-CAL-053** — Unless the feed states otherwise at creation, a feed MUST carry
for each entry only its title and its times. A feed MUST NOT carry access details
(VTI-EVT-060), registrants, answers, receipts, or any identifier of the principal;
a feed's iCalendar `UID`s MUST be derived from the feed and the event, and MUST
NOT be stable across feeds. A notified entry MUST appear, if at all, only as busy
time with no title.

**VTI-CAL-054** — A VTA MUST record, for each feed, when it was last read and how
many times, and MUST show both to a party with standing in every context the feed
covers. It MUST NOT record what was read or from where beyond what VTI-OPS-113
requires.

**VTI-CAL-055** — A feed MUST stop covering a context when the party that created
it loses standing in that context.

*Rationale for VTI-CAL-050 – VTI-CAL-055.* A calendar application can only fetch a
URL, again and again, from wherever its provider runs it. The URL is therefore a
key held by a third party: whoever runs the application, whoever reads its logs,
whoever is shown the screen. Each rule narrows what that key opens and for how
long — one purpose, titles and times, no identifier that follows the member
between feeds, an end date, and a way to see that it is being used.

### Busy times

**VTI-CAL-060** — A VTA MAY share its principal's busy times with a party that
offers bookings (a booking page), only where the principal has chosen to share
with that party, for that context, and MUST stop when the principal withdraws the
choice.

**VTI-CAL-061** — What a VTA shares as busy time MUST be intervals only: merged,
widened to whole quarter hours, and stating no title, place, community, context
or count. A VTA MUST NOT share busy time more than ninety days ahead.

*Rationale.* Someone booking time with a member needs to know when the member is
free, not why they are not. A merged, coarsened interval answers the first and
nothing else; the same interval at minute precision, or one per event, starts to
answer the second.

### Settings and audit

**VTI-CAL-070** — A VTA MUST let a party with authority in a context read and
change its calendar settings: whether it reads communities' events (VTI-CAL-010),
the default reminder offsets, and the periods of quiet. A VTA MUST record each
change with the party that made it.

**VTI-CAL-071** — A VTA MUST audit the creation and revocation of a feed, a change
of a sharing choice (VTI-CAL-060), and a change of settings, and MUST NOT audit an
entry's title, a receipt's content or a reminder's text.

### Operations

*This section is informative.* The catalogue tasks that carry this chapter are
the `vta/calendar/*` family:

| Task | Requirements |
|---|---|
| `vta/calendar/list` | VTI-CAL-001, 002, 020 – 023 |
| `vta/calendar/sync` | VTI-CAL-010 – 014 |
| `vta/calendar/settings/show`, `settings/update` | VTI-CAL-010, 041, 042, 070 |
| `vta/calendar/alerts/set`, `alerts/clear` | VTI-CAL-040 – 042 |
| `vta/calendar/receipts/deposit`, `receipts/list` | VTI-CAL-030 – 032 |
| `vta/calendar/export` | VTI-CAL-030, 053 |
| `vta/calendar/feeds/create`, `feeds/list`, `feeds/revoke` | VTI-CAL-050 – 055 |
| `vta/calendar/busy/share` | VTI-CAL-060 – 061 |
