## Community Badges

This section is normative.

### In plain terms

A community pins a small picture on a member's card: *hosted a workshop*, *two
years aboard*, *came to open night*, *thanks for the welcome*. Some badges the
community defines and a few trusted members hand out; some any member can give
to any other, as often as they like, and the card keeps count. A member can
hide a badge they would rather not show, decline one they do not want, ask the
people who give a badge for it, and click any badge to see who else has it.

Underneath, a community badge is a community group with a picture: the people
who give it are the group's managers, the people who hold it are its members,
and the group's own identifier signs a credential that says so. A badge the
community lets its holders show outside is a credential anyone can verify, that
the community stands behind and can take back.

### What this chapter defines

This chapter specifies how a community keeps **badges**: pictures with a name
that a member earns, holds and shows on their directory card.

A VTC is not required to offer badges. A VTC that offers them MUST satisfy the
requirements of this chapter; a VTC that does not is not bound by them. A VTC
offering badges MUST offer the community directory and groups (The Community
Directory and Groups chapter).

The chapter adds no authority model. A badge is evidence of something a member
did or is, shown to people; it is not a role, a capability or a membership, and
nothing a badge says grants, widens or removes any authority (VTI-VTC-020).

The capability this chapter refers to is registered in Appendix C.3:
`vtc.badges.admin`, the capability to define the community's badges and to
change what badges the community offers.

Throughout this chapter:

- a **badge** is the community's definition: a name, a description, a picture,
  what it is given for, and its settings;
- a **community badge** is a badge the community defines and that its
  **owners** give; a **kudos badge** is a badge a member makes and that any
  member gives;
- a **holder** is a member who holds a badge; an **award** is one giving of a
  badge to one member;
- a **badge group** is the community group that backs a community badge.

### Badges

**VTI-BDG-001** — A badge MUST state: its identifier, unique within the
community and never reused; its kind, `community` or `kudos`; its name and
description; its picture; whether one member may hold it more than once; and,
for a community badge, whether a member may ask for it, whether it is listed to
members who do not hold it, and whether its holders may show it outside the
community. A badge MUST be either `active` or `retired`.

**VTI-BDG-002** — A VTC MUST NOT read a badge, an award or a badge credential in
deciding whether any subject is authorized (VTI-VTC-020, VTI-ACL-070).

**VTI-BDG-003** — Defining a community badge, changing one, and retiring one
MUST require `vtc.badges.admin`. A kudos badge MAY be made by any active member
the community's badge settings admit (VTI-BDG-004), and changed or retired by
its maker or by a holder of `vtc.badges.admin`.

**VTI-BDG-004** — A VTC MUST keep badge settings of the community's: whether
badges are offered at all; who may make a kudos badge (nobody, the holders of
roles or the members of groups the community names, or every active member);
the limits of VTI-BDG-040; whether a member's kudos total is shown; and whether
automatic badges (VTI-BDG-013) run. Changing them MUST take the consent the
community requires of a change to its own settings, bound to the change
(VTI-OPS-050), and MUST NOT be treated as conferring authority. Turning badges
off MUST hide every badge surface and keep every record; it MUST NOT revoke a
credential, end a membership or erase an award, and no badge MUST be given or
credential issued while badges are off.

**VTI-BDG-005** — Retiring a badge MUST keep the badge's record and its awards
for its holders' own history, delete its badge group where it has one
(VTI-DIR-083), revoke every credential of it that carries a status
(VTI-BDG-063), withdraw its open offers, and tell its holders. A retired badge
MUST NOT be shown on any card or in any listing to members who do not hold it.

*Rationale for VTI-BDG-002.* A badge is the one thing in a community that is
meant to be given freely, by many people, for fun. The moment a badge opens a
door, giving it becomes a grant and every giver an administrator. Keeping badges
out of authorization keeps giving them cheap.

*Rationale for VTI-BDG-004.* A community that turns badges off for a season, or
because a badge drew the wrong kind of attention, should be able to turn them
back on with nothing lost. Off is a shutter, not a purge.

### Badge groups

**VTI-BDG-010** — A community badge MUST be backed by exactly one community
group created for it, with the identity, authorization and custody The
Community Directory and Groups chapter requires of a group (VTI-DIR-080 –
VTI-DIR-083). The group's identifier MUST exist before the badge is recorded,
and the badge and its group MUST reference each other.

**VTI-BDG-011** — A community badge's owners MUST be its badge group's managers
(VTI-DIR-071), and its holders MUST be its badge group's members, held against
the entry identifier (VTI-DIR-073). A VTC MUST NOT keep a second record of who
owns or holds a badge.

**VTI-BDG-012** — A badge group MUST NOT carry authority: a VTC MUST refuse to
bind one to a capability or a role, MUST NOT count it among the
groups two members share, and MUST list it among the community's groups only
to a caller who asks for badge groups.

**VTI-BDG-013** — An **automatic badge** is a community badge whose badge group
is dynamic. VTI-DIR-072 and VTI-DIR-074 apply to it: its holders are computed
from verified facts only, and a member's opting out of the group is their
declining the badge.

**VTI-BDG-014** — A member asking for a community badge MUST be a request to
join its badge group, decided by the badge's owners as any such request is, and
never by the requester (VTI-DIR-071). A badge that is not marked as one members
may ask for MUST have a closed badge group.

*Rationale for VTI-BDG-010.* A group already has what a badge needs and is
expensive to get right: an identifier of its own held with the community's
attestation keys, an authorization credential from the community that one bit
revokes, managers appointed by delegation and reviewed when the delegator
leaves, a request path nobody decides for themselves, membership keyed to the
entry so it survives rotation, and rule-computed membership with an opt-out.
Building a badge on a group buys all of it; building a second copy would
diverge from it.

### Awards and acceptance

**VTI-BDG-020** — Giving a community badge to a member MUST be an **offer**: a
record of the badge's own that names the badge, the member, the owner who made
it, when, and what it is for. A VTC MUST NOT admit the member to the badge
group, issue a credential, or show the badge on the member's card until the
member accepts the offer. An owner MAY withdraw an open offer, and a VTC MAY
expire one after a period it states.

**VTI-BDG-021** — When a member accepts an offer, a VTC MUST admit them to the
badge group as it admits a member to any group that issues credentials:
credential first, membership recorded after (VTI-DIR-084). When a member
declines an offer, a VTC MUST record nothing in the badge group and issue
nothing, MUST show the offer as closed to the badge's owners and to nobody
else, and MAY accept a later offer of the same badge to the same member.

**VTI-BDG-022** — A badge one member may hold more than once MUST record each
giving as an award of its own, offered and accepted as the first was. A holder's
count of a badge is the number of awards they have accepted. A VTC MUST refuse
a second award of a badge that is not so marked.

**VTI-BDG-023** — An owner, or a holder of `vtc.badges.admin`, MAY take an award
back. Taking back a holder's last award of a badge MUST remove them from the
badge group as a manager's removal does; taking back any award MUST revoke the
credential that evidences it where it carries a status (VTI-BDG-063), or stop
its renewal where it does not, and MUST tell the holder.

**VTI-BDG-024** — An approved request for a badge (VTI-BDG-014) is the
requester's consent: a VTC MUST admit them on approval without a further offer.

*Rationale for VTI-BDG-020.* A badge on a card is a statement the member makes
about themselves, in their own name, to everyone who sees the card. Nobody else
gets to make it for them. The offer is also what makes a credential-backed
badge honest: the credential is issued to a subject who asked to hold it.

### The holder's control

**VTI-BDG-030** — A holder MAY hide any badge they hold and show it again. A
hidden badge MUST NOT appear on the holder's card, in the badge's list of
holders shown to members, or in its count, and the holder MUST keep its awards,
their badge-group membership and any credential unchanged. A VTC MUST show the
badge's owners and holders of `vtc.badges.admin` that the holder has hidden it.

**VTI-BDG-031** — Who may see a member's badges MUST be a setting of the
member's, defaulting to the community's active members, and a VTC MUST NOT
show a member's badges to a viewer to whom the directory does not show that
member's card (VTI-DIR-020, VTI-DIR-021).

**VTI-BDG-032** — The order of a member's badges on their card, and which of
them are pinned beside their name, MUST be the member's own choice. A VTC MUST
bound the number pinned.

**VTI-BDG-033** — A badge's holders MUST be shown to an active member who may
see the badge, each holder as the directory projects them to that viewer
(VTI-DIR-020), never by identifier, with hidden holders left out and the count
stated over the holders shown. The list MUST be returned by a paginated
listing, never inline in a read of the badge (VTI-DIR-070).

*Rationale for VTI-BDG-030.* The badge is the member's to show, and hiding is
the member's choice about their own card, not a change to what the community
recorded. The owners who gave it, and who answer for the badge, still see whom
they gave it to; that is what lets them run the badge.

### Kudos

**VTI-BDG-040** — A kudos badge MAY be given by any active member to any other
active member, never to themselves. A VTC MUST bound how many kudos one member
may give in a day, how often the same member may give a kudos badge to the same
member, and how long a member must have been a member before giving one, at
values the community states, and MUST refuse beyond them.

**VTI-BDG-041** — Each giving of a kudos badge MUST be an award of its own,
offered and accepted or declined by its recipient as VTI-BDG-020 and
VTI-BDG-021 require. A kudos badge MUST NOT carry a credential and has no badge
group.

**VTI-BDG-042** — A member MUST be shown the kudos they received, with the
giver and any note, and the kudos they gave. A holder of `vtc.badges.admin`
MUST be shown every giving of a kudos badge. Every other viewer MUST be shown
only the count a holder shows (VTI-BDG-030).

**VTI-BDG-043** — A member's kudos total, where the community shows one
(VTI-BDG-004), MUST be counted over the awards the member has accepted and not
hidden, and MUST be shown subject to VTI-BDG-031.

**VTI-BDG-044** — A holder of `vtc.badges.admin` MAY promote a kudos badge to a
community badge. Promotion MUST create its badge group (VTI-BDG-010) and offer
each existing holder the badge (VTI-BDG-020); it MUST NOT admit anyone or issue
anything without their acceptance.

*Rationale for VTI-BDG-040.* Kudos are the badges that make a community feel
like one, and also the ones easiest to farm. The limits are per giver, so a
popular member can receive as many as the community cares to give, and a pair
of members cannot manufacture a wall between them.

### Pictures

**VTI-BDG-050** — A badge's picture MUST be stored and served as the community's
own re-encoding, as the directory requires of a card picture (VTI-DIR-051): no
byte the uploader sent and no metadata survive. A VTC MUST show the picture of
a badge that may not be shown outside only to active members.

**VTI-BDG-051** — A badge whose holders may show it outside MUST have a picture
and a description of the badge at stable public locations its credential
names. Those locations MUST NOT disclose who holds the badge.

### Credentials

**VTI-BDG-060** — A badge group MUST issue each holder a **badge credential** in
place of a group membership credential (VTI-DIR-084): a credential whose issuer
is the badge group's identifier, whose subject is the holder's membership
identifier, that names the badge and its picture, that names the community as
the badge's creator and carries the community's endorsement of it, and that
evidences each award the holder accepted. A VTC MUST obtain it by a signing
request to its VTA (VTI-DIR-080) and MUST offer it to the holder as it offers
any credential it issues. Its data model is the Open Badges 3.0 profile of the
Verifiable Credentials Data Model, stated in the Trust Task specification.

**VTI-BDG-061** — The badge credential of a badge its holders may not show
outside MUST be short-lived and renewed, with no status mechanism and no
status set of the badge's or the group's own, on the terms VTI-DIR-085 sets
for a group membership credential.

**VTI-BDG-062** — The badge credential of a badge its holders may show outside
MUST have a validity period the community states and MUST carry a status
mechanism (VTI-CRD-010). Its status MUST be answered over the community's own
status set, the one its community credentials use, allocated as VTI-CRD-014
requires; it MUST NOT be answered over a set of the badge's or the group's own.
The community issues that set and alone changes it.

**VTI-BDG-063** — Taking an award back (VTI-BDG-023), retiring the badge
(VTI-BDG-005) and the holder's departure MUST each revoke the holder's badge
credential where it carries a status and stop its renewal where it does not.
Retiring the badge MUST also revoke the community's authorization credential to
the badge group (VTI-DIR-083).

**VTI-BDG-064** — A verifier relying on a badge credential MUST verify it and
its validity, its status where it carries one, and that the community stands
behind its issuer, by the community's endorsement in the credential, by the
community's authorization credential to the group, or by a trust registry
(VTI-BDG-080), and MUST refuse where any of them fails. VTI-DIR-087 applies: a
VTC MUST NOT consult a badge credential to decide anything of its own.

*Rationale for VTI-BDG-061 and VTI-BDG-062.* The two regimes answer two uses. A
badge seen only on a card inside the community needs no status check at all:
a short life renewed by the community reaches a verifier as fast as any
removal needs to, and nobody fetching a list learns who is being checked. A
badge on a website outside the community is useless if it lapses every month
and dangerous if it cannot be taken back, so it lives long and carries a
status; the crowd it hides in is the whole community's, which is why the set is
the community's and never the badge's.

### Outside the community

**VTI-BDG-070** — Only a badge marked by the community as one its holders may
show outside MAY be shown outside, and only by a holder's choice, award by
award. For an award so chosen a VTC MUST serve, at a public location it answers
to the holder, a verification of exactly what the credential says, and MAY
serve the credential embedded in the badge's picture. A holder MAY stop
showing an award at any time, after which the location MUST answer nothing;
stopping is not a revocation.

**VTI-BDG-071** — Nothing a VTC serves publicly for a badge MUST disclose any
holder other than the one who chose to show the award it is for, nor how many
hold the badge.

**VTI-BDG-072** — A member's published external card (VTI-DIR-046) MAY name the public
locations of the awards they show outside; a VTC MUST NOT add one the member
did not choose.

### Registries

**VTI-BDG-080** — A VTC that publishes to a trust registry MUST publish each
badge group as an issuer the community recognises, as a projection of its own
record, which stays the source of truth, and MUST remove it when the badge is retired.

### Departure and erasure

**VTI-BDG-090** — A member's departure MUST end their badge-group memberships,
withdraw their open offers and requests, and leave the awards
they gave standing by entry reference, so that other holders' counts are
unchanged. Erasure MUST remove the notes they wrote on awards.

**VTI-BDG-091** — Every change to a badge, an award or a badge setting MUST be
audited by reference, never with a picture or a note.

### Members' acts and the portal

**VTI-BDG-095** — A holder's acts (accepting, declining, hiding, showing,
arranging, giving kudos, making a kudos badge) MUST each be a document signed
as the member. A member portal that holds a key the member authorized to sign
for them for this chapter's tasks (`auth/oob` `signingScope`) MAY sign them
with it, for the session that authorization arms, as VTI-BRD-050 allows for a
board. A VTC MUST NOT accept such a key for defining, changing, retiring or
promoting a community badge, for offering or taking back an award, or for
changing badge settings.

*Open issue.* An automatic badge for coming to an event needs a fact the
community verified, such as a claim code the event's host hands out and a
member redeems. This chapter does not yet specify it.
