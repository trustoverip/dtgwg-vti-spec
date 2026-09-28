## Transports, Messaging and Delivery

This section is normative.

### In plain terms

The post office accepting your letter is not the recipient reading it.

Most of this chapter is that sentence applied carefully. A send that returns
without error has, at best, handed the message to something that promised to
carry it. Treating that as delivery is how a system ends up confident that
something happened when it did not — and the failure is silent, because
everything reported success.

So this chapter is exact about what counts as evidence that a message arrived,
and requires a sender to record *which* evidence it has. It also requires a
node's health signal to answer "can I send right now?" rather than "did I
manage to once, when I started".

### What this chapter defines

This chapter specifies how VTI nodes reach each other, and — the harder half —
what a sender is entitled to conclude when a send returns successfully.

### Transport independence

**VTI-TRN-001** — A node MUST support at least one of the transports defined by
the referenced transport specifications, and MUST state which.

**VTI-TRN-002** — An authorization decision MUST NOT depend on the transport a
request arrived over. The same request under the same authority MUST produce
the same decision on every transport a node exposes.

**VTI-TRN-003** — A node MUST NOT expose an operation on one transport that it
refuses on another for any reason other than the caller's authority.

*Rationale.* Where transports carry different decisions, the weakest one is the
node's real policy, and which transport that is will not be recorded anywhere.

### Liveness

**VTI-TRN-010** — A node MUST retain at least one working transport. A
configuration change that would remove the last one MUST be refused.

### Mediators

**VTI-TRN-020** — A mediator MUST NOT be able to read the content it forwards.

**VTI-TRN-021** — A recipient MUST authenticate the sender of a message from the
message itself, and MUST NOT rely on an assertion by the mediator.

**VTI-TRN-022** — A node MUST NOT treat the ability to reach it through a
particular mediator as conferring authority.

### Metadata and correlation

**VTI-TRN-025** — A sender MUST NOT place outside the encrypted envelope any
metadata that reveals the nature of the operation being performed.

**VTI-TRN-026** — Routing identifiers SHOULD differ per relationship, such that
an intermediary cannot link two of a principal's contexts by their routing
alone.

**VTI-TRN-027** — Where a deployment's threat model includes traffic analysis,
a node SHOULD apply padding, batching or cover traffic, and MUST document
whether it does.

**VTI-TRN-028** — A wake or push notification delivered through a third-party
service MUST NOT carry content, and MUST NOT carry identifiers that reveal to
that service the nature of the pending action or the parties to it.

*Rationale.* A mediator is trusted to route and not to read, and the routing is
the part it necessarily sees. What it must not be able to do is reconstruct a
principal's activity across contexts from routing alone, which is what shared
routing identifiers hand it for free. VTI-TRN-028 addresses the same hazard one
layer out: a push service is an intermediary nobody in the trust system chose,
and a notification that says what is waiting tells it more than the mediator
learns.

### Resource bounds

**VTI-TRN-029** — A node MUST bound the resources any one subject can consume,
and MUST bound those available to unauthenticated peers separately.

### Truthful send

**VTI-TRN-030** — A send operation MUST NOT report success unless the message
has been transmitted or durably queued for transmission.

**VTI-TRN-031** — Acceptance of a message by a hop MUST NOT be reported as
delivery to the recipient.

*Rationale.* A send that resolves successfully for a frame that was dropped
makes every layer above it wrong in the same direction: the application
believes the message is gone, the retry machinery has nothing to retry, and the
health signal agrees with both. Nothing in the system disagrees, so nothing
raises an alarm.

The requirement is stated at the send because that is the only place the truth
is still available. A layer above can observe that no reply came, which is
consistent with delivery; only the sending layer knows whether anything left.

### Delivery confirmation

**VTI-TRN-040** — A sender MUST NOT treat a message as delivered except on one
of the following classes of evidence, listed in descending strength:

1. **A receipt from the recipient's delivery layer**, acknowledging durable
   receipt. This is the only class that is end-to-end and does not rest on
   trusting an intermediary's durability.
2. **A protocol reply** correlated to the message.
3. **Transport evidence that the recipient collected the message**, where the
   transport provides it.

Evidence of the first or third class is further qualified by VTI-TRN-044.

**VTI-TRN-041** — A sender MUST record which class of evidence a delivery was
confirmed by. Where it declines to count evidence as delivery under
VTI-TRN-044, it MUST record the evidence and that it was not counted.

**VTI-TRN-042** — Where a stated delivery window passes without evidence, a
sender MUST NOT assume delivery. It MUST escalate: re-resolve the recipient and
attempt an alternate binding offered by the recipient's identifier document
where one exists; otherwise mark the delivery failed and surface it to the
operator.

**VTI-TRN-043** — A receiver MUST deduplicate redelivered messages by their
idempotency key, as required by VTI-OPS-061.

### Delivery and the acceptance window

A store-and-forward delivery can outlive the document it carries. A recipient
refuses a document whose time of issue lies outside its acceptance window
(VTI-OPS-024), and a message held by a mediator, refused by a hop, or escalated
under VTI-TRN-042 can reach its recipient after that window has closed. The
requirements below keep such a message from being counted as delivered, and say
how a sender delivers the operation instead.

In this section, the **latest acceptable instant** of a document at a recipient
is the earlier of: the document's time of issue plus the recipient's maximum
age and its clock-skew tolerance; and the document's expiry, where it carries
one. After that instant no recipient applying that window accepts the document,
whatever its clock reads within its tolerance.

**VTI-TRN-044** — A sender MUST NOT treat evidence of the first or third class
of VTI-TRN-040 as delivery where the message arrived after the latest
acceptable instant of the document it carries. The sender MUST place the
arrival at the time the evidence states, or, where the evidence states none, at
the time the sender received the evidence. A message so arrived MUST be treated
as undelivered, and VTI-TRN-042 continues to apply to the operation it carries.

**VTI-TRN-045** — A sender MUST determine a recipient's acceptance window from
the window the recipient advertises in an authenticated discovery response
(VTI-OPS-046), where it advertises one. Otherwise it MUST use the acceptance
window the operation's definition states, where it states one, and otherwise a
window the sender documents and applies to every recipient for which it has no
other.

**VTI-TRN-046** — A sender MUST NOT send a document after its latest
acceptable instant at the recipient, and SHOULD NOT send one after its time of
issue plus the recipient's maximum age. Where it would otherwise do so, where
VTI-TRN-044 leaves an operation undelivered, and where the recipient has refused
a document as outside its acceptance window, a sender that continues the
delivery MUST deliver the operation as a new document: a new identifier
(VTI-OPS-025), a new time of issue, a new proof by the same issuer, the
original's thread, and every other member unchanged, including the idempotency
key (VTI-OPS-064). The sender:

1. MUST NOT re-send the original under a new time of issue, or send any altered
   copy of it under the original's identifier;
2. MUST NOT issue a new document for an operation of the third class of
   VTI-OPS-060 unless the original carries an idempotency key;
3. MUST NOT issue a new document once the original's own expiry has passed, or
   once the delivery window of VTI-TRN-042 has closed; and
4. MUST bound the number of new documents one delivery can issue, and MUST mark
   the delivery failed and surface it to the operator when the bound is reached.

**VTI-TRN-047** — A node SHOULD advertise its acceptance window in its
discovery response, where the discovery definition provides for it, and MUST
NOT advertise a window longer than the one it applies.

*Rationale for VTI-TRN-044.* Collection is evidence that the recipient was
reachable, not that it accepted anything. A copy collected after its window has
closed is refused as expired, and a sender that records it as delivered has
closed the one obligation — VTI-TRN-042's — that would have delivered the
operation. Nothing downstream disagrees, because the refusal is sent to a
sender that has stopped listening. A receipt from the recipient's delivery
layer has the same defect where it acknowledges arrival below the point where
the window is applied. A protocol reply does not: it carries the recipient's
disposition, and an `expired` refusal is a refusal the sender acts on under
VTI-TRN-046, not a delivery. The bound is the latest acceptable instant rather
than the maximum age alone, so that a copy the recipient might still accept is
not recorded as lost; a copy that arrives inside the skew tolerance and is
refused comes back as a refusal and is handled by VTI-TRN-046.

*Rationale for VTI-TRN-045 and VTI-TRN-047.* A recipient's acceptance window is
its own policy, and a sender holding a document for delivery cannot apply
VTI-TRN-044 or VTI-TRN-046 without knowing it. Where the two ends happen to
share a constant, the sender is right by coincidence, and the first recipient
configured differently is one whose deliveries are either recorded as lost
while they are accepted, or recorded as delivered while they are refused. An
advertised window longer than the one applied produces the second, which is
why VTI-TRN-047 forbids it; one shorter than the one applied costs only the
signatures spent on new documents the recipient did not need.

*Rationale for VTI-TRN-046.* The obvious repair — re-stamping the time of issue
and re-signing — produces a different document under an identifier the
recipient may already have accepted, which it refuses under VTI-OPS-026 as a
conflicting document rather than absorbing as a repeat. A new identifier is what makes the
document acceptable, and the idempotency key is what keeps the new document one
operation with the old: a recipient performs a keyed operation at most once per
key (VTI-OPS-061), so however many of the documents reach it, the operation is
performed once. That is also why item 2 refuses a new document for an unkeyed
operation of the third class: for such an operation, delivering it again is
performing it again. The replay exposure is unchanged. The recipient's window
and its record of accepted identifiers stay as they were, and the new document
is one only the issuer could have signed. A copy already held by an
intermediary cannot be recalled; its recipient refuses one stale copy, and a
new document follows under VTI-TRN-044. The bound of item 4 matters where
signing is expensive or is itself an audited act, and it is what stops a
recipient that refuses every document from costing the sender a signature per
window for as long as the delivery window lasts.

*Rationale for VTI-TRN-040.* Store-and-forward transports are at-least-once
buses. Acceptance by a mediator means durably queued, which is a genuine
property and not the one the application needs: the mediator can still lose the
message, and the recipient may never collect it. The three classes are kept
distinct rather than collapsed because they close different windows, and a
sender that records only "delivered" cannot later tell which window its claim
rests on.

*Rationale for VTI-TRN-042.* A dead mediator is not a dead peer. Re-resolving
before failing is what distinguishes the two, and doing it in that order is what
prevents a transport outage from being recorded as a peer that will not answer.

### Bounded waits and honest health

**VTI-TRN-050** — Every network operation MUST be bounded by a timeout.

**VTI-TRN-051** — A failure to establish a transport MUST fail closed.

**VTI-TRN-052** — A node's determination of its own reachability MUST be
re-falsifiable. A node MUST NOT latch a reachability determination made at
start-up.

**VTI-TRN-053** — A health signal MUST reflect the node's current ability to
send, and MUST NOT report the last known good state.

*Rationale for VTI-TRN-052 and VTI-TRN-053.* A connection state decided once at
boot is a claim about a moment that has passed, and a health endpoint built on
it reports success for as long as the process survives. The failure mode is
specific and expensive: the operator's dashboard is green, the messages are not
arriving, and the signal designed to detect exactly that is the reason nobody
is looking.
