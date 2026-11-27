## Community Events

This section is normative.

### In plain terms

A community holds a workshop on a Monday evening. Twelve places, a question about
dietary needs, and a code of conduct everyone agrees to first. Members of the
electronics group see it in the community's calendar; the rest of the community
does not. A member registers, brings a friend, and changes their mind on Sunday;
the place they free goes to the first person waiting for one. Nobody coming sees
who else is coming, because the host chose that before anyone registered, and the
host cannot change it afterwards. The address and the door code reach only the
people who have a place.

The event lives at the community, and nowhere else. A member's own agent asks the
community for the events they are going to, alongside the events of every other
community they belong to, and reminds them itself, so that no community learns
who wanted to be reminded. What the community answers each time a member
registers or withdraws is signed, and the member keeps it.

### What this chapter defines

This chapter specifies how a community keeps **events**: things that happen at a
time, which members of the community register for, withdraw from and are reminded
of, and **calendars** that hold them.

A VTC is not required to offer events. A VTC that offers them MUST satisfy the
requirements of this chapter; a VTC that does not is not bound by them. A VTC
offering events MUST offer the community directory and groups (The Community
Directory and Groups chapter), whose entry identifiers, groups and dynamic-group
rules this chapter uses.

The chapter adds no authority model. Being registered for an event, or having
attended one, is a fact shown to people; it is not a role, a capability or a
membership, and nothing an event says grants, widens or removes any authority
(VTI-VTC-020).

The capability this chapter refers to is registered in Appendix C.3:
`vtc.events.manage`, the capability to run the community's calendars and the
events in them, qualified by group or by event.

Throughout this chapter:

- an **event** is something that happens at a time, of kind `event` or
  `meeting`;
- a **calendar** is the community's own, or one group's, set of events;
- a **host** of an event is a member who runs it;
- a **registrant** is a party with a registration for an event, and a
  **registration** is that party's standing with it;
- the **audience** of an event is the set of members who may see it and register;
- the **roster** of an event is the set of its registrations, and its **roster
  setting** who other than its hosts may see it;
- an event's **access details** are where it happens and how to join it.

### Events

**VTI-EVT-001** — An event MUST be held by the community that runs it. It MUST
have an identifier unique within the community and never reused. A party's agent
that shows a member an event MUST read it from the community that holds it, and a
VTC MUST NOT hold a copy of another community's event as its own.

**VTI-EVT-002** — A VTC MUST NOT read an event, a registration, or a member's
attendance in deciding whether any subject is authorized (VTI-VTC-020,
VTI-ACL-070).

**VTI-EVT-003** — An event MUST be of kind `event` or `meeting`, fixed for its
life. A meeting is an event that may also carry what a meeting needs to decide
things; an operation that only means something for a meeting MUST refuse an event.

**VTI-EVT-004** — An event MUST be `draft`, `published` or `cancelled`. A draft
MUST be shown only to its hosts and MUST NOT take registrations. A cancelled
event MUST NOT take registrations and MUST be kept, so that its registrants and
their agents see that it was cancelled. Whether a published event has ended MUST
be read from its time and MUST NOT be stored as a status.

**VTI-EVT-005** — An event's time MUST be stated as a local start date and time
with no offset, an IANA time zone in which it is read, and a duration, as
JSCalendar (RFC 8984) states them. A VTC MUST refuse a time zone its time zone
database does not know. An all-day event MUST start at midnight local time and
last whole days.

**VTI-EVT-006** — A VTC MUST keep a sequence number on each event and MUST
increase it whenever the event's time, its access details, its status or its kind
changes after it is published, so that a registrant's agent can tell an event it
holds has changed in a way the registrant must notice.

**VTI-EVT-007** — A community MAY turn events off. While events are off, a VTC
MUST answer every operation of this chapter as if no calendar and no event
existed, and MUST keep every record.

*Rationale for VTI-EVT-001.* A member belongs to several communities, and an
agent that gathered their events by copying them into a shared place would become
the place that knows every community a member belongs to and every evening they
are out. Reading each event from its own community keeps what any one party
learns to what it already holds.

*Rationale for VTI-EVT-005.* An event stored as an instant drifts from what its
host chose when a country changes its daylight-saving rules between the day it is
scheduled and the day it happens. A local time and a zone is what the host meant;
the instant is worked out when it is read. JSCalendar's member names are used so
that conversion to and from iCalendar is the published mapping, not a new one.

*Rationale for VTI-EVT-006.* A registrant decided to come to the event as it
was. A change of title is not a reason to reconsider; a change of day or place
is. The sequence number marks only the second kind, as the SEQUENCE property
does in iCalendar scheduling (RFC 5546).

### Calendars

**VTI-EVT-010** — Every event MUST belong to exactly one calendar. A calendar
MUST have an identifier unique within the community and never reused.

**VTI-EVT-011** — A VTC offering events MUST have one calendar of the
community's own, which MUST always exist and MUST NOT be archived. A VTC MAY
create it when it is first needed.

**VTI-EVT-012** — A calendar MAY belong to one group. A group MUST NOT have more
than one calendar that is not archived.

**VTI-EVT-013** — Creating or changing a calendar, and creating an event in it,
MUST require:

- for the community's own calendar, `vtc.events.manage` unqualified;
- for a group's calendar, `vtc.events.manage` unqualified or qualified by that
  group, or `vtc.groups.manage` for that group, so that a group's managers run
  their group's calendar without an administrative role.

**VTI-EVT-014** — An archived calendar MUST NOT take new events. Its events MUST
be kept and MUST remain readable to the parties that could read them before.

### Audience

**VTI-EVT-020** — An event's audience MUST be stated as a scope — every active
member, the members of any of a list of groups, or the holders of any of a list
of roles — and MAY be narrowed by a rule in the dynamic-group rule language,
under VTI-DIR-072. A rule on an event MUST NOT read the counts VTI-BDG-015
reserves to badge groups. A member is in the audience when the scope admits them
and, where there is a rule, the rule does too. An event's hosts MUST be treated as
in its audience. A VTC MUST refuse an audience naming a group that does not
exist.

**VTI-EVT-021** — A VTC MUST decide whether a member is in an event's audience
when the member reads the event or registers for it, from its records at that
time. It MUST NOT treat a notification, an earlier answer, or a registration made
before the audience changed as evidence that the member is in the audience
(VTI-NTF-004).

**VTI-EVT-022** — An event MAY be unlisted. A VTC MUST leave an unlisted event out
of every listing of events, except for its hosts and for its own registrants,
and MUST answer a read of it by identifier to a member of its audience.

**VTI-EVT-023** — A VTC MUST answer alike a read of, or an operation on, an
event that does not exist and one the caller is not entitled to see, so that the
answer does not reveal that an event exists.

*Rationale for VTI-EVT-020.* A community badge is a group (VTI-BDG-010), so "the
members of these groups" also covers "the holders of this badge", and the
dynamic-group rule already reads every verified fact a community will let decide
who belongs: tenure, role, a credential-sourced field. An event inventing its own
eligibility language would be one more place to get that wrong.

*Rationale for VTI-EVT-021.* Membership changes while an event is open. A member
who left the electronics group on Tuesday is not in Thursday's workshop's audience
on Wednesday, whatever they were told on Monday.

### Registration

**VTI-EVT-030** — A party MUST have at most one registration for an event. A
registration MUST have an identifier unique within the community. Registering
again for an event MUST change the existing registration.

**VTI-EVT-031** — An event MUST take registrations in one of three modes:
`open`, in which a registration from a member of the audience is accepted at
once; `approval`, in which it waits for a host's decision; and `closed`, in which
the event takes none.

**VTI-EVT-032** — A registration MUST be in one of these states: `going`, with
a place; `maybe`, holding no place; `pending`, awaiting a host's decision;
`waitlisted`, waiting for a place in order; `withdrawn`, by the registrant;
`declined`, by a host deciding a pending registration; `removed`, by a host.

**VTI-EVT-033** — Where an event states a capacity, the places taken MUST be the
registrations that are `going` together with the guests each brings, and a
registration MUST NOT be made `going` if that would exceed it. Where the event
keeps a waitlist, a registration that does not fit MUST be `waitlisted`, in the
order it was made; otherwise it MUST be refused. When a place frees, a VTC MUST
make `going`, in order, each waitlisted registration that then fits. A
registration that joins a waitlist is the registrant's consent to that. A host's
approval of a pending registration MUST obey this requirement as a new
registration does.

**VTI-EVT-034** — An event MAY ask registrants questions. A VTC MUST refuse a
registration without an answer to a question the event marks required, and MUST
show a registrant's answers only to that registrant and to the event's hosts.

**VTI-EVT-035** — An event MAY require registrants to acknowledge a code of
conduct, named by its location and the digest of its text. A VTC MUST refuse a
registration that does not acknowledge the digest the event names, and MUST
record on the registration the digest acknowledged. A change to the code of
conduct MUST NOT change what an existing registration acknowledged.

**VTI-EVT-036** — An event MAY state what it offers for access — step-free
access, captions, a hearing loop, a quiet space, a way to take part remotely —
as its hosts' statement. A VTC MUST show it to everyone who may see the event.

**VTI-EVT-037** — A registrant MUST be able to withdraw their own registration
at any time: before the event, after its registration window has closed, and
after it is cancelled. Withdrawal MUST NOT be parked for consent or held for a
decision. Withdrawing a registration that was `going` frees its places
(VTI-EVT-033).

**VTI-EVT-038** — An event MAY state when registration opens and closes. Outside
that window a VTC MUST refuse a registration and a change to one, and MUST still
accept a withdrawal (VTI-EVT-037). Where the event states no close, registration
MUST close when the event starts.

**VTI-EVT-039** — When a member departs the community, a VTC MUST end each of
their registrations, freeing their places (VTI-EVT-033).

*Rationale for VTI-EVT-033.* Asking every waitlisted member to come back and
claim a freed place makes the place go to whoever is watching, not whoever asked
first. Joining a waitlist says "give me a place if one frees"; doing so is
following the registrant's own instruction.

*Rationale for VTI-EVT-037.* Being able to leave is what makes registering safe.
A registrant who finds that they cannot withdraw, because a window has closed or
a host must agree, has been made to attend, or to be listed as attending, by a
form.

### Reminders

**VTI-EVT-040** — A VTC MUST NOT send reminders of an event to its registrants.
An event MAY state reminders its hosts suggest, as durations before its start,
for each registrant's own agent to apply as the registrant chooses.

*Rationale.* A community that sends reminders learns who wanted to be reminded,
and sends something per registrant at a predictable time, which says who is
going to anyone watching the traffic. The registrant's agent already holds the
event; firing the reminder there tells nobody anything.

### The roster

**VTI-EVT-050** — An event MUST have one of two roster settings: `visible`, in
which registrants who are `going` or `maybe` may see one another; or `hidden`, in
which no registrant may see another.

**VTI-EVT-051** — A VTC MUST fix an event's roster setting when the event is
published, and MUST refuse a change to it afterwards. It MUST NOT park such a
change for consent or approval.

**VTI-EVT-052** — A VTC MUST record on each registration the roster setting in
force when it was made, and MUST show the roster setting to every party who may
register, before they do.

**VTI-EVT-053** — Under `visible`, a VTC MUST show a registrant who is `going`
or `maybe` the other registrations that are `going` or `maybe` — their entry
identifier, state and number of guests — and nothing else of them. Under
`hidden`, it MUST show a registrant no registration but their own. In either
setting a VTC MUST show the whole roster, in every state, to the event's hosts,
and MUST NOT show any party but the registrant and the hosts a registrant's
answers.

**VTI-EVT-054** — A VTC MUST show the event's hosts the exact number of places
taken. It MAY show the number to the rest of the audience, where the event says
so, and then MUST show it as a number only when it is at least the event's
threshold (5 unless the event states another), and as "fewer than" the threshold
otherwise. Whether the event is full MUST be shown to every party who may
register.

**VTI-EVT-055** — A VTC MUST NOT let a registrant leave themselves off a
`visible` roster.

*Rationale for VTI-EVT-051 and VTI-EVT-052.* The roster setting is a promise
made to people before they decide to register. An event that could be made
`visible` after people registered under `hidden` would show them to everyone
on terms they never agreed to. A host who needs the other setting cancels and
starts again, and each registrant decides afresh.

*Rationale for VTI-EVT-054.* A count of two, watched as it changes, tells anyone
who knows who might have registered exactly who did. Below a threshold, the
count says only that it is small.

*Rationale for VTI-EVT-055.* A `visible` roster is a promise to the people on it
that they can see who else is coming. A roster that silently omits people breaks
that promise to everyone else. A member who does not want to be recognised at an
event chooses which of their identities registers.

### Access details

**VTI-EVT-060** — A VTC MUST show an event's access details only to its hosts and
to registrants whose registration is `going`, and, where the event states a time
from which they are released, not before that time to any party but its hosts.

**VTI-EVT-061** — A VTC MUST NOT put an event's access details in a listing of
events, in a read of the event, or in anything it publishes, unless the event's
hosts have marked them public.

**VTI-EVT-062** — An event MAY state a coarse summary of where it happens, which
a VTC shows to its whole audience. A VTC MUST NOT accept an address as that
summary where it can tell that it is one, and the summary MUST NOT be relied on
as access details.

**VTI-EVT-063** — A change to an event's access details after it is published
MUST increase its sequence number (VTI-EVT-006).

*Rationale for VTI-EVT-060.* Where a small event happens, and when, is the most
sensitive thing about it: it says where a group of people will be. Giving it
only to those who have a place keeps the event's existence and its door apart.

### Hosts

**VTI-EVT-070** — The member who creates an event MUST be its first host. A host
of an event MAY appoint another active member a host of that event. An
appointment MUST be `vtc.events.manage` qualified by that one event and nothing
else, written as a grant on the appointee's access control entry that names its
appointer (VTI-ACL-070, VTI-ACL-072), beside any role the entry holds and never
inside it, so that a member who holds no administrative role MAY be appointed.

**VTI-EVT-071** — A VTC MUST refuse an appointment its appointer does not cover
when it is written (VTI-ACL-071), and one of a party who is not an active member
or already hosts the event.

**VTI-EVT-072** — A host MAY end their own appointment, and a party with the
authority of VTI-EVT-013 over the event's calendar MAY end any host's. A VTC MUST
refuse to end the last host's appointment while the event is published.

**VTI-EVT-073** — The hosts of an event MAY change it, publish it, cancel it,
delete it while it is a draft, decide its pending registrations, and remove a
registration. A host deciding or removing a registration MAY give a note, which a
VTC MUST show to the registrant.

**VTI-EVT-074** — A VTC MUST withdraw every appointment to an event when the
event is deleted, and every appointment a member holds when they depart. When an
appointment's appointer departs, a VTC MUST put the appointment to the
community's administrators for review as VTI-BRD-092 requires of a board's
facilitators.

### Policy and reports

**VTI-EVT-080** — A community MAY keep a policy over events, evaluated when an
event is created, changed or published. Such a policy MUST only be able to refuse
an operation this chapter would otherwise allow; it MUST NOT allow one this chapter
refuses, and MUST NOT widen an audience or a roster setting.

**VTI-EVT-081** — A member of an event's audience MAY report the event to the
community's administrators, with a reason. A VTC MUST keep at most one open
report per member per event, MUST put each report before the administrators who
hold `vtc.events.manage` unqualified, and MUST NOT show who reported an event to
any party but those administrators.

**VTI-EVT-082** — A VTC MUST apply the bounds of VTI-TRN-029 to every operation
of this chapter.

### Retention, receipts and audit

**VTI-EVT-090** — A VTC MUST delete registrants' answers no later than a period
the community states after the event ends (30 days unless the community states
another), and MAY keep the registrations' states.

**VTI-EVT-091** — A VTC MUST audit the creation, publication, change,
cancellation and deletion of an event; every registration, change of a
registration and withdrawal; every host decision and removal; and every
appointment and its end. An audit record MUST name these by reference and MUST
NOT carry a registrant's answers.

**VTI-EVT-092** — A VTC's signed reply to a registration, a change of one, a
withdrawal, and a host's decision MUST state the event's identifier, title,
time, status and sequence number as they stood when it answered, together with
the registration's state and the roster setting it was made under, so that the
reply serves the registrant as a receipt.

**VTI-EVT-093** — A VTC MUST NOT delete a published event. A published event that
will not happen is cancelled (VTI-EVT-004).

*Rationale for VTI-EVT-092.* A Trust Task reply is already a signed document. A
reply that names what was agreed to, and when, is evidence the registrant can
keep in their own agent and show later, without the community having to keep, or
still exist to answer for, a record of its own.

### Members' acts and the portal

**VTI-EVT-095** — A member's acts under this chapter — reading calendars and
events, registering, changing and withdrawing a registration, reading access
details, reading their own registrations, and reporting an event — MUST each be a
document signed as the member. A member portal that holds a key the member
authorized to sign for them for this chapter's tasks (`auth/oob` `signingScope`)
MAY sign them with it, for the session that authorization arms, as VTI-BRD-050
allows for a board. A VTC MUST NOT accept such a key for creating, changing,
publishing, cancelling or deleting a calendar or an event, for deciding or
removing a registration, or for appointing or ending a host.

### Operations

*This subsection is informative.* The operations of this chapter are the
`vtc/events/*/0.1` Trust Tasks:

| Operation | Requirements |
|---|---|
| `vtc/events/calendars/create`, `calendars/update`, `calendars/list` | VTI-EVT-010 – 014 |
| `vtc/events/create`, `update`, `publish`, `cancel`, `delete` | VTI-EVT-001 – 006, 013, 020, 050 – 051, 073, 080, 093 |
| `vtc/events/show`, `list` | VTI-EVT-020 – 023, 054, 061 |
| `vtc/events/mine/list` | VTI-EVT-092, 095 |
| `vtc/events/access/show` | VTI-EVT-060 – 061 |
| `vtc/events/registrations/register`, `withdraw` | VTI-EVT-030 – 039, 052, 092 |
| `vtc/events/registrations/list`, `decide`, `remove` | VTI-EVT-033, 053, 073 |
| `vtc/events/hosts/appoint`, `remove`, `list` | VTI-EVT-070 – 074 |
| `vtc/events/report` | VTI-EVT-081 |

### Later phases

*This subsection is informative.* These are designed but not yet specified; a
later revision of this chapter states them normatively:

- **Invitations and the `event` notification kind**: inviting by audience rule,
  re-evaluated as membership changes, with notifications that name the event and
  its period and carry nothing else (VTI-NTF-003); invitation links; changes and
  cancellations as revisions and withdrawals; relay to partner communities.
- **The member's calendar** (`vta/calendar/*`): a member's VTA reading their
  events from every community they belong to, firing their reminders, keeping the
  receipts of VTI-EVT-092, and serving them as calendar feeds.
- **Check-in and tickets**: check-in by a nonce the member's wallet answers;
  ticket credentials checked by OpenID4VP; attendance counted for badges.
- **Anonymous and persona registration**: registration by a zero-knowledge proof
  of membership and a per-event tag, on the predicate credential system the
  community runs for hidden vetting.
- **Meetings**: agenda, chair, roll call, quorum, motions decided by the
  community's elections, minutes, action items.
- **Recurrence**: series of events, with changes to one occurrence.
- **An event's own identity**, **guests who are not members**, and **events
  hosted by a person** from their own VTA.
