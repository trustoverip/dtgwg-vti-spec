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

The capabilities this chapter refers to are registered in Appendix C.3:
`vtc.events.manage`, the capability to run the community's calendars and the
events in them, qualified by group or by event; and `vtc.events.checkin`, an
event's door.

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
- a **guest** is a registrant who is not a member of the community;
- a **check-in** is the record that a registrant came, and **door staff** those
  who record it.

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
which no registrant may see another. Where a community offers it, an event MAY
instead be `anonymous` (VTI-EVT-210), which is `hidden` and more.

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

### Invitations

**VTI-EVT-100** — An event MUST state how it invites its audience: `none`, in
which members of the audience find it themselves; `notify`, in which the VTC
invites each member of the audience; or `expect`, in which it invites them and
records that a reply is expected. An invitation MUST NOT register anyone: an
invited member is `going`, `maybe` or neither only by their own act
(VTI-EVT-104).

**VTI-EVT-101** — Under `notify` or `expect`, a VTC MUST invite each member of
an event's audience once the event is published, and each member who comes into
the audience afterwards, while the event is published and has not ended. It
MUST NOT invite a member who holds a registration for the event, who has
already been invited to it, or who has opted out under VTI-EVT-110.

**VTI-EVT-102** — A VTC MUST invite by sending the member a notification of the
kind `event` (VTI-NTF-064), and MUST record the invitation before it sends
anything. A member MUST have at most one invitation to an event. A VTC MUST
send each invitation from one place that retries until the notification is
delivered or lapses; a task MUST NOT send one itself.

**VTI-EVT-103** — An event's hosts MAY invite members by name. A member a host
invites MUST be treated as in the event's audience while the invitation stands,
so that a host can hold an event only the members they invite may see. A host
MAY revoke an invitation they or another host made; revoking it withdraws its
notification (VTI-NTF-065) and leaves any registration the member made.

**VTI-EVT-104** — An invited member MUST be able to answer their invitation:
`going` or `maybe`, which registers them as VTI-EVT-030 – VTI-EVT-038 require,
or `decline`, which records on the invitation that they will not come and
registers nothing. Declining an invitation is not a host declining a
registration (VTI-EVT-032) and MUST NOT be shown as one. A member who declined
MAY register later, as any member of the audience may.

**VTI-EVT-105** — An event MAY let members invite other members of its
audience. A VTC MUST then accept an invitation from any member of the audience,
MUST NOT let a member's invitation admit anyone the audience does not
(VTI-EVT-020), and MUST limit the invitations one member makes in a day to a
number the community sets.

**VTI-EVT-106** — Hosts MUST be able to list an event's invitations: who was
invited, by whom (a host, a member, or the audience under VTI-EVT-101), and
each invitation's answer. Under `expect` the list MUST show who has not
answered. A member MUST be able to list their own invitations. No other party
may list them.

**VTI-EVT-107** — An event's hosts MAY make invitation links: a trigger link
(VTI-LNK) whose code, once redeemed by an active member of the community,
invites that member as VTI-EVT-103 does. A VTC MUST keep only a digest of the
code, MUST refuse a code past the expiry, or beyond the number of redemptions,
its hosts set, and MUST refuse a revoked one. A code MUST NOT be redeemed twice
by the same member, and a refusal MUST NOT say which of these it was.

**VTI-EVT-108** — Changing an event's invitation mode from `none` to `notify` or
`expect` on a published event MUST invite its audience as VTI-EVT-101 does.
Changing it to `none` MUST NOT withdraw invitations already sent.

*Rationale for VTI-EVT-100 and VTI-EVT-104.* A meeting the whole committee is
expected at is different from an evening anyone may drop in on, and the
difference is whether someone is waiting for an answer. It is never whether the
member is registered: a member is listed as coming only by saying so.

*Rationale for VTI-EVT-102.* An invitation sent from a request's handler is
sent once, or twice, or not at all, depending on where a crash falls. A record
written first, and one sender that reads it, sends each exactly once and can say
which were sent.

*Rationale for VTI-EVT-105.* "Bring a friend who is in the community" is an
invitation; "bring a friend the community has not admitted to this event" is a
widening of its audience by someone who does not run it.

### Opt-outs

**VTI-EVT-110** — A member MUST be able to opt out of invitations to one event or
from one calendar, and to withdraw an opt-out. A VTC MUST NOT invite a member
under VTI-EVT-101 to an event an opt-out covers. An opt-out MUST NOT remove the
member from any audience, and MUST NOT stop a host's invitation by name
(VTI-EVT-103), which the member may decline.

*Rationale.* An audience rule re-evaluated as membership changes reaches members
who never asked to hear about a group's every meetup. The member, not the rule,
decides whether they hear.

### Announcements

**VTI-EVT-120** — An event's hosts MAY post announcements to it. A VTC MUST let
the event's hosts, and its registrants who are `going`, `maybe`, `pending` or
`waitlisted`, read its announcements, and no one else.

**VTI-EVT-121** — An announcement MUST NOT change the event's time, place or
status; a host changes those by changing the event (VTI-EVT-006). A VTC MUST
mark the event changed for its registrants' agents when an announcement is
posted, and MUST NOT carry an announcement's text in any notification.

*Rationale.* An announcement is how a host says "bring a laptop" or "we start
fifteen minutes late". If it could move the event in words, a member's calendar,
which reads the event's time and not its prose, would be wrong.

### Partner communities

**VTI-EVT-130** — An event's audience MAY name partner communities: communities
that are members of this one, or with which it holds a relationship in force. A
VTC MUST refuse a partner that is neither.

**VTI-EVT-131** — Once an event naming a partner is published, a VTC MUST send
the partner a notification of the kind `event` (VTI-NTF-064) whose audience is
the partner's identifier. The partner decides whether and to whom to relay it
(VTI-NTF-071). The VTC MUST NOT send anything to the partner's members, whom it
does not know.

**VTI-EVT-132** — A party who is not a member of the community is in an event's
audience, for that request only, when the request carries a presentation of a
membership credential that a partner the audience names issued to them, made by
the credential's subject who signs the request and bound to it, verified as
VTI-VOT-016 verifies a presentation for an election, valid and unrevoked when
presented.

**VTI-EVT-133** — A VTC MUST verify such a presentation before, and without
reference to, the event the request names, MUST use its outcome only for an
event whose audience names the credential's issuer, and MUST discard the
presentation once it has decided. Without a presentation that passes, an event
the party may not otherwise see MUST be answered as VTI-EVT-023 requires.

**VTI-EVT-134** — A registration by a party who is not a member MUST name them
by their identifier, not a directory entry, and MUST be shown to the event's
hosts. It MUST NOT be shown by identifier to other registrants: a roster view
(VTI-EVT-053) counts such registrants without naming them.

**VTI-EVT-135** — The requirements on registration (VTI-EVT-030 – VTI-EVT-039)
apply to a partner's member as to a member, except that their departure from
the partner is learnt only when they next present (VTI-EVT-132), and a
registration they hold MUST NOT let them read the event without a presentation
that passes.

*Rationale for VTI-EVT-131 and VTI-EVT-132.* The community holding the event
does not know who the partner's members are and has no right to; the partner
does, and decides what its members hear. Each member then shows, when they open
the event, the one thing the holding community needs: that the partner says they
are a member.

*Rationale for VTI-EVT-133.* Verifying the presentation first, and the same way
whatever the event, keeps the verification from becoming a probe of which
events exist for which partner.

### An event's own identity

**VTI-EVT-140** — An event MAY have an identifier of its own, chosen when the
event is created and fixed for its life. An event without one MUST have every
artefact issued for it — a ticket (VTI-EVT-170), an attendance record — issued
by the community.

**VTI-EVT-141** — An event's identifier MUST be created by the VTA the community
is provisioned on, as a group's is (VTI-DIR-080), from a template of the kind
`vtc-event`. The VTC MUST NOT hold its keys, MUST obtain every artefact signed
under it by a signing request to that VTA, and MUST NOT record the event as
having an identifier before the identifier exists. The identifier MUST be
published where the community publishes the identifiers it mints for its
groups.

**VTI-EVT-142** — The community MUST authorize an event's identifier to issue
by an authorization credential, issued by the community to the identifier,
which is an attestation artefact of the community (VTI-KEY-080) with a status in
the community's revocation list. The community MUST revoke it when the event is
cancelled, deleted or archived, and a verifier MUST treat every artefact the
identifier issued as void from the time it is revoked.

**VTI-EVT-143** — An event's identifier document MUST NOT carry the event's
title, its description, its place or anything else that identifies it or its
registrants, and MUST NOT advertise a transport at which the community does not
serve it (VTI-KEY-050).

**VTI-EVT-144** — A series of events MUST have at most one identifier, shared by
every occurrence, and MUST NOT be given one per occurrence.

**VTI-EVT-145** — An event's identifier MAY sign the tickets and attendance
records of its event, and nothing else. A VTC MUST NOT read anything an event's
identifier signed in an authorization decision (VTI-EVT-002), and a grant held
at an event's identifier confers nothing beyond that event.

*Rationale for VTI-EVT-140 and VTI-EVT-144.* An identifier is created, published
and revoked; most events — a weekly meetup — need none, and one per occurrence
would leave a trail of identifiers to host for ever. An identifier earns its
keep where something outside the community must check what the event issued: a
door run by another party, a conference, an event co-hosted with partners.

*Rationale for VTI-EVT-142.* The authorization credential is what ties the
event's identifier to the community for a verifier who knows the community but
not the event; revoking it ends everything the event issued with one bit, as
deleting a group does (VTI-DIR-083).

### Guests

**VTI-EVT-150** — An event MUST state whether it admits guests — parties who are
not members of the community — as `none`, `invited` or `open`. Absent, it admits
none.

**VTI-EVT-151** — A guest MUST be a party identified by its own identifier,
signing its own requests. A VTC MUST admit a guest only under `invited`, when
they present the code of an invitation link made for guests (VTI-EVT-107), or
under `open`; MUST NOT let a link made for members admit a guest, nor one made
for guests admit a member; and MUST NOT let the audience (VTI-EVT-020) admit a
guest. A guest MUST be able to withdraw their registration as a member can
(VTI-EVT-037).

**VTI-EVT-152** — A guest MUST NOT become a member, appear in the directory, join
a group, or see any record of the community but the event they registered for,
its access details once they are going (VTI-EVT-060), its announcements
(VTI-EVT-120) and their own registration.

**VTI-EVT-153** — A guest's registration MUST name them by their identifier, MUST
count against the event's capacity as any registration does, and MUST be shown
to the event's hosts. It MUST NOT be shown by identifier to other registrants: a
roster view counts guests without naming them (VTI-EVT-134).

**VTI-EVT-154** — A VTC MUST charge every operation a guest sends to the bounds
for senders without standing (VTI-TRN-029), whatever the event, and MAY refuse
`open` by its events policy (VTI-EVT-080).

*Rationale for VTI-EVT-151.* The audience is a rule over what the community
knows of its members; a guest is someone it knows nothing about, so a guest
comes in only by a host's decision (`invited`) or the event's (`open`), never by
satisfying a rule written for members.

### Check-in

**VTI-EVT-160** — An event's hosts MAY appoint door staff: a capability
`vtc.events.checkin` qualified by the event. Door staff MUST be able to record
check-ins for that event and see the names its hosts see, and nothing else.

**VTI-EVT-161** — Door staff MUST obtain a **door challenge** from the community
for the event: a fresh random value of at least 128 bits, bound to the event,
that the community accepts for a stated short time and MUST NOT accept after it.
A VTC MUST issue a new challenge on each request and MAY accept several at once.

**VTI-EVT-162** — A registrant MUST check in by signing a request that carries a
door challenge the community issued for the event and that is still accepted.
A VTC MUST record the check-in only for a registration that is `going`, MUST
record at most one check-in per registration, and MUST refuse a challenge it did
not issue, one for another event, or one no longer accepted, with one refusal for
all three.

**VTI-EVT-163** — Door staff MAY check in a registrant by name. A VTC MUST record
who did so, and MUST distinguish that record from a registrant's own check-in.

**VTI-EVT-164** — A calendar MAY allow **self check-in** for its events: a
registrant checking in without a door challenge during the event. A VTC MUST
refuse a check-in without a door challenge for an event whose calendar does not
allow it.

**VTI-EVT-165** — A check-in MUST be recorded against the registration as
`attended`, and is shown to the event's hosts and door staff and to the
registrant. A VTC MUST NOT record, show or announce who did not check in.

**VTI-EVT-166** — Check-in records are registrants' records for VTI-EVT-090: a
VTC MUST delete them on the schedule it applies to answers, keeping only what
VTI-EVT-185 keeps.

*Rationale for VTI-EVT-161 and VTI-EVT-162.* A code printed on a screen at the
door proves nothing if it can be photographed and replayed; a challenge the
community issued moments ago, signed over by the registrant's own key, proves
that this registrant was in front of this door now. The single refusal keeps
the check-in from telling a stranger which challenges exist.

*Rationale for VTI-EVT-165.* A list of who stayed away is a list nobody agreed to
be on; recording only who came is enough for every purpose this chapter has.

### Tickets

**VTI-EVT-170** — An event MAY issue **tickets**: a credential issued to a
registrant when their registration becomes `going`, by the event's identifier
where it has one (VTI-EVT-140) and by the community otherwise, naming the
registrant's identifier as subject — their membership identifier, a partner
member's identifier (VTI-EVT-134) or a guest's (VTI-EVT-153) — and the event.

**VTI-EVT-171** — A ticket MUST be valid from its issue until the event ends, and
MUST NOT outlive it.

**VTI-EVT-172** — A ticket MUST carry a status in the community's shared status
set (VTI-CRD-014), never in a set of the event's own. A VTC MUST revoke a ticket
when its registration stops being `going` and when the event is cancelled.

**VTI-EVT-173** — A VTC checking a ticket at the door MUST request it as a
presentation with a fresh nonce per presentation, through the community's
OpenID4VP verifier, MUST verify the issuer, the subject's binding to the
presenter, the validity period and the status, and MUST record each ticket it
admits so that a ticket admits once.

**VTI-EVT-174** — A registrant MUST be able to read their own ticket. A VTC MUST
NOT show a ticket to anyone else.

*Rationale for VTI-EVT-170 and VTI-EVT-173.* The community can check a
registration live, so a door it runs needs no ticket. A ticket is for a door that
cannot ask the community — offline, or run by another party — and for a guest,
whom the community knows only by their identifier; it says, verifiably and
without a call home, that this subject is going to this event.

*Rationale for VTI-EVT-172.* A status list of one event's tickets would tell
anyone who checks a ticket how small the event is and which entries changed;
the community's shared set hides each ticket among all the community's
credentials.

### Attendance

**VTI-EVT-180** — An event MAY state that it does not count toward attendance;
absent, it counts. A calendar MAY set the default for its events.

**VTI-EVT-181** — A member's attendance MUST be counted only from check-ins
(VTI-EVT-165), never from registrations. A check-in without a door challenge
(VTI-EVT-164) MUST count only where the event's calendar allows self check-in to
count.

**VTI-EVT-182** — One event MUST count at most once for a member, however many
times they checked in, and every occurrence of a series is one event.

**VTI-EVT-183** — A guest's, a partner member's, a persona's or an anonymous
registration MUST NOT count toward any member's attendance.

**VTI-EVT-184** — A dynamic group's rule MAY read a member's attendance as a
count — events attended, optionally only in one calendar and only within a stated
period before the rule is evaluated — compared with a stated threshold, as a
badge group's rule reads the counts of VTI-BDG-015.

**VTI-EVT-185** — A VTC MUST keep, as the record attendance is counted from, the
number of counting events each member attended per calendar per month, and
nothing finer once the check-in records are deleted (VTI-EVT-166). A member MUST
be able to read their own counts.

**VTI-EVT-186** — An event MAY name an attendance badge. A VTC MUST offer it to a
member when their check-in is recorded, as an award they accept or decline
(VTI-BDG-020), and MUST NOT give it without their acceptance.

*Rationale for VTI-EVT-181 and VTI-EVT-182.* Attendance feeds badges, which are
public recognition; counting registrations would reward signing up, and counting
check-ins per session or per occurrence twice would let one evening count as
several.

*Rationale for VTI-EVT-185.* A monthly tally per calendar answers every rule
VTI-EVT-184 allows — a count, a calendar, a period of months — and keeps no
record of which evening anyone came.

### Recurring series

**VTI-EVT-190** — An event MAY be a series: one event that recurs by rule. Its
rule MUST be expressed as JSCalendar recurrence rules, limited to: a frequency of
daily, weekly, monthly or yearly; an interval; the days of the week (optionally
with an ordinal for a monthly or yearly series, such as the second Tuesday); the
days of the month; and an end, as a number of occurrences or a last date, never
both and never neither. A VTC MUST refuse any other rule part.

**VTI-EVT-191** — An occurrence MUST be identified by its original start: the
local date-time the rule gives it, in the series' time zone, before any change
to it. A series' own start MUST be its first occurrence. A VTC MUST expand a
series in its own time zone, so that a weekly event keeps its local time across
a change of daylight-saving time, and MUST resolve a local time the zone skips or
repeats as VTI-EVT-005 resolves one.

**VTI-EVT-192** — A VTC MUST expand a series only when it is read, for the window
the reader asks for, and MUST NOT store occurrences ahead of time. A VTC MUST
bound what one read expands, refusing a window longer than 400 days.

**VTI-EVT-193** — A host MAY change one occurrence — its start, duration, title,
description, place summary or access details — or cancel it, without changing
the others. A change MUST be kept against the occurrence's original start, and
MUST raise the series' `sequence` (VTI-EVT-006) where it changes the occurrence's
time, access details or status.

**VTI-EVT-194** — A series MUST state, fixed when it is published, whether a
registration is for the whole series or for one occurrence. A registration for
the whole series MUST count as a registration for every occurrence that has not
yet ended; a registration for one occurrence MUST name that occurrence by its
original start.

**VTI-EVT-195** — Capacity, the waitlist (VTI-EVT-033), access details
(VTI-EVT-060), check-in (VTI-EVT-160 – 166) and attendance (VTI-EVT-180 – 185)
MUST apply to each occurrence on its own: a registration for the whole series
holds a place in each occurrence, and each occurrence a member checks in to is
one event attended (VTI-EVT-182).

**VTI-EVT-196** — When an occurrence is cancelled, a VTC MUST keep every
registration for it, show the occurrence as cancelled to its registrants, release
no access details for it and count no attendance for it. A registration for the
whole series MUST be unaffected for every other occurrence. A withdrawal from a
cancelled occurrence MUST change nothing (VTI-EVT-037).

**VTI-EVT-197** — A host MAY change a series from one occurrence onward ("this
and following"). A VTC MUST then end the series before that occurrence and make
the change a new series that starts with it, linked to the first as its
continuation, and MUST carry every registration that concerns the new series'
occurrences into it with its state, its guests and its answers.

**VTI-EVT-198** — A VTC MUST state, in a notification of a series (VTI-NTF-064),
no period beyond the series' next occurrence, and MUST revise the notification
when that occurrence ends, moves or is cancelled.

*Rationale for VTI-EVT-191.* An occurrence that is moved still has to be the
same occurrence to the people registered for it; its original start is the one
name for it that a change does not alter, which is why JSCalendar and iCalendar
both key a change by it.

*Rationale for VTI-EVT-192.* A series with no end date is infinite. Storing its
occurrences ahead of time would either stop somewhere arbitrary or never stop;
expanding on read for a bounded window answers every reader exactly.

*Rationale for VTI-EVT-196.* Deleting the registrations of a cancelled occurrence
would tell their holders nothing and make the cancellation invisible in their own
calendars; keeping them, marked cancelled, is what a registrant expects to see.

### Anonymous registration

**VTI-EVT-210** — An event MAY have the roster setting `anonymous`, fixed when it
is published (VTI-EVT-051), only where the community runs a predicate credential
system [PCS] for its members, as it may for voting (VTI-VOT-020). Under `anonymous`,
a registration MUST name no member: a VTC MUST hold, for each registration, a tag
(VTI-EVT-213) and the registration's state and number of guests, and nothing that
identifies the registrant. The event's hosts and the community MUST see a count
only, as every other registrant does.

**VTI-EVT-211** — The audience of an `anonymous` event MUST be the community's
active members or the members of named groups (VTI-EVT-020), and nothing a member
cannot prove anonymously: a VTC MUST refuse an `anonymous` event whose audience
names roles, a rule, partner communities or guests.

**VTI-EVT-212** — A community offering anonymous registration MUST issue each
active member, by blind issuance, a class credential for the membership and one
for each group the member belongs to, under class labels shared by every holder —
one per class and period, never one per member — and MUST state the length of a
period. A member's agent MUST obtain them again for each period, and a VTC MUST
NOT issue a credential for a period to a party that is not then an active member
of the class. The key a member's agent holds for these credentials MUST be
derived for this purpose alone, never the key it holds for hidden vetting.

**VTI-EVT-213** — When an `anonymous` event is published, a VTC MUST make it an
identity in the predicate credential system, used for that event alone, and show
it with the event. A registration MUST carry an attestation, made by the member's
agent with a credential for a live class of the event's audience, to the event's
identity; its tag — determined by the member's key and the event's identity, the
same for one member at one event and unlinkable between events — MUST be the
registration's only identifier. A VTC MUST verify the attestation under its own
verification key, MUST check that its class is a live class of the event's
audience, and MUST keep one registration per tag.

**VTI-EVT-214** — An attestation MUST be bound to the operation it is sent with:
to the event, the operation's name, and the identifier and signer of the document
that carries it. A document carrying one MUST be signed by a key made for that
document alone, and SHOULD be sent from a connection that does not identify the
member, after a pause, as a ballot is cast (VTI-VOT-004). A VTC MUST refuse an
attestation that was carried before.

**VTI-EVT-215** — A registrant MUST change, withdraw from or check in to an
`anonymous` event with a fresh attestation carrying the same tag. A check-in's
attestation MUST also be bound to the door's challenge (VTI-EVT-161).

**VTI-EVT-216** — An `anonymous` event MUST NOT take registrations by approval,
MUST NOT ask questions, MUST NOT issue tickets, and MUST NOT name an attendance
badge; its registrations MUST NOT count toward any member's attendance
(VTI-EVT-183).

**VTI-EVT-217** — A VTC MUST refuse to publish an `anonymous` event unless the
number of members holding a credential for a live class of its audience in the
current period is at least the community's floor, which MUST NOT be below 10, and
MUST show that number with the event.

**VTI-EVT-218** — A VTC MUST show, with an `anonymous` event, the length of the
community's period, since a party that stops being a member keeps a credential
until its period ends.

*Rationale for VTI-EVT-211.* A member can prove anonymously only what a credential
they hold says; the community issues credentials for its membership and its
groups. A role or a rule over a member's records would have to be checked against
the member, which is what anonymity forbids.

*Rationale for VTI-EVT-213.* The tag gives the event exactly what it needs and no
more: the same member cannot register twice, because their second attestation
carries the same tag; their registration can be changed, because they can make
the tag again; and nothing ties it to another event, because the tag for another
event is unrelated to it.

*Rationale for VTI-EVT-214.* A registration sent over the member's own session, or
signed by a key the community knows to be theirs, would name them however
anonymous its contents. The binding to the document stops a captured attestation
being replayed for another operation.

*Rationale for VTI-EVT-217.* An anonymous registration hides one member among the
holders of the class. Among three, it hides little; the floor and the number shown
let every registrant judge how well hidden they are.

*Rationale for VTI-EVT-218.* Credentials are revoked by their period running out,
not one by one: a member who leaves can still register, anonymously, until their
period ends. Saying so is part of the event's promise.

### Persona registration

**VTI-EVT-230** — On an event that does not require identified registration, a
member MAY register under a persona identifier — any identifier of theirs other
than the one they are a member by — with an attestation as VTI-EVT-213 describes
it, so that one member cannot register twice under two personas. The roster and
the hosts MUST then show the persona identifier and never the member.

**VTI-EVT-231** — An event MAY require identified registration, fixed when it is
published; a VTC MUST then refuse a persona registration. A meeting whose quorum
counts members by role, and an event that expects a reply from its invitees
(VTI-EVT-101), SHOULD require it.

**VTI-EVT-232** — Persona registration MUST be refused where the event's audience
cannot be proven anonymously (VTI-EVT-211). A persona registration MUST NOT count
toward the member's attendance, and an attendance badge MUST NOT be offered to the
member for it.

*Rationale for VTI-EVT-230.* A member who does not want to be recognised at an
event, on a `visible` roster (VTI-EVT-055), registers as another of their
identities. The attestation proves that identity is a member's without saying
whose, and its tag stops one member taking two places.

### Operations

*This subsection is informative.* The operations of this chapter are the
`vtc/events/*` Trust Tasks (0.1, and 0.2 where invitations and partners changed
an event's or a registration's shape):

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
| `vtc/events/invitations/send`, `list`, `respond` | VTI-EVT-100 – 106 |
| `vtc/events/invitations/links/create`, `revoke`, `redeem` | VTI-EVT-107 |
| `vtc/events/optouts/set`, `list` | VTI-EVT-110 |
| `vtc/events/announcements/send`, `list` | VTI-EVT-120 – 121 |
| `vtc/events/show`, `registrations/register` 0.2 (a partner member's presentation) | VTI-EVT-130 – 135 |
| `vtc/events/create`, `update`, `show` 0.3 (identity, guests, tickets, attendance) | VTI-EVT-140 – 145, 150, 170, 180, 186 |
| `vtc/events/guests/register`, `guests/withdraw`, `invitations/links/create` 0.2 (`forGuests`) | VTI-EVT-150 – 154 |
| `vtc/events/calendars/create`, `update`, `list` 0.2 (self check-in, attendance default) | VTI-EVT-164, 180 |
| `vtc/events/checkin/challenge`, `checkin/record`, `checkin/list` | VTI-EVT-160 – 166 |
| `vtc/events/tickets/show` | VTI-EVT-170 – 174 |
| `vtc/events/attendance/show` | VTI-EVT-180 – 185 |
| `vtc/events/create`, `update`, `show`, `list`, `publish`, `cancel` 0.4 (series, anonymity, identified registration) | VTI-EVT-190 – 194, 210 – 213, 217 – 218, 231 |
| `vtc/events/occurrences/update`, `vtc/events/series/split` | VTI-EVT-193, 196, 197 |
| `vtc/events/registrations/*` 0.3, `mine/list` 0.3, `checkin/*` 0.2, `access/show` 0.2 (an occurrence) | VTI-EVT-194 – 196 |
| `vtc/events/classes/show`, `classes/enrol` | VTI-EVT-212 |
| `vtc/events/anonymous/register`, `anonymous/withdraw`, `anonymous/checkin` | VTI-EVT-213 – 216 |
| `vtc/events/personas/register`, `personas/withdraw` | VTI-EVT-230 – 232 |
| `vta/pcs/member/enrol-request`, `enrol-accept`, `attest` (the member's agent) | VTI-EVT-212 – 215, 230 |

### Later phases

*This subsection is informative.* These are designed but not yet specified; a
later revision of this chapter states them normatively:

- **The member's calendar** (`vta/calendar/*`): a member's VTA reading their
  events from every community they belong to, firing their reminders, keeping the
  receipts of VTI-EVT-092, and serving them as calendar feeds.
- **Meetings**: agenda, chair, roll call, quorum, motions decided by the
  community's elections, minutes, action items.
- **Booking pages**: a member's page where others book time with them, with
  rules per tier.
- **Events hosted by a person** from their own VTA.
