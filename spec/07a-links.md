## Trigger Links

This section is normative.

### In plain terms

A community wants a member to sign in on a laptop, or an operator wants a
phone to claim an agent. The easiest thing to hand a person is a QR code on a
screen or a link in a message. The person points a camera at it, or taps it,
and the wallet they already use should open.

The code is not a key and not an instruction. It is a note that says *this is
who wants to talk to you, and this is the reference number of the
conversation*. The wallet does not believe a word of it until it has checked.
It shows the person who claims to be asking, looks that party up in its own
published records, and only then, and only if the person agrees, sends a signed
first message. Everything the exchange needs after that comes from the
verified records, never from the note.

Two decisions in this chapter look fussy and are not. The note travels after
the `#` in the link, because that is the part a browser never sends to a
server: the host serving the link never learns the reference number. And a
wallet ignores any parameter it does not recognise, because mail systems and
link shorteners append their own; a link that breaks when a newsletter adds a
tracking parameter is a link people stop trusting.

### What this chapter defines

This chapter specifies the [[ref: trigger link]]: the text an inviter shows as a QR
code or a link to start an exchange with a wallet it cannot otherwise reach. It
defines the link's form and how a reader parses it, the fields it carries, the
flows it can name, what a reader does after reading it, what a producer must
and must not emit, and the first two registered flows, `sign-in` and
`vta-claim`.

It does not define the exchange that follows the first request. That is the
business of the Trust Tasks the flow uses (Operation surface), carried over a
transport the inviter's DID document advertises (Transports, messaging and
delivery).

Terms used in this chapter:

| Term | Meaning |
|---|---|
| **Trigger link** | The text defined by this chapter. |
| **Inviter** | The party that produces a trigger link and receives the first request. For `sign-in`, the VTC; for `vta-claim`, the VTA Farm. |
| **Reader** | A client that reads a trigger link and acts on it: a wallet. |
| **Producer** | Anything that emits a trigger link on the inviter's behalf: the inviter, or a page or service it operates. |
| **Link host** | The host named in the link's authority, which serves the platform association files and a page for people with no wallet. |
| **Flow** | A named kind of exchange a trigger link can start, identified by a URI. |

**VTI-LNK-001** — A target that reads, produces or hosts trigger links MUST
implement the `Links` profile, and MUST satisfy the requirements of this
chapter that bind the role it plays.

### The link

A trigger link has the form:

```
https://<link host>/<path>#_from=<VID>&_id=<handle>[&_exp=<n>][&_type=<flow>]
```

The proposed shared link host is `link.trustoverip.org`, with the path `/t`.
Every parameter a reader uses sits in the fragment.

**VTI-LNK-010** — A producer MUST emit a trigger link as an `https` URL, with
`_from` and `_id`, and `_exp` and `_type` where used, each exactly once, in the
fragment. A producer MUST NOT put any of them in the query.

**VTI-LNK-011** — A reader MUST read only the four **reserved names** `_from`,
`_id`, `_exp` and `_type`, and only from the fragment. A reader MUST ignore every
other name, whatever its value, and MUST NOT read the query.

**VTI-LNK-012** — A reader MUST accept the same fragment on any link host that
meets the host rules (VTI-LNK-060). A reader MAY also read the same text under a
custom scheme it registers for its own scanner (an **alias scheme**), with the
same host, path and fragment.

**VTI-LNK-013** — A producer MUST NOT emit a trigger link under a custom scheme,
and MUST NOT offer one for a handle that can be spent.

*Rationale.* A client sends no fragment in a request or in `Referer` [RFC 9110],
so a host serving the link, a CDN in front of it and a link-preview fetch all
see the path and never the handle. Ignoring unknown names is what lets a link
survive a mail system or a redirector appending its own parameters; rejecting
them would break links and buy nothing, because every field that matters is
read by name and checked by grammar. A custom scheme is refused to producers
because any app on a phone can register one [RFC 8252], so the app that opens it
is not the one the person chose. Which app opens an `https` link is decided by
the operating system, from association files the link host publishes and from
what each installed app declares.

### Reading a link

Parsing follows the WHATWG `application/x-www-form-urlencoded` parser [URL]
applied to the fragment: split on `&`, skip empty sequences, split each at its
first `=`, replace `+` with a space, percent-decode once (a malformed `%`
sequence stays as written), and decode as UTF-8. Names are compared
case-sensitively after decoding. A raw `=` inside a value therefore reads the
same as `%3D`; an unencoded `&` or `#` ends the value.

**VTI-LNK-020** — A reader MUST evaluate a trigger link in the following order,
and MUST stop at the first failure with the reason given. A reader MAY evaluate
in another order if it always reports the same reason for the same text.

1. Trim leading and trailing ASCII whitespace. More than 1,536 code points:
   `too-long`.
2. The text does not begin with `<scheme>://`, compared case-insensitively,
   where the scheme is `https`, `http` or an alias scheme: `not-ours`. No
   reserved name in the fragment: `query-form` if the query has one, otherwise
   `not-ours`. Scheme `http`: `insecure-scheme`.
3. Any code point from U+0000 to U+0020 or U+007F anywhere in the text, or a
   second `#`: `bad-grammar`.
4. The authority has userinfo or a port, or its host, after WHATWG host
   parsing as for an `https` URL, fails the host rules (VTI-LNK-060):
   `bad-authority`.
5. A reserved name occurs more than once: `repeated-param`.
6. `_from` is absent: `missing-from`. `_from` is not a well-formed contact
   (VTI-LNK-030): `bad-from`. `_from` is a contact form the reader does not
   support: `unsupported-vid`.
7. `_id` is absent or not as VTI-LNK-033 requires: `bad-id`. `_exp` is present
   and not as VTI-LNK-036 requires: `bad-exp`.
8. `_type` is present and not as VTI-LNK-040 requires: `bad-type`. The resolved
   flow names a flow the reader implements, on a different host: `wrong-host`.
   The resolved flow, without its version segment, is not a flow the reader
   implements: `unknown-flow`. Its version is one the reader does not accept
   (VTI-LNK-044): `unsupported-version`.
9. The flow does not allow the contact (VTI-LNK-031): `from-not-allowed`. The
   flow requires an expiry and `_exp` is absent: `missing-exp`.
10. `_exp + 60 <= now`, by the reader's clock: `expired`.
11. Accept.

**VTI-LNK-021** — A reader MUST show the person only the message for the
outcome of the reason, from the table below, and MUST NOT say which field
failed or whether a contact was on a list.

| Outcome | Reasons | Message |
|---|---|---|
| `update` | `unsupported-vid`, `unknown-flow`, `unsupported-version` | "This code needs a newer version of the app." |
| `expired` | `expired` | "This code has expired. Get a new one." |
| `unreachable` | `no-common-transport` (VTI-LNK-053) | "This service can't be reached from your wallet." |
| `pass-on` | `not-ours` | None. The text goes to the reader's other handlers, unchanged. |
| `invalid` | every other reason | "This code can't be used." |

**VTI-LNK-022** — After `unsupported-vid`, `unknown-flow` or
`unsupported-version`, a reader MUST NOT try another flow, version or parser on
the same text.

*Rationale.* The outcomes are few because the person can act on only a few:
update the app, get a new code, or give up. A message that names the failing
field teaches an attacker which field to adjust and teaches the person nothing.
`unsupported-vid` maps to `update` because a newer wallet may support that kind
of identifier; `from-not-allowed` maps to `invalid` because no update changes a
flow's rule. The 60-second allowance in step 10 absorbs phone clock error
without outlasting a short-lived code; the inviter still decides expiry by its
own clock.

### The fields

#### `_from`: the contact

**VTI-LNK-030** — `_from` MUST be a VID [TSP]: a DID of any method, or another
verifiable identifier. A reader MUST percent-decode it once and MUST check it
against the syntax of its identifier type.

**VTI-LNK-031** — A flow MAY restrict the identifier types it accepts as a
contact. A reader MUST refuse a contact the flow does not allow with
`from-not-allowed`, and MUST refuse an identifier type it does not support with
`unsupported-vid`.

**VTI-LNK-032** — A value of `_from` containing the two characters `/@` is
reserved for agent names. Until this specification defines agent names as a
contact form, a reader MUST refuse such a value with `unsupported-vid`, and a
producer MUST NOT emit one.

*Note.* An agent name (`example.com/@alice`) is a domain-rooted, human-readable
name that resolves to a DID, verified in both directions: the domain points the
name at a DID, and that DID's document lists the name in `alsoKnownAs`. It is a
third the length of a `did:webvh` and shows the person a domain they recognise.
Its canonical form, what a community name (`example.com/@`) resolves to, and the
redirect contract are not yet settled; the reservation lets a later revision
admit it without changing how existing links are read. A DID cannot contain `/`
outside a DID URL, so no valid DID is mistaken for an agent name.

#### `_id`: the handle

**VTI-LNK-033** — `_id` MUST be 16 to 32 bytes written as unpadded base64url
[RFC 4648], using only `A-Za-z0-9-_`: 22 to 43 characters. A value of length 25,
29, 33, 37 or 41 characters, which no whole number of bytes produces, is
malformed. The unused bits of the last character MUST be zero.

**VTI-LNK-034** — A producer MUST generate the handle's bytes from a
cryptographically secure random source.

**VTI-LNK-035** — An inviter MUST compare a handle as a string.

*Rationale.* Requiring the unused bits to be zero gives each handle exactly one
spelling, so a handle compared as a string cannot be presented twice under two
spellings. 128 bits is the floor for a value nobody can guess; 256 bits leaves
room for an inviter that encodes state in the handle.

#### `_exp`: the expiry

**VTI-LNK-036** — `_exp` MUST be UTC epoch seconds written as a decimal
integer: `0`, or with no leading zero, and at most 2^53−1.

**VTI-LNK-037** — A producer MUST set `_exp` no later than the end of the
inviter's own lifetime for the handle.

### Flows

#### `_type`: the flow

A flow is named by an absolute `https` URI whose last segment is the version,
`<MAJOR>.<MINOR>`. In a link, a flow on the link's own host is written as a
path, and resolved against that host.

**VTI-LNK-040** — `_type`, where present, MUST be either an absolute `https` URI
with no query or fragment whose last segment is `<MAJOR>.<MINOR>` in decimal
with no leading zeros, or a **path form**: a path starting with exactly one `/`,
containing only `A-Za-z0-9-._~/`, with no `.` or `..` segment and no
percent-encoding, whose last segment is a version as above. A reader MUST refuse
any other value with `bad-type`, and MUST NOT normalise one into shape.

**VTI-LNK-041** — A reader MUST resolve a path form against `https://` and the
link's host, lowercased, whatever the link's scheme. The flow is identified by
the resolved URI, compared as a whole string.

**VTI-LNK-042** — A producer MUST use the path form when the flow URI is on the
link's own host, and MUST use the absolute form otherwise.

**VTI-LNK-043** — A reader MUST refuse with `wrong-host` a resolved flow whose
path names a flow the reader implements but whose host differs from that flow's.

*Rationale.* A flow is identified by its whole string, never by its final name
alone, so the same name under another host is a different flow. The path form
is a standard relative reference [RFC 3986], not a name completed against an
assumed registry, and it removes the scheme and host from the code, which is
most of `_type`'s length. Resolving against `https` and the lowercased host,
whatever scheme the link arrived under, keeps an alias scheme and an
upper-cased host from producing a URI that matches no flow. A path that names a
known flow on another host is refused as `wrong-host` rather than
`unknown-flow`, because telling the person to update the app would be wrong.

#### Versions

**VTI-LNK-044** — A reader MUST refuse a MAJOR version it does not implement,
and SHOULD accept a higher MINOR of a MAJOR it implements. For a flow whose
status is draft, a reader MUST accept only the MINOR versions it implements.

**VTI-LNK-045** — A published flow identifier MUST NOT be edited. A change to a
flow MUST be a new version, and a security-relevant field added to a flow MUST
come with a new version that a reader without the field refuses.

*Rationale for VTI-LNK-045.* A reader ignores names it does not know, so an
unsigned link can lose any optional field in transit. A field that matters to
security is only enforced if a reader that does not know it refuses the link,
and only a new version achieves that.

#### The flow registry

Flows are named under `https://link.trustoverip.org/vti/flow/` and governed by
this specification. A flow names the purpose of an exchange, not the Trust Task
a reader sends first; the tasks a flow uses may change without changing its
identifier.

**VTI-LNK-046** — A new flow MUST be added to this registry by a change to this
specification that states its identifier, the contact it expects, its expiry
rule, which contacts it accepts, and test vectors, with an owner on the inviter
side and on each reader that implements it.

| Flow | Identifier (version 0.1, draft) | Contact | Expiry |
|---|---|---|---|
| Sign in to a community portal | `https://link.trustoverip.org/vti/flow/sign-in/0.1` | the VTC's VID | required |
| Claim a VTA from a VTA Farm | `https://link.trustoverip.org/vti/flow/vta-claim/0.1` | the Farm's VID | required |

*Note.* Step-up and device enrolment, if started by a trigger link, are
separate flows, each named when it is designed.

### After the link

**VTI-LNK-050** — Before any network activity, DID resolution included, a
reader MUST show the person who the contact claims to be, marked unverified,
and MUST wait for the person to continue. For a contact with a domain, the
reader shows the domain, followed by the path where the identifier has one (for
example `dids.example.org/farm-auth` for
`did:webvh:<SCID>:dids.example.org:farm-auth`). For one without, it shows its own label for that
contact if it has one, and otherwise says the contact has no domain to show.

**VTI-LNK-051** — For a flow that acts only for contacts already in the reader's
own records, a reader MAY resolve such a contact before the person continues.

**VTI-LNK-052** — A reader MUST resolve the contact's DID document and verify it
(for `did:webvh`, the log verifies) before sending anything. If it cannot, it
MUST stop with `did-document-unverified` (outcome `invalid`) and send nothing.
After verification it MUST show the verified details, and MUST send nothing
until the person approves. A reader MUST NOT show any field of the link other
than the contact as a statement of what the exchange is.

**VTI-LNK-053** — A reader MUST take the transport and endpoint from the
verified DID document, and never from the link. Candidates are the services of
the document whose `type` maps to a binding the reader implements, matched on
`type` and never on `id`, whose endpoint is an `https` URL or a DID, and whose
endpoint host, where it has one, meets the host rules. The reader's own
preference order chooses among bindings; among candidates of one type the first
in document order wins. With no candidate the reader MUST stop with
`no-common-transport`, MUST send nothing, and MUST NOT fall back to anything in
the link. A reader MUST resolve the document afresh for each exchange.

**VTI-LNK-054** — The first request MUST be a Trust Task document whose issuer is
an identifier generated by the reader for this exchange and used nowhere else,
whose recipient is the contact, with a unique `id`, carrying the handle as
`parentThreadId`, and signed by the key of that identifier.

**VTI-LNK-055** — Where `_type` is absent, a reader MAY ask the inviter which
tasks it supports, and MAY refuse a link it cannot place, with outcome
`invalid`.

*Rationale.* The link is unauthenticated text that has been on screens,
photographed, previewed and logged. Showing the contact as unverified before
any network activity stops a code from making a phone contact a host the person
never chose; VTI-LNK-051 relaxes that only where the reader already knows the
contact, so the host contacted is one the person chose when they joined. The
transport comes only from the verified document, so a link cannot point a
wallet at an endpoint of the link-maker's choosing. A fresh identifier for the
first request means a code seen by a stranger cannot be used to correlate the
person's other exchanges.

### The host rules

**VTI-LNK-060** — The host rules apply to the link host, to the host of a
contact that has one, and to every endpoint host a reader contacts. A host MUST
be a DNS name of at least two labels and at most 253 characters, with no
trailing dot. Each label is 1 to 63 of lowercase `a-z`, `0-9` and `-`, not
beginning or ending with `-`, and the last label is not all digits. A host MUST
NOT be an IP address in any form, including the forms that WHATWG host parsing
turns into an address, MUST NOT carry a port, and MUST NOT be `localhost`, a
name under `localhost` or `local` [RFC 6761] [RFC 6762], or a name under
`home.arpa` [RFC 8375].

**VTI-LNK-061** — A reader SHOULD refuse an endpoint whose resolved address is
loopback, link-local or private.

### Security of the handle

**VTI-LNK-070** — A trigger link MUST NOT confer authority. Acting on it is the
reader's decision; granting anything is the inviter's, on the signed first
request.

**VTI-LNK-071** — An inviter that treats possession of a handle as authority
MUST make the handle single use and short lived.

**VTI-LNK-072** — Fetching a trigger link MUST NOT spend its handle. A handle is
spent, if at all, by the signed first request. A trigger link MUST NOT cause a
fetch by reference.

**VTI-LNK-073** — A reader MUST NOT log the link, the handle or the contact, and
MUST NOT put them in an error, a notification or analytics, or send them
anywhere but to the inviter in the first request. A reader MAY log the outcome
of a link.

*Rationale for VTI-LNK-072.* Link previews, mail scanners and browsers fetch
links nobody tapped. A handle spent by a GET is spent by whichever of them gets
there first.

### Producers

**VTI-LNK-080** — A producer MUST emit only ASCII.

**VTI-LNK-081** — A producer MUST NOT emit a trigger link whose `https` form
exceeds 251 bytes where the code is rendered at QR error-correction level M, or
177 bytes where it is rendered at level Q [ISO 18004]. A flow's field limits
follow from these.

**VTI-LNK-082** — A page that carries or shows a trigger link MUST be served
with `Referrer-Policy: no-referrer` and `Cache-Control: no-store`, MUST load no
third-party scripts, and MUST NOT copy the fragment into a request, a log or a
script that sends it.

**VTI-LNK-083** — A redirect from a page that carries a trigger link MUST give
the target an explicit fragment, possibly empty.

**VTI-LNK-084** — A producer MUST NOT show a trigger link whose link host is the
domain of the page showing it.

**VTI-LNK-085** — An inviter MUST publish in the contact's DID document a
service a reader can select under VTI-LNK-053.

*Rationale.* The two byte limits are the capacity of a version 11 QR code at
levels M and Q, which scan comfortably from a laptop or monitor. Limiting the
producer, not the reader, keeps a valid link valid after something appends to
it. Emitting only ASCII matters because QR byte mode does not say which
character set it carries: the standard assumes ISO-8859-1 and many scanners
guess UTF-8. A redirect with no fragment of its own carries the original
fragment to its target [RFC 9110]. A universal link tapped on a page of the same
domain opens in the browser on iOS, not in the app, which is why the link host
is kept off the page's domain.

#### Rendering guidance

The following is guidance for producers and carries no normative force:

- Render in byte mode at level M. Use level Q with a centre logo covering at most
  about 15% of the area, and keep the link within 177 bytes. Do not use level L:
  on a screen, glare damages a code as a scratch does.
- Keep a quiet zone of at least 4 modules, draw at least 4 CSS pixels per module
  and larger where there is room, and draw dark modules on a light background
  in every colour theme, never inverted.
- Draw as SVG or a crisp canvas, never scaled with smoothing.

### The link host

**VTI-LNK-090** — A link host MUST publish platform association files that claim
only its trigger path, so that a flow identifier on the same host opens its page
and not a wallet.

**VTI-LNK-091** — A link host MUST serve, at its trigger path, a page for a
person with no wallet. The page MUST NOT read the fragment, MUST NOT send it
anywhere, and MUST NOT redirect to a custom scheme. It SHOULD say what the code
is for, and SHOULD offer the reader stores and an "open in the app" action that
uses the `https` link itself.

**VTI-LNK-092** — A link host SHOULD serve, at each flow identifier, a page that
describes the flow.

*Note.* One shared link host listed in every participating wallet's build is the
only way one link can open whichever wallet a person has, because each
platform opens an `https` link only in an app whose build declares that host.
Until a shared host exists, a link opens only the wallets that declare its
host. Whether a platform keeps the fragment through every camera, browser and
app hand-off is not yet verified on real devices; if a platform drops it, the
handle cannot travel in the fragment there.

### The `sign-in` flow

The `sign-in` flow starts a member's sign-in to a community portal from a code
the portal shows. The contact is the community's VTC.

**VTI-LNK-100** — For `sign-in`, `_exp` is required, and MUST be no later than
300 seconds after the code is made.

**VTI-LNK-101** — For `sign-in`, a reader MUST act only for a community already
in its own records. For a contact that is not, a reader MUST send nothing to it,
and SHOULD offer to join the community.

**VTI-LNK-102** — For `sign-in`, the contact's resolved DID document MUST list
the portal's service. A reader MUST refuse with `from-not-allowed` a contact
whose identifier type cannot carry one, such as `did:key`.

**VTI-LNK-103** — A VTC SHOULD use a 16-byte handle (22 characters) for
`sign-in`.

**VTI-LNK-104** — Before the first request, a reader MUST show the community's
name from its own records, and MUST flag any difference from the name the VTC
supplies.

*Note on the size budget.* With the path form on `link.trustoverip.org/t`, the
parts of a `sign-in` link other than `_from` and `_id` take 86 bytes. At level
M, VTI-LNK-081 leaves 165 bytes for `_from` and `_id` together: `_from` at most
122 with a 43-character handle, or 143 with the recommended 22. At level Q they
share 91, so `_from` is at most 69 with a 22-character handle, and a `did:webvh`
then fits only with a host of 12 characters or fewer.

*Note on the exchange that follows.* The Trust Tasks of the sign-in exchange,
and whether the first request locks the request to the first reader that claims
it, are being specified separately and are not part of this revision. Whatever
they are, VTI-LNK-050 to VTI-LNK-054 order them: the reader shows the community
from its own records, the member continues, and only then is anything signed
sent.

### The `vta-claim` flow

The `vta-claim` flow connects a wallet to a VTA that a VTA Farm provisions. A
visitor opens a claim page, which reserves a VTA and shows a code. The wallet
scans it and sends a fresh identifier, which becomes the VTA's first
administrator. The contact is the Farm, not the VTA: the VTA is what the
exchange produces, and the Farm returns its DID in the first response. The same
code serves a first administrator for a new VTA, an added device for a running
VTA and a claim of a pooled VTA; the Farm tells them apart from its own state,
and the reader cannot choose.

**VTI-LNK-110** — For `vta-claim`, `_exp` is required, and MUST be no later than
300 seconds after the code is made.

**VTI-LNK-111** — For `vta-claim`, a reader MUST NOT apply VTI-LNK-051: the Farm
is a first contact, and nothing is resolved before the person continues.

**VTI-LNK-112** — For `vta-claim`, the contact's resolved DID document MUST list
the Farm's claim service. A reader MUST refuse with `from-not-allowed` a contact
whose identifier type cannot carry one, such as `did:key`.

**VTI-LNK-113** — For `vta-claim`, the issuer of the first request (VTI-LNK-054)
is the identifier the claim makes an administrator of the VTA. A reader MUST NOT
use an identifier it already uses for another VTA or another exchange.

**VTI-LNK-114** — A Farm MUST accept at most one claim per handle, atomically,
and MUST refuse every later claim for it. A Farm MUST NOT treat a request that
does not carry a valid signed first request as a claim.

**VTI-LNK-115** — After a successful claim, a Farm SHOULD show on the claim page
a **claim check**, and a reader SHOULD show the same claim check computed from
its own identifier. The claim check is the first six characters of the base32
encoding [RFC 4648] (section 6, upper case) of the SHA-256 hash [FIPS-180-4] of
the identifier's UTF-8 string. A Farm SHOULD NOT release the VTA from the reservation until the person
confirms on the page that the two match, and SHOULD revoke the identifier and
return the VTA to the pool when the person says they do not.

**VTI-LNK-116** — A Farm SHOULD use a 16-byte handle (22 characters) for
`vta-claim`.

*Rationale for VTI-LNK-114 and VTI-LNK-115.* A forged code is harmless: the
reader talks only to the endpoint in the Farm's verified document, so a made-up
handle earns a refusal. A real code is not: whoever scans it first becomes an
administrator, and a person behind the visitor can photograph the screen and
scan before the visitor does. `sign-in` meets that threat with a check before
the claim. A first contact has nothing to check against beforehand, so the claim
check catches it afterwards, before the VTA is handed over.

*Note on adding a device.* A code that adds an administrator to a running VTA
is worth more to an attacker than one for an empty VTA. Whether such a code is
shown only to a signed-in administrator, which role the added identifier gets,
and whether the claim check is then required are open with the Farm's authors.

*Note on the size budget.* With the path form on `link.trustoverip.org/t`, the
parts of a `vta-claim` link other than `_from` and `_id` take 88 bytes. At
level M, `_from` and `_id` share 163 bytes: `_from` is at most 141 with a
22-character handle. At level Q they share 89, so `_from` is at most 67 with a
22-character handle, and a Farm with a longer identifier renders its codes
without a logo.

### Examples

These examples are informative. The DIDs are made up.

A `sign-in` link with a `did:webvh` contact, 184 bytes:

```
https://link.trustoverip.org/t#_from=did:webvh:QmPEQVM1JPTyrvEgBcDXwjK4TeyLGSX1PxjgyeAisPviUx:members.example.org&_id=Hk2pQ9xV4mT7rW1sZ8yN3A&_exp=1791460920&_type=/vti/flow/sign-in/0.1
```

| Input, as it differs from the link above | Result |
|---|---|
| The link above | accepted; flow `https://link.trustoverip.org/vti/flow/sign-in/0.1` |
| `_type=https://link.trustoverip.org/vti/flow/sign-in/0.1` | accepted; the same flow |
| Scheme `keyring://` (an alias scheme the reader registers) | accepted; the same flow |
| Authority `LINK.TRUSTOVERIP.ORG` | accepted; the same flow |
| `?utm_source=newsletter` inserted before `#` | accepted; the query is not read |
| `&utm_source=x` appended to the fragment | accepted; the name is ignored |
| Link host `invite.example.org` | `wrong-host` (`invalid`) |
| `_type=//evil.example/vti/flow/sign-in/0.1` | `bad-type` (`invalid`) |
| `_type=/vti/flow/../flow/sign-in/0.1` | `bad-type` (`invalid`) |
| `_type=/vti/flow/sign%2Din/0.1` | `bad-type` (`invalid`) |
| `_id` of 23 characters | accepted |
| `_id` of 21, 25 or 44 characters | `bad-id` (`invalid`) |
| `_id` of 22 characters whose last character has non-zero unused bits | `bad-id` (`invalid`) |
| `_id` appears twice | `repeated-param` (`invalid`) |
| `_from=members.example.org/@` | `unsupported-vid` (`update`) |
| `_from=did:key:z6Mk…` | `from-not-allowed` (`invalid`) for `sign-in` |
| `_from=did:web:example.com%253A8443` | parses as `did:web:example.com%3A8443`, then refused at resolution: the host rules forbid a port |
| The reader's clock 59 seconds past `_exp` | accepted |
| The reader's clock 60 seconds past `_exp` | `expired` |

A `vta-claim` link with a path `did:webvh` contact, 193 bytes. Before the
person continues, the reader shows `dids.example.org/farm-auth`, unverified:

```
https://link.trustoverip.org/t#_from=did:webvh:QmXa7Rk2ZpLwT9vNc4HbYe1Jd8sMfU3qGo6PtVnEyKiBhW:dids.example.org:farm-auth&_id=Rv8LmQ2nX5tW9kPz3cJhYg&_exp=1791460920&_type=/vti/flow/vta-claim/0.1
```
